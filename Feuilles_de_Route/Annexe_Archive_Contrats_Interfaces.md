# ARCHIVE — Contrats d'interface des structures d'estimation (fichier supprimé)

> ## AVERTISSEMENT — DOCUMENT NON NORMATIF
> ## NE PAS UTILISER COMME SUPPORT DE PROGRAMMATION
>
> Copie **gelée** de l'ancien fichier `Contrats_Interfaces_Estimation.md`, supprimé le
> 2026-10-30. Son contenu est **intégralement repris** dans
> [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md) :
>
> | Ancien emplacement (ci-dessous) | Emplacement de référence actuel |
> | :--- | :--- |
> | §1 Contraintes imposées à l'Étape 1 | `Reference_Technique_Etape_1_Capteurs.md` **§7.1** |
> | §2 `FilterState` dédié | **§7.2** (et §7.2.1 à §7.2.3) |
> | §3 Projection `ControlState` | **§7.3** (et §7.3.1 à §7.3.2) |
> | §4 Variantes et capacités | **§7.4** (et §7.4.1 à §7.4.2) |
> | §5 Impacts sur l'architecture | **§7.5** |
> | §6 Squelettes de code | **Annexe C** |
>
> Ce fichier n'est conservé que pour la traçabilité des décisions et la lecture de
> l'historique. En cas de contradiction avec `Reference_Technique_Etape_1_Capteurs.md`,
> **c'est la référence qui fait foi**.

---

> Document d'origine (2026-10-29) : extrait de l'ancienne `Feuille_de_Route_Etape_1.md`
> (§7 et Annexe C). Il figeait les structures qui font l'interface entre l'acquisition
> (étape 1, paquets 1.1 à 1.3), le filtre (étape 2) et l'asservissement (étape 3). **Aucun
> de ces types n'est livré à l'Étape 1** : seules les contraintes qu'ils imposent à l'Étape 1
> sont opposables (section 1). Le chapitre §5.1 de la référence Étape 1 décrit la **mémoire
> des mesures** ; ce document décrit **l'état estimé** et la façon dont il est consommé.
> Les variantes écartées sont consignées dans
> [`Annexe_Variantes_Et_Historique_Etape_1.md`](./Annexe_Variantes_Et_Historique_Etape_1.md),
> §6 (structures d'estimation) et §7 (idées issues du code).

---

## 1. Contraintes imposées à l'Étape 1 (opposables)

1. `CapteursState` est une **mémoire d'entrée pure** : mesures, horodatage, fraîcheur,
   séquence. Elle ne contient **ni** état de filtre, **ni** covariance, **ni** champ
   conditionné par le mode.
2. Les payloads `ValsImu`, `ValsAlti`, `ValsUWB` sont des **sur-ensembles** à disposition
   fixe, avec un masque `valid` ; aucun type dérivé n'est stocké (pas de *slicing*).
3. Aucune structure n'est **conditionnée** par le mode, le type de filtre ou la présence
   d'un capteur ; la variabilité passe par des capacités (`MAX_*`), des masques et des
   `#if` sur les **fonctions**.
4. `id` et index de ligne sont **stables** (feuille §1.2.1) : un log d'Étape 1 reste
   rapprochable d'un rejeu de validation de l'étape 2.1.
5. Les types d'estimation (`FilterState`, `ControlState`, `FilterDiagnostics`) ne sont
   **pas** compilés dans les projets de test d'Étape 1 ; ils sont figés ici pour que le
   paquet 1.2 et l'étape 2.1 ne les remettent pas en cause.

---

## 2. État du filtre : `FilterState` dédié

**Décision.** Le filtre possède un **état nominal** `FilterState`, structure dédiée et
stable, et, en interne seulement, un **état d'erreur** `dx` de dimension
`FILTER_ERROR_STATE_DIM` avec sa covariance `P`. `FilterState` n'hérite pas de
`CapteursState`, n'en contient pas et n'est pas contenue par elle. Le filtre expose
`const FilterState& state()` ; `dx` et `P` restent privés. Une vue `FilterDiagnostics`
(covariance, innovation) est fournie uniquement au log `KAL` et au diagnostic DEV.

Résumé de la justification (détail des options écartées : compagnon §6.1) :
`CapteursState` est une **entrée**, `FilterState` une **sortie** ; la dimension d'erreur
dépend du modèle, donc l'encapsuler dans `CapteursState` ferait varier son `sizeof` avec
`FilterType` — violation d'ODR entre unités de compilation ; et l'Étape 1 ne doit pas
embarquer `Eigen` ni la covariance.

### 2.1 Contenu de `FilterState` (état nominal)

| Champ | Repère / unité | Renseigné par |
| :--- | :--- | :--- |
| `p_w[3]` | monde, m | tous les filtres |
| `v_w[3]` | monde, m/s | ESKF (Situations 2/3) ; invalide en cinématique minimal |
| `q_wb` | quaternion `[w,x,y,z]`, corps → monde | tous les filtres (construit depuis les angles en cinématique) |
| `omega_b[3]` | corps, rad/s | ESKF ; invalide en cinématique minimal |
| `a_b[3]` | corps, m/s² | ESKF ; invalide en cinématique minimal |
| `b_baro` | m | Situation 3 uniquement |
| `tMicros` | `uint32_t`, `micros()` | tous |
| `valid` | masque `FilterQuantity` | tous ; marque les champs réellement estimés |

`FilterState` ne contient **ni `P`, ni `dx`, ni l'innovation** : ce sont des objets
internes au filtre. Le log `KAL` reçoit une vue `FilterDiagnostics` construite à la
demande, afin que la structure publiée reste petite et copiable. La dimension nominale est
fixe (17 flottants) ; c'est la **dimension d'erreur** qui varie avec `FilterConfig`.

### 2.2 Types de filtre — alignement sur `Implementation` §1.2.1

`Implementation.pdf` §1.2.1 énumère **quatre** valeurs. Le contrat les reprend **toutes**,
y compris `INERTIEL_EULER`, qui manquait dans la version antérieure du §7 de la feuille :

```cpp
enum class FilterType {
    CINEMATIQUE_MINIMAL,   // Situation 1, n = 6
    INERTIEL_EULER,        // Situation 2, variante A, n = 15
    INERTIEL_ESKF,         // Situation 2, variante B (ESKF), n = 15
    INERTIEL_AVEC_BIAIS    // Situation 3, variante B, n = 16
};
constexpr FilterType ACTIVE_FILTER = FilterType::INERTIEL_ESKF;
```

### 2.3 Correction induite sur `config.h`

Le `KALMAN_STATE_DIM` actuel dimensionne à la fois `KalmanVector` et `KalmanMatrix` alors
que, pour l'ESKF, il désigne la dimension de **l'erreur** (`15`/`16`), pas celle de l'état
nominal (`16`/`17`). `FilterConfig.h` distinguera explicitement `FILTER_TYPE` (modèle) et
`FILTER_ERROR_STATE_DIM` (taille de `dx`/`P`) ; `FilterState` reste la représentation
nominale, non matricielle. Le nom `KALMAN_STATE_DIM` peut être conservé pour la
compatibilité, mais il est documenté comme dimension d'erreur ; le filtre cinématique à
6 états reste le cas où état nominal et erreur coïncident.

---

## 3. Asservissement : projection `ControlState`

**Décision.** La boucle d'asservissement lit **`ControlState`**, une **projection** de
`FilterState` construite à la frontière par `makeControlState(const FilterState&)`, selon
la même stratégie que la liaison radio (le consommateur reçoit une vue dédiée, pas la
mémoire de stockage). L'asservissement ne lit **ni tout `CapteursState`, ni un
sous-ensemble de celui-ci**. Si une mesure brute reste nécessaire, elle est ajoutée
**explicitement** à la projection sous un nom propre.

### 3.1 Contenu de `ControlState`

`r_w` (position), `v_w` (vitesse monde), `eul` (roulis, tangage, lacet, conventions de
`Formulations_Kalman` §2.2.1), `omega_b` (vitesses angulaires corps, SI), `tMicros` et le
masque `valid` (réutilisant `FilterQuantity`). `eul` est dérivé de `q_wb` par
`quatToEuler()` ; il n'est pas stocké en double dans `FilterState`. Lorsque le filtre
sélectionné n'estime pas une grandeur (vitesse ou vitesse angulaire en cinématique
minimal), la projection la marque invalide ; l'asservissement applique alors son propre
calcul de repli (dérivée filtrée de position, gyromètre brut explicitement copié dans la
projection), sans jamais lire `CapteursState`. Le repli est une politique de la projection,
pas une permission donnée à la commande de parcourir la mémoire des mesures.

### 3.2 Conséquences pour l'asservissement

`PositionController::update()` prend `const ControlState&` au lieu du filtre et des
valeurs IMU ; `hold()` utilise `r_w` et `eul.z()`. Les conversions d'échelle gyroscopique
et la matrice de passage IMU → corps **sortent** de l'asservissement : `omega_b` est déjà
dans le repère corps et en rad/s, produit par le filtre. Le point d'entrée construit la
projection depuis `state()`. Le contrôle devient testable sur hôte avec un `ControlState`
synthétique, sans `Wire`, sans `Eigen` de capteur et sans carte.

---

## 4. Variantes : une structure stable, des capacités variables

**Décision.** Les structures de données **ne sont pas modifiées** selon le type de filtre,
le mode, les grandeurs mesurées, la journalisation ou la présence d'un capteur. Elles ont
une **disposition fixe** ; la variabilité est portée par des **capacités**, des **masques**
et la compilation des **fonctions**.

| Facteur | La structure change ? | Mécanisme retenu | Motif |
| :--- | :--- | :--- | :--- |
| Type de filtre (`6`/`15`/`16`) | non pour `FilterState` ; oui pour `dx`/`P` | `FilterConfig.h` + `if constexpr` ; `valid` marque les champs estimés | La dimension d'erreur est intrinsèque au modèle ; l'interface du contrôle et du log reste stable |
| Mode d'exécution | non | `#if` sur les **fonctions** de journalisation, diagnostic, simulation ; jamais sur les champs | `VALIDATION` doit rester représentatif de `USAGE` ; `id`, offsets de log et rejeu identiques |
| Grandeurs mesurées | non | `Vals*` = sur-ensembles + `valid`/`havePosition` | Évite le *slicing* et les variantes par famille |
| Journalisation | non | `CAP_LOG_*` décide de l'**émission** d'une ligne ; `formatRecord()` reste l'unique producteur | Parité des puits et parseur unique des paquets 1.3 et 2.1 |
| Capteur absent ou perdu | non | cellule conservée avec `isFresh = false` (et `valid = 0`) ; `CAP_*_ACTIF` compile ou non le pilote | `MAX_*` = capacité matérielle, pas topologie installée |
| Simulation | non | `SOURCE_SIMULE` + `CapSimule<T>`, en DEV uniquement | Le rejeu produit les mêmes octets et le même état |

### 4.1 Exception unique et bornée

La dimension `FILTER_ERROR_STATE_DIM` et le type de `P` sont les **seules** grandeurs
conditionnelles, parce qu'elles sont **intrinsèques au modèle** et restent internes au
filtre. `FilterState`, `ControlState`, `CapteursState` et les payloads `Vals*` gardent la
même disposition dans tous les modes et pour tous les filtres. Le champ `b_baro` de
`FilterState` existe même en Situation 2 (4 octets) et est simplement marqué invalide :
c'est préférable à un `#if` dans une structure partagée.

### 4.2 Journalisation `KAL`

`FilterState` est la source de l'état ; `FilterDiagnostics` (covariance, innovation) est une
vue séparée, compilée seulement pour `CAP_LOG_KALMAN`/DEV. Le log `KAL` est donc une
**projection** de l'état et du diagnostic, pas une extension de `CapteursState` ni de
`FilterState`. Les colonnes `KAL` sont figées à l'étape 2.1, avec le même `formatRecord()` et
le même en-tête versionné que les autres `type` (feuille §1.3.1).

---

## 5. Impacts et corrections induites dans le reste de l'architecture

1. **`CapteursState` reste une mémoire de mesures pure.** `ValsImu` devient le sur-ensemble
   commun (champs BNO085 additifs + `valid`) ; `ValsAlti` migre dans `Capteurs/alti.h`
   (hors `#if CAP_BMP581_ACTIF`) ; `ValsUWB` reste inchangée. Le type dérivé `ValsImuBNO`
   n'est plus un type de stockage.
2. **`CapteursState` est une instance injectée**, pas un ensemble de méthodes statiques sur
   des membres non statiques. L'acquisition possède l'instance et la passe par référence au
   filtre et au journal ; l'étape 4 pourra la faire évoluer en double tampon sans changer
   l'interface.
3. **`FilterState`, `ControlState`, `FilterDiagnostics`** sont les trois types de la couche
   estimation : produit par le filtre, consommé par l'asservissement, consommé par le log
   `KAL` et le diagnostic. `FilterState` et `ControlState` sont des valeurs, testables sur
   hôte.
4. **`FilterConfig.h`** fixe `FILTER_TYPE` et `FILTER_ERROR_STATE_DIM` ; le filtre conserve
   `dx`/`P` privés et utilise `if constexpr`.
5. **`filtre_kalman.h` / `asserv_position.h` / `main.cpp`** : le filtre expose `state()` ;
   l'asservissement prend `const ControlState&` ; `main` construit la projection. La
   télémétrie reste un rôle séparé, hors du chemin de commande.
6. **`log_capteurs.h`** : `formatRecord()` reçoit `Vals*` ou
   (`FilterState`, `FilterDiagnostics`) selon le `type` ; `CAP_LOG_*` ne change que
   l'émission. Aucune structure de données n'est dupliquée pour le log.
7. **Périmètre de l'Étape 1 inchangé** : `CapteursState`, `Vals*`, `CapteursConfig.h`, les
   pilotes et les algorithmes unitaires restent livrables sans `FilterState`,
   `ControlState`, `FilterDiagnostics` ni `Eigen` de filtrage.

---

## 6. Squelettes

> Les payloads `Vals*` doivent rester **sans dépendance Arduino** pour que les tests natifs
> (§1.7 de la feuille Étape 1) compilent sur hôte ; `FilterState`/`ControlState` sont les
> seuls types à dépendre d'`Eigen`.

```cpp
// capteurs_state.h — mémoire des dernières mesures, séparée du format radio.
// Une instance par nœud, possédée par l'acquisition et injectée par référence.
// Pas d'allocation dynamique : tableaux de capacité fixe MAX_IMU / MAX_ALTI.
#pragma once
#include <cstdint>
#include "Capteurs/imu.h"
#include "Capteurs/alti.h"
#include "Capteurs/uwb.h"
#include "CapteursConfig.h"

struct SensorCell {                     // métadonnées communes à toute mesure
    uint32_t lastUpdateMicros = 0;      // horodatage de la dernière mesure valide
    uint32_t sequenceCounter  = 0;      // compteur de mesures (trous détectables)
    bool     isFresh          = false;  // mis à true par le producteur, baissé par
};                                      // le consommateur (le filtre) après lecture

class CapteursState {
public:
    // Écrit par la tâche capteurs (boucle coopérative ; double tampon à l'étape 4)
    void setImu (uint8_t i, const ValsImu& v, uint32_t tsMicros);
    void setAlti(uint8_t i, const ValsAlti& v, uint32_t tsMicros);
    void setUWB (const ValsUWB& u, uint32_t tsMicros);

    // Lecture non destructive (état courant, journal, diagnostic)
    const ValsImu&  imu (uint8_t i) const;
    const ValsAlti& alti(uint8_t i) const;
    const ValsUWB&  uwb () const;
    const SensorCell& imuCell (uint8_t i) const;
    const SensorCell& altiCell(uint8_t i) const;
    const SensorCell& uwbCell () const;

    // Lecture destructrice de la fraîcheur : un seul consommateur (le filtre).
    bool consumeImu (uint8_t i, ValsImu& out, SensorCell& cell);
    bool consumeAlti(uint8_t i, ValsAlti& out, SensorCell& cell);
    bool consumeUWB (ValsUWB& out, SensorCell& cell);

private:
    ValsImu    imus[MAX_IMU]{};
    ValsAlti   altis[MAX_ALTI]{};
    ValsUWB    uwb_{};
    SensorCell imuCells[MAX_IMU]{};
    SensorCell altiCells[MAX_ALTI]{};
    SensorCell uwbCell_{};
};

// FilterQuantities.h — masque partagé par FilterState et ControlState.
// Aucun #if : les champs existent toujours, le masque dit lesquels sont estimés.
#pragma once
#include <cstdint>
enum FilterQuantity : uint16_t {
    FQ_POSITION  = 1u << 0,
    FQ_VELOCITY  = 1u << 1,
    FQ_ATTITUDE  = 1u << 2,
    FQ_BODY_RATE = 1u << 3,
    FQ_BODY_ACC  = 1u << 4,
    FQ_BARO_BIAS = 1u << 5
};

// FilterState.h — état nominal du filtre, distinct de CapteursState.
// Convention quaternion [w, x, y, z] ; p_w/v_w en repère monde,
// omega_b/a_b en repère corps, b_baro en mètres. Les champs non estimés
// sont marqués invalides dans `valid` (aucun #if dans les consommateurs).
// P et dx ne figurent pas ici : ils restent internes au filtre.
#pragma once
#include <cstdint>
#include <Eigen/Geometry>
#include "FilterQuantities.h"

struct FilterState {
    uint32_t            tMicros = 0;
    Eigen::Vector3f     p_w     = Eigen::Vector3f::Zero();
    Eigen::Vector3f     v_w     = Eigen::Vector3f::Zero();
    Eigen::Quaternionf  q_wb    = Eigen::Quaternionf::Identity();
    Eigen::Vector3f     omega_b = Eigen::Vector3f::Zero();
    Eigen::Vector3f     a_b     = Eigen::Vector3f::Zero();
    float               b_baro  = 0.0f;
    uint16_t            valid   = 0;

    bool has(FilterQuantity q) const { return (valid & q) != 0; }
    EIGEN_MAKE_ALIGNED_OPERATOR_NEW
};

// ControlState.h — projection de FilterState pour l'asservissement.
// Aucune référence à CapteursState ni au filtre : le contrôle ne dépend
// que de cette vue, testable sur hôte.
#pragma once
#include <cstdint>
#include <Eigen/Geometry>
#include "FilterQuantities.h"

struct ControlState {
    uint32_t        tMicros = 0;
    Eigen::Vector3d r_w     = Eigen::Vector3d::Zero();
    Eigen::Vector3d v_w     = Eigen::Vector3d::Zero();
    Eigen::Vector3d eul     = Eigen::Vector3d::Zero();   // roulis, tangage, lacet
    Eigen::Vector3d omega_b = Eigen::Vector3d::Zero();   // rad/s, repère corps
    uint16_t        valid   = 0;
};

// ControlStateAdapter.h — seul point qui connaît à la fois FilterState et
// ControlState ; convertit float -> double et q -> eul.
ControlState makeControlState(const FilterState& s);

// FilterDiagnostics.h — vue de diagnostic (log KAL, DEV), jamais lue par
// l'asservissement. Compilée seulement pour CAP_LOG_KALMAN / DEV ; les
// pointeurs désignent les objets internes du filtre, le temps de formater
// une ligne.
#pragma once
#include <cstdint>
#include "FilterConfig.h"

struct FilterDiagnostics {
    const float* P          = nullptr;  // FILTER_ERROR_STATE_DIM², layout Eigen
    const float* innovation = nullptr;  // FILTER_ERROR_STATE_DIM
    uint8_t      n          = 0;        // = FILTER_ERROR_STATE_DIM
};
```
