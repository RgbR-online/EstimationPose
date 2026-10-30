# Référentiel technique global — étapes et paquets

> **Rôle du document.** Référentiel technique **unique** du développement des capteurs, du
> filtre de Kalman et de leur intégration dans le dirigeable autonome. Il décrit le contenu
> technique de chaque étape et de chaque paquet, **pointe vers les documents détaillés**
> des étapes (§2.1) et fixe **l'organisation du dépôt `FromTo Github`** (§10). Il ne
> définit **ni identifiant, ni jalon, ni règle de progression** :
> ceux-ci appartiennent à
> [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md).
> L'état d'avancement et l'ordre de lecture sont dans
> [`Suivi_Avancement.md`](./Suivi_Avancement.md).

Les spécifications théoriques de référence sont [`Formulations_Kalman.pdf`](../../Formulations_Kalman/Formulations_Kalman.pdf) et les contraintes embarquées sont décrites dans [`Implementation.pdf`](../../Implementation/Implementation.pdf).

---

## 1. Objectifs principaux

1. Intégrer et valider les capteurs : centrale inertielle **Adafruit BNO085**, baromètre **BMP581**, et conserver la compatibilité avec les capteurs historiques **CMPS12** et **UWB**.
2. Faire évoluer le filtre de Kalman du modèle cinématique minimal (**Situation 1**) vers le modèle inertiel à quaternions d'erreur (**Situations 2 et 3 — ESKF**).
3. Optimiser l'architecture temporelle sur Teensy 4.1 (Cortex-M7 à 600 MHz) en exploitant le cadencement matériel et les interruptions, sans introduire de RTOS lourd.
4. Assurer une intégration progressive, testable et sans régression dans le dépôt [`FromTo Github`](../FromTo%20Github).

---

## 2. Référentiel documentaire et cohérence

### 2.1 Documents techniques par étape

Chaque étape disposera de son document de référence détaillé. Ce référentiel global en
donne le contenu technique de haut niveau et **renvoie** au détail ; il n'en duplique pas le
contenu.

| Étape | Document de référence détaillé | Statut | Contenu couvert |
|---|---|---|---|
| `1` | [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md) | **Rédigé** | Paquets `1.1` à `1.3`, configuration `CapteursConfig.h`, capteurs, contrats d'interface (§7), annexes de squelettes (A, B, C) |
| `2` | `Reference_Technique_Etape_2_Kalman.md` | *À produire au démarrage du paquet `2.1`* | Noyau du filtre, `FilterConfig`, types de filtre, banc hôte et banc autonome sur cible |
| `3` | `Reference_Technique_Etape_3_Integration.md` | *À produire au démarrage du paquet `3.1`* | Intégration PlatformIO, câblage acquisition → filtre → asservissement, tests sur cible |
| `4` | `Reference_Technique_Etape_4_Temps_Reel.md` | *À produire au démarrage du paquet `4.1`* | Ordonnancement, horodatage, priorités |
| `5` | `Reference_Technique_Etape_5_Profilage.md` | *À produire au démarrage du paquet `5.1`* | Budgets temps, mémoire, latence |
| `6` | `Reference_Technique_Etape_6_Validation.md` | *À produire au démarrage du paquet `6.1`* | Validation système, modes dégradés, documentation finale |

#### 2.1.1 Checklist de création d'un document d'étape

Le premier paquet de chaque étape non couverte (`2.1`, `3.1`, `4.1`, `5.1`, `6.1`) porte, en
plus de son contenu technique, la **rédaction du document de référence détaillé de l'étape**.
L'agent qui le porte :

1. rédige le document de référence détaillé de l'étape (`Reference_Technique_Etape_<n>_*.md`)
   avec l'en-tête normalisé (§2.2, règle 5) et sa table d'ordre de lecture ;
2. crée l'annexe non normative `Annexe_Variantes_Et_Historique_Etape_<n>.md` ;
3. réduit la section d'étape correspondante du présent référentiel à l'objectif et au
   renvoi (§2.2, règle 7), en transférant les blocs « Organisation du dépôt » des paquets
   de l'étape dans le document de référence détaillé ;
4. porte le statut **Rédigé** au tableau ci-dessus et inscrit le nouveau document dans la
   carte des documents de [`Suivi_Avancement.md`](./Suivi_Avancement.md) §3 ;
5. consigne la création dans la note de passation du point de reprise.

La **vue d'ensemble du séquencement** — étapes, paquets, points de reprise, jalons — n'est
pas reproduite ici : elle figure dans
[`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §3
(diagramme) et §5 (jalons), qui en sont la **source unique**.

### 2.2 Règles de cohérence entre documents

1. **Préséance.** Identifiants, jalons et règles de progression :
   `Planification_Etapes_Paquets_Jalons.md`. Contenu technique, périmètres, livrables et
   critères de paquet, organisation du dépôt `FromTo Github` (§10) : le présent
   référentiel. Détail d'implémentation : le document de référence de l'étape concernée.
2. **Identifiants invariants.** Un document d'étape ne crée aucun identifiant nouveau : il
   reprend ceux de la planification (paquets `<étape>.<numéro>`, points de reprise
   `C<étape>.<numéro>`, jalons `J1` à `J6`).
3. **Critères de sortie d'étape.** Les critères d'étape de la planification sont une
   **synthèse** des critères de paquet définis ici ; en cas d'écart, le critère de paquet
   fait foi.
4. **Nommage.** Le nom d'un livrable et le chemin d'un fichier produit sont identiques dans
   ce référentiel, dans le document d'étape et dans la note de passation.
5. **En-tête normalisé.** Chaque document d'étape s'ouvre par son rôle, son lien vers ce
   référentiel, le ou les paquets couverts et le rappel de l'interdiction de redéfinir un
   identifiant.
6. **Annexes non normatives.** Les documents `Annexe_*` (variantes, historique, archives)
   ne sont jamais un support de programmation et ne peuvent pas contredire un document
   technique.
7. **Réduction à la création d'un document d'étape.** Dès que
   `Reference_Technique_Etape_<n>_*.md` existe, la section d'étape correspondante du présent
   référentiel est réduite à l'objectif et au renvoi vers ce document — y compris les blocs
   « Organisation du dépôt » des paquets concernés, transférés au document d'étape : le
   détail technique d'une étape n'existe qu'à un seul endroit.

En cas d'écart détecté entre deux documents : corriger le **document de détail** ou ce
référentiel, jamais la planification, et consigner la correction dans la note de passation.

---

## 3. Étape 1 — Capteurs en isolation

**Objectif :** valider chaque capteur, puis l'acquisition multi-capteurs, sans dépendance au firmware.

**Détail d'implémentation :** [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md)
(configuration, capteurs, contrats d'interface §7, annexes A à C). En cas de divergence sur
le détail, ce document d'étape fait foi.

<a id="paquet-1-1"></a>
### Paquet 1.1 — Validation indépendante par capteur

**Périmètre :**

- valider le bus de communication I2C à 400 kHz ;
- configurer et lire le BNO085 ;
- configurer et lire le BMP581 ;
- consolider les classes historiques CMPS12 et UWB ;
- vérifier les cas d'erreur : capteur absent, mauvaise adresse, mauvais bus, timeout, absence de réponse.

**Entrées :** documents d'ingénierie (`Materiel.pdf`, `Implementation.pdf`,
`Formulations_Kalman.pdf`, fiches de `Materiel/Datasheets/`, schéma `Elec/Schéma
électrique capteurs.pdf`) ; sketches de `NvCapteurs/` et classes historiques du dépôt,
comme **précédents non normatifs** ; bus I2C et câblage réels.

**Hors périmètre :** toute modification du firmware ; toute broche d'interruption (règle
normative du document d'étape §0 : polling non bloquant) ; l'acquisition multi-capteurs
(paquet `1.2`) ; la caractérisation métrologique et les jeux de données (paquet `1.3`) ;
les capteurs hors périmètre (GPS PA1010D, ultrasons) ; la livraison des types d'estimation
(figés au §7 du document d'étape, non compilés dans les projets de test).

**Travaux attendus :**

- **BNO085 :**
  - extraire les rapports SH-2 natifs : *Rotation Vector*, *Accelerometer*, *Calibrated Gyroscope* et *Linear Acceleration* ;
  - vérifier la fréquence réelle de publication ;
  - vérifier l'absence de blocage de bus ;
  - mesurer les bruits et la stabilité des biais.
- **BMP581 :**
  - configurer la mesure altimétrique ;
  - évaluer le filtrage IIR interne et le sur-échantillonnage ;
  - déterminer la fréquence réelle pour l'ODR retenue ;
  - mesurer le bruit et la stabilité d'altitude relative.
- **CMPS12 et UWB :**
  - consolider le code fonctionnel existant ;
  - conserver une structure de classe compatible avec l'acquisition multi-capteurs.

**Livrables :**

- un projet PlatformIO autonome par capteur ;
- des tests unitaires des fonctions pures ;
- un banc prêt à exécuter ;
- des algorithmes de décodage, de conversion et de configuration ;
- les classes capteurs associées ;
- le fichier de configuration partagé `CapteursConfig.h`, source unique de vérité ;
- la structure de mémorisation `CapteursState` (document d'étape, Annexe C) ;
- le contrat du tampon d'émission et de la clôture de session, vérifié sur un puits
  simulé (aucun matériel SD requis) ;
- les contraintes d'interface des structures d'estimation, figées au §7 du document
  d'étape (les types `FilterState`/`ControlState`/`FilterDiagnostics` ne sont pas livrés).

**Critères de sortie :**

- chaque banc se construit et s'exécute indépendamment ;
- chaque banc affiche des valeurs plausibles ou des erreurs explicites ;
- les fonctions pures sont couvertes par des tests ;
- aucun code du firmware n'est copié dans les bancs ;
- le firmware existant n'est pas modifié ;
- `CapteursConfig.h` compile avec ses macros réellement surchargées par `build_flags` ;
- le contrat du tampon et de la clôture est vérifié sur un puits simulé bloquant, avec le
  `static_assert` de dimensionnement au débit pire ;
- les types d'estimation (`FilterState`, `ControlState`, `FilterDiagnostics`) ne sont
  compilés dans aucun projet de test.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| `lib/sensors/` : squelette, `library.json`, interfaces, pilotes BNO085 et BMP581 | `README.md` si les instructions de banc y sont ajoutées | `src/`, `include/`, `main/` du firmware |
| `bench/test_bno085/` avec son `platformio.ini` | `.gitignore` si nécessaire | `platformio.ini` racine, sauf exclusion explicite de `bench/` |
| `bench/test_bmp581/` avec son `platformio.ini` | | `commun/` |
| Tests des fonctions pures dans `lib/sensors/test/` | | |
| `lib/sensors/include/CapteursConfig.h` comme source unique de configuration | | |

**Point de reprise :** `C1.1`  
**Jalon associé :** `J1`

<a id="paquet-1-2"></a>
### Paquet 1.2 — Acquisition multi-capteurs

**Périmètre :**

- lire simultanément le BNO085 et le BMP581 sur le bus I2C partagé ;
- centraliser la configuration dans `CapteursConfig.h` ;
- garantir un horodatage cohérent entre capteurs ;
- vérifier l'arbitrage du bus en lecture concurrente ;
- préparer l'intégration de CMPS12 et UWB dans le même schéma ;
- utiliser une simulation de certains capteurs si nécessaire.

**Entrées :** bancs, classes et `CapteursConfig.h` validés au paquet `1.1` (configuration
de référence, tables d'instances) ; machine de phases, contrat de trame canonique et
contrat du tampon d'émission démontrés en `1.1` (document d'étape §1.3, §5.2) ; bus I2C
partagé et câblage réels ; port télémétrie sol `Serial3` (`Materiel` §5.4) pour les
commandes de session (`CMD_START`, `CMD_LOG_STOP`) ; documents d'ingénierie pour les
ports et les bus (`Materiel` §5.4, `Implementation` §5.1).

**Hors périmètre :** la caractérisation métrologique et les jeux de données (paquet
`1.3`) ; toute broche d'interruption (règle §0 du document d'étape : polling non
bloquant) ; toute modification du firmware ; le filtre de Kalman et les types
d'estimation (étape 2) ; l'intégration au PlatformIO global (paquet `3.1`) ; capteurs
hors périmètre (GPS PA1010D, ultrasons).

**Livrables :**

- un projet PlatformIO multi-capteurs autonome ;
- le fichier de configuration partagé ;
- des trames brutes horodatées et cohérentes ;
- un banc prêt à exécuter avec ou sans capteurs simulés.

**Critères de sortie :**

- le banc lit toutes les instances prévues ;
- les horodatages sont cohérents ;
- l'arbitrage du bus est vérifié ;
- aucune duplication de pilote entre les bancs ;
- le firmware n'est pas modifié.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| `bench/test_all/` avec son `platformio.ini` | `lib/sensors/` pour la coordination des instances | `src/`, `include/`, `main/` du firmware |
| Structures de mémorisation des mesures, si nécessaire | `CapteursConfig.h` pour les instances actives | `platformio.ini` racine |
| Tests de cohérence des tables et des conversions | | `commun/` |

**Point de reprise :** `C1.2`

<a id="paquet-1-3"></a>
### Paquet 1.3 — Caractérisation métrologique et jeux de données

**Périmètre :**

- mesurer les bruits, biais, dérives et influences thermiques ;
- mesurer la cadence réelle des capteurs ;
- définir le format de log, les métadonnées et le parseur ;
- produire des jeux de données réels et des jeux de validation ;
- documenter les conditions d'acquisition.

**Entrées :** bancs et acquisition multi-capteurs validés par les paquets `1.1` et `1.2` ;
trame canonique, en-tête versionné et métadonnées `#` (document d'étape §1.3.1) ; logs
produits par ces paquets ; protocoles de mesure de `Formulations_Kalman` §1.3.5 (synthèse
métrologique) et §1.3.6 (protocole 10 000 points).

**Hors périmètre :** toute modification des pilotes ou de la configuration (corrections
justifiées seulement) ; le réglage du filtre et l'exploitation des valeurs par le filtre
(étape 2) ; les jeux de données volumineux dans le dépôt (planification §7, règle 7) ;
toute broche d'interruption ; toute modification du firmware.

**Livrables :**

- un rapport de caractérisation métrologique ;
- un format de données stable et versionné ;
- des jeux de données réels ;
- des jeux de données de validation ;
- un parseur unique ;
- les valeurs de bruit et de dérive exploitables par le filtre.

**Critères de sortie :**

- les bruits et les défauts sont documentés ;
- les jeux de données sont reproductibles et traçables ;
- le format de données est stable ;
- les jeux volumineux restent hors du dépôt ;
- les valeurs sont prêtes à alimenter le réglage du filtre.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de caractérisation métrologique | `lib/sensors/` pour les conversions et les métadonnées | `src/`, `include/`, `main/` du firmware |
| Format de log et parseur unique | `.gitignore` pour les jeux volumineux | `platformio.ini` racine |
| Jeux de données réels et jeux de validation | `README.md` pour la procédure d'acquisition | `commun/` |
| Petits jeux de test dans `test/fixtures/` si nécessaire | | |

**Point de reprise :** `C1.3`  
**Jalon associé :** `J2`

---

## 4. Étape 2 — Kalman en isolation

**Objectif :** valider le filtre comme composant autonome, sans firmware : validation d'abord sur hôte, puis sur cible via un banc PlatformIO autonome, sans intégration au dépôt global.

**Détail d'implémentation :** `Reference_Technique_Etape_2_Kalman.md` *(à produire au
démarrage du paquet `2.1`)*.

<a id="paquet-2-1"></a>
### Paquet 2.1 — Validation indépendante du filtre

**Périmètre :**

- implémenter ou consolider le noyau du filtre ;
- définir `FilterConfig`, `FilterState`, `ControlState` et `FilterDiagnostics` ;
- sélectionner statiquement le type de filtre à la compilation ;
- tester les conversions, la prédiction et les corrections ;
- vérifier la convergence, la covariance et les invariants numériques ;
- rejouer des jeux de données réels ;
- simuler certains capteurs ;
- vérifier la portabilité de compilation sur cible.

**Entrées :** jeux de données réels et protocoles produits par le paquet `1.3` ;
`Formulations_Kalman.pdf` (Situations 1 à 3) ; contraintes d'interface figées par
l'étape 1 (§7 de son document d'étape) ; Eigen (côté filtre).

**Hors périmètre :** toute dépendance d'`Arduino.h`, du firmware ou d'un pilote matériel
dans `lib/kalman` ; l'exécution sur cible via banc autonome (paquet `2.2`) ; l'intégration
au firmware (paquet `3.1`) ; la modification des contrats `CapteursState`/`Vals*` et des
types d'estimation figés.

**Types de filtre à couvrir :**

- `CINEMATIQUE_MINIMAL` — Situation 1, $n = 6$, Euler ;
- `INERTIEL_EULER` — Situation 2, variante A, $n = 15$ ;
- `INERTIEL_ESKF` — Situation 2, variante B, $n = 15$ ;
- `INERTIEL_AVEC_BIAIS` — Situation 3, variante B, $n = 16$, avec biais barométrique.

**Travaux attendus :**

- fonctions modulaires `predict(dt)`, `updateUWB()`, `updateBaro()` et `updateIMU()` ;
- tests unitaires des fonctions pures ;
- scénarios analytiques et synthétiques ;
- rejeu de jeux de données ;
- simulation de capteurs ;
- vérification de la symétrie et de la positivité de la covariance ;
- vérification de la norme du quaternion ;
- absence de `NaN` et de `Inf` ;
- comparaison à une référence ou à des tolérances ;
- compilation de portabilité sur `teensy41`.

**Livrables :**

- un projet PlatformIO indépendant, principalement natif ;
- une bibliothèque de filtre sans dépendance Arduino ni firmware ;
- des tests unitaires et des scénarios de validation ;
- des références hôte et leurs tolérances, entrée du paquet `2.2` ;
- un rapport de validation ;
- une API publique documentée ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_2_Kalman.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- le filtre se compile et s'exécute sur hôte ;
- le filtre se compile pour `teensy41` (portabilité, sans exécution sur cible) ;
- les tests unitaires et les scénarios de validation passent ;
- les invariants numériques sont vérifiés ;
- les jeux de données et les simulations sont rejouables ;
- l'API publique est stable et documentée ;
- le filtre ne dépend ni d'Arduino, ni du firmware, ni d'un pilote matériel.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| `lib/kalman/` : noyau pur, `FilterConfig`, types d'état, API de prédiction et de correction | `README.md` pour la procédure de rejeu hôte | `src/`, `include/`, `main/` du firmware |
| `bench/test_kalman/` : `platformio.ini` avec environnement natif (hôte) et environnement cible en **compilation seule** (portabilité, sans exécution) ; programme de rejeu piloté par le `tMicros` des jeux de données `1.3` | `.gitignore` pour les jeux volumineux | `platformio.ini` racine |
| `bench/test_kalman/README.md` : procédure de rejeu hôte | | |
| `bench/test_kalman/reference/` : références hôte (format et précision figés) et tolérances, entrée du paquet `2.2` | | |
| Tests unitaires et scénarios de validation dans `lib/kalman/test/` | | `commun/` |
| Rapport de validation du filtre | | |

**Point de reprise :** `C2.1`

> Le jalon `J3` n'est évalué **qu'après `C2.2`**, pas après `C2.1`
> ([`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §5).

<a id="paquet-2-2"></a>
### Paquet 2.2 — Validation matérielle du filtre sur banc autonome

**Périmètre :**

- exécuter la bibliothèque du filtre sur la cible via un projet PlatformIO autonome minimal ;
- rejouer de façon déterministe les jeux de données de `1.3` sur la cible ;
- comparer les résultats sur cible aux références hôte produites en `2.1` ;
- revérifier les invariants numériques sur la cible ;
- importer la bibliothèque `lib/kalman/` sans copie.

**Entrées :** bibliothèque `lib/kalman` validée sur hôte en `2.1` (références hôte) ; jeux
de données du paquet `1.3` ; projet PlatformIO autonome minimal.

**Hors périmètre :** copie du code de la bibliothèque dans le banc ; intégration au
firmware ; capteurs réels et modes dégradés (relèvent de `3.3`) ; modification de l'API
publique du filtre.

**Livrables :**

- un projet PlatformIO autonome du filtre pour la cible ;
- des scripts de rejeu déterministe sur cible ;
- une comparaison hôte/cible documentée, écarts explicités ;
- un rapport de validation matérielle.

**Critères de sortie :**

- le projet autonome se construit pour la cible ;
- le rejeu déterministe s'exécute sur la cible ;
- les résultats sont conformes aux références hôte de `2.1` ;
- les invariants numériques sont vérifiés sur cible ;
- aucun code de la bibliothèque n'est dupliqué dans le banc ;
- la validation n'utilise que des données rejouées : capteurs réels, modes dégradés et intégration au firmware restent dans `3.3`.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Environnement cible **exécutable** de `bench/test_kalman/` (même source, sélection par macro) | `bench/test_kalman/platformio.ini` : ajout de l'environnement cible exécutable | `lib/kalman/` : source unique, aucune copie |
| `bench/test_kalman/scripts/` : comparaison hôte/cible aux `bench/test_kalman/reference/` avec les tolérances de `2.1` | `bench/test_kalman/README.md` : section rejeu sur cible et interprétation des écarts | `src/`, `include/`, `main/` du firmware |
| Rapport de validation matérielle (comparaison hôte/cible) | | `src/`, `include/`, `main/` du firmware |
| | | `platformio.ini` racine, `commun/` |

**Point de reprise :** `C2.2`  
**Jalon associé :** `J3`

---

## 5. Étape 3 — Intégration et validation sur cible

**Objectif :** intégrer les bibliothèques dans le PlatformIO global et valider le comportement sur Teensy. De même que `3.2` reprend l'acquisition de `1.2` dans le firmware, `3.3` reprend la validation de `2.2` dans le firmware intégré.

**Détail d'implémentation :** `Reference_Technique_Etape_3_Integration.md` *(à produire au
démarrage du paquet `3.1`)*.

<a id="paquet-3-1"></a>
### Paquet 3.1 — Intégration dans le PlatformIO global

**Périmètre :**

- intégrer les bibliothèques capteurs et Kalman dans le projet PlatformIO global ;
- brancher l'acquisition, le filtre et l'asservissement ;
- aligner la configuration partagée ;
- vérifier les dépendances et les exclusions de compilation ;
- supprimer toute duplication de code.

**Entrées :** bibliothèques validées en isolation (paquets `1.x` et `2.x`) ; `platformio.ini`
du firmware et arborescence du dépôt ; `CapteursConfig.h` partagé.

**Hors périmètre :** duplication de code des bibliothèques dans `src/` ou `include/` ;
modification de l'API publique des bibliothèques (sauf décision documentée) ; les tests
sur cible (paquets `3.2` et `3.3`).

**Livrables :**

- bibliothèques intégrées dans `FromTo Github` ;
- configuration firmware alignée ;
- builds de tous les environnements ;
- câblage acquisition → filtre → asservissement ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_3_Integration.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- tous les environnements firmware se compilent ;
- les bibliothèques sont utilisées sans copie ;
- les bancs et les tests natifs continuent de fonctionner ;
- les dépendances sont déclarées.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Adaptateurs dans `src/Capteurs/` et `include/Capteurs/` si nécessaire | `main/dirigeable/main.cpp` pour le câblage acquisition → filtre → asservissement | `commun/` |
| Configuration firmware du filtre, si elle diffère de `FilterConfig` | `src/Asservissement/`, `include/Asservissement/` pour consommer `ControlState` | Les bancs restent autonomes |
| Environnements PlatformIO nécessaires dans `platformio.ini` | `platformio.ini` pour les dépendances et les exclusions de `bench/` | Les pilotes de `lib/sensors/` |
| | `include/config.h` si des constantes doivent être alignées | |

**Point de reprise :** `C3.1`

<a id="paquet-3-2"></a>
### Paquet 3.2 — Tests multi-capteurs sur cible

**Périmètre :**

- exécuter l'acquisition multi-capteurs sur Teensy ;
- vérifier l'horodatage ;
- vérifier l'absence de blocage de bus ;
- vérifier la non-régression des capteurs historiques ;
- vérifier les modes dégradés.

**Entrées :** intégration validée par le paquet `3.1` ; banc Teensy et trame canonique ;
références d'acquisition des paquets `1.2`/`1.3`.

**Hors périmètre :** modification des pilotes (sauf correction justifiée) ; le filtre
(paquet `3.3`) ; l'ordonnancement temps réel (paquet `4.1`).

**Livrables :**

- résultats de tests sur cible ;
- journal des acquisitions ;
- rapport de non-régression.

**Critères de sortie :**

- l'acquisition fonctionne sur cible ;
- les cadences réelles sont conformes ;
- aucun blocage de bus n'est observé ;
- les capteurs historiques fonctionnent toujours.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de tests sur cible | `bench/test_all/` si les scénarios de banc évoluent | `lib/kalman/` |
| Journal des acquisitions | `README.md` pour les procédures de test | `lib/sensors/`, sauf correction justifiée |
| | | `commun/` |

**Point de reprise :** `C3.2`

<a id="paquet-3-3"></a>
### Paquet 3.3 — Tests d'intégration Kalman sur cible

**Périmètre :**

- reprendre les scénarios et les références de `2.2` avec le filtre intégré au firmware ;
- rejouer des données déterministes sur cible ;
- exécuter le filtre avec les capteurs réels ;
- vérifier la convergence et la stabilité ;
- tester les modes dégradés : perte de capteur, mesure aberrante, redémarrage ;
- mesurer le temps et la mémoire ;
- vérifier la non-régression.

**Entrées :** intégration du paquet `3.1` ; acquisition multi-capteurs validée par le
paquet `3.2` ; scénarios et références hôte/cible du paquet `2.2` ; jeux de données du
paquet `1.3`.

**Hors périmètre :** modification de l'API du filtre ou des pilotes (stables) ;
l'optimisation temps réel (paquet `4.1`) ; la validation système (paquet `6.1`).

**Livrables :**

- résultats de rejeu sur cible ;
- résultats avec capteurs réels ;
- rapport de temps et de mémoire ;
- rapport de non-régression ;
- documentation des modes dégradés.

**Critères de sortie :**

- le filtre fonctionne sur cible avec des données rejouées ;
- le filtre fonctionne avec les capteurs réels ;
- les modes dégradés sont testés ;
- le temps et la mémoire sont mesurés ;
- aucune duplication de pilote ou de filtre n'existe dans le firmware.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de rejeu sur cible | `main/dirigeable/`, `src/`, `include/` pour les modes dégradés | `lib/kalman/` : API stable |
| Rapport de temps et de mémoire | `platformio.ini` pour les environnements de test | `lib/sensors/` : pilotes stables |
| Documentation des modes dégradés | | `commun/` |

**Point de reprise :** `C3.3`  
**Jalon associé :** `J4`

---

## 6. Étape 4 — Optimisation temps réel

**Objectif :** améliorer l'ordonnancement et l'horodatage sans modifier l'API des bibliothèques.

**Détail d'implémentation :** `Reference_Technique_Etape_4_Temps_Reel.md` *(à produire au
démarrage du paquet `4.1`)*.

<a id="paquet-4-1"></a>
### Paquet 4.1 — Ordonnancement et horodatage

**Périmètre :**

- remplacer les temporisations logicielles flottantes par un cadencement déterministe ;
- mettre en place le cadencement haute priorité pour la prédiction et la consigne ;
- utiliser un horodatage à haute résolution ;
- déclencher les corrections sur arrivée d'échantillon ;
- déporter la télémétrie et la journalisation en basse priorité ;
- conserver le mode sûr et la clôture de session ;
- mesurer la gigue et la latence.

**Entrées :** firmware intégré et validé sur cible (paquet `3.3`) ; machine de session,
clôture et mécanismes de sûreté (`Implementation` §6) ; budgets de gigue et de latence
cibles.

**Hors périmètre :** modification de l'API des bibliothèques ; toute régression
fonctionnelle ; les nouvelles fonctions de filtrage.

**Livrables :**

- ordonnancement temps réel ;
- horodatage déterministe ;
- journalisation et clôture de session ;
- mesures de gigue et de latence ;
- documentation de l'ordonnancement ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_4_Temps_Reel.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- le comportement fonctionnel est inchangé ;
- la gigue et la latence sont mesurées et documentées ;
- l'API des bibliothèques reste stable ;
- aucune régression n'est introduite.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Instrumentation de mesure des temps, si elle n'existe pas déjà | `main/dirigeable/`, `src/`, `include/` pour l'ordonnancement | `lib/kalman/` : API stable |
| Documentation de l'ordonnancement | `platformio.ini` pour les environnements de mesure | `lib/sensors/` : pilotes stables |
| | | `commun/` |

**Point de reprise :** `C4.1`

---

## 7. Étape 5 — Profilage

**Objectif :** mesurer et documenter le budget temps, la mémoire et la latence.

**Détail d'implémentation :** `Reference_Technique_Etape_5_Profilage.md` *(à produire au
démarrage du paquet `5.1`)*.

<a id="paquet-5-1"></a>
### Paquet 5.1 — Profilage CPU, mémoire et latence

**Périmètre :**

- mesurer le budget temps de chaque phase : prédiction, correction UWB, correction baro ;
- mesurer la charge CPU totale ;
- mesurer la mémoire utilisée ;
- mesurer la latence et la gigue ;
- comparer aux budgets ;
- documenter les limites.

**Entrées :** firmware intégré et optimisé (paquet `4.1`) ; environnement de profilage
(`bench/banc_cpu`) ; budgets temps, mémoire et latence de
`Implementation.pdf`.

**Hors périmètre :** modification structurelle des bibliothèques (corrections justifiées
seulement) ; l'optimisation temps réel elle-même (paquet `4.1`) ; la validation système
(paquet `6.1`).

**Livrables :**

- rapport de profilage ;
- résultats reproductibles ;
- comparaison aux budgets ;
- limites connues ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_5_Profilage.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- la charge CPU est mesurée ;
- la mémoire et la latence sont documentées ;
- les résultats sont reproductibles ;
- les limites sont explicitées.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Résultats de profilage documentés | `bench/banc_cpu/` | `lib/kalman/` et `lib/sensors/` : uniquement des corrections justifiées |
| | `platformio.ini` pour l'environnement de profilage | `commun/` |
| | `README.md` pour les résultats et les limites | |

**Point de reprise :** `C5.1`  
**Jalon associé :** `J5`

---

## 8. Étape 6 — Validation système et clôture

**Objectif :** valider le système complet sur banc et, si possible, en vol.

**Détail d'implémentation :** `Reference_Technique_Etape_6_Validation.md` *(à produire au
démarrage du paquet `6.1`)*.

<a id="paquet-6-1"></a>
### Paquet 6.1 — Validation système

**Périmètre :**

- valider le système complet sur banc ;
- valider en vol si les conditions le permettent ;
- vérifier les modes dégradés et de sécurité ;
- analyser les données ;
- vérifier les critères d'acceptation ;
- produire la documentation finale ;
- consigner les limites et les travaux futurs.

**Entrées :** système complet sur banc ; résultats des paquets `3.3`, `4.1` et `5.1` ;
critères d'acceptation système ; documentation des modes dégradés.

**Hors périmètre :** modification des bibliothèques (sauf correction justifiée) ;
développement de nouvelles fonctions ; campagnes de données volumineuses dans le dépôt.

**Livrables :**

- rapport de validation système ;
- résultats de banc et de vol ;
- analyse des modes dégradés ;
- documentation finale ;
- liste des limites et des travaux futurs ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_6_Validation.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- les critères d'acceptation système sont vérifiés ;
- les modes dégradés et de sécurité sont validés ;
- les résultats sont documentés ;
- les limites et les travaux futurs sont consignés.

**Organisation du dépôt :**

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de validation système | `README.md` pour les résultats, les limites et les travaux futurs | Les bibliothèques, sauf correction justifiée |
| Documentation des modes dégradés et de sécurité | `platformio.ini` si les environnements de validation évoluent | `commun/` |
| Liste des limites et des travaux futurs | | |

**Point de reprise :** `C6.1`  
**Jalon associé :** `J6`

---

## 9. Matrice récapitulative des configurations

> **Vue dérivée.** Cette matrice résume les paquets définis ci-dessus ; elle ne crée aucun
> identifiant et ne remplace ni les sections de paquet ni la planification.

| Étape et paquet | Capteur IMU | Altimètre | État Kalman ($n$) | Formalisme | Mode d'exécution |
|---|---|---|---|---|---|
| **1.1** | BNO085, CMPS12 | BMP581 | — | Logs bruts | Banc autonome |
| **1.2** | BNO085, CMPS12 | BMP581 | — | Logs bruts | Banc multi-capteurs |
| **1.3** | BNO085, CMPS12 | BMP581 | — | Logs bruts | Banc et PC |
| **2.1** | BNO085, CMPS12 | BMP581 | 6, 15 ou 16 | Cinématique, Euler, ESKF | Natif et compilation cible |
| **2.2** | BNO085, CMPS12 | BMP581 | 6, 15 ou 16 | Cinématique, Euler, ESKF | Banc autonome sur cible |
| **3.1** | BNO085, CMPS12 | BMP581 | 15 ou 16 | ESKF | Firmware |
| **3.2** | BNO085, CMPS12 | BMP581 | — | Acquisition | Cible |
| **3.3** | BNO085, CMPS12 | BMP581 | 15 ou 16 | ESKF | Cible |
| **4.1** | BNO085, CMPS12 | BMP581 | 15 ou 16 | ESKF | Temps réel |
| **5.1** | BNO085, CMPS12 | BMP581 | 15 ou 16 | ESKF | Profilage |
| **6.1** | BNO085, CMPS12 | BMP581 | 15 ou 16 | ESKF | Système complet |

---

## 10. Organisation du dépôt `FromTo Github`

> **Rôle de la section.** Cette section fixe l'organisation du dépôt
> [`FromTo Github`](../FromTo%20Github) : décisions d'organisation, arborescence cible,
> rôles des dossiers, règles de dépendance, synthèse des changements et points de
> vigilance. Elle est normative. Les conséquences de chaque paquet sur cette organisation
> figurent dans les blocs « Organisation du dépôt » des sections de paquet (§3 à §8) ;
> elles migrent vers le document de référence détaillé de l'étape à sa création (§2.1.1).
> En cas de divergence sur les emplacements, cette section fait foi ; l'ordre des étapes
> relève de la planification (préséance : [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md)
> §1). L'historique des choix d'organisation est consigné dans
> [`Annexe_Variantes_Et_Historique_Globale.md`](./Annexes/Annexe_Variantes_Et_Historique_Globale.md).

Ne relève pas de cette section : les commandes Git, les procédures d'issue, de branche ou
de revue (traitées dans un cadre séparé) ; le contenu détaillé des pilotes, des trames ou
des modèles de filtre ; les choix de matériel, d'adresses I2C ou de brochage ; les
résultats de mesure et les campagnes de données ; les décisions de licence ou de diffusion
du dépôt. Ces points relèvent des documents d'étape, des documents d'ingénierie ou d'un
cadre séparé.

### 10.1 Décisions d'organisation

L'organisation retenue repose sur les principes suivants :

1. **Un seul dépôt Git** : `Programmation/FromTo Github`.
2. **Aucun second dépôt** pour les capteurs ou le filtre de Kalman.
3. **Aucun sous-module** dédié aux capteurs ou au filtre.
4. **Pas de dossier racine temporaire** `Capteurs/` ou `Kalman/` destiné à être déplacé plus tard.
5. Les domaines « capteurs » et « Kalman » sont matérialisés dès le départ à leur emplacement final :
   - `lib/sensors/` pour les pilotes et le code capteurs réutilisable ;
   - `lib/kalman/` pour le filtre pur ;
   - `bench/` pour les projets PlatformIO autonomes, prêts à exécuter.
6. L'intégration au firmware se fait progressivement, par petites tranches, sans copie de code.

Le dossier `Programmation/Capteurs/` n'est **pas** utilisé. Il est remplacé par :

- `FromTo Github/lib/sensors/` ;
- `FromTo Github/bench/test_*`.

De même, un éventuel dossier `Programmation/Kalman/` n'est pas créé : le filtre vit dans `FromTo Github/lib/kalman/`.

> **Règle importante :** `Capteurs` et `Kalman` désignent des domaines fonctionnels, pas des dépôts ni des dossiers racine à déplacer. Toute création exploratoire d'un dossier `Capteurs/` ou `Kalman/` doit être considérée comme un espace de travail temporaire dont le contenu rejoindra `lib/` et `bench/`. Elle ne doit jamais devenir une seconde source de vérité.

### 10.2 Arborescence cible

```text
FromTo Github/                 <- dépôt Git et projet PlatformIO firmware
├── platformio.ini             <- environnements du firmware
├── src/                       <- code firmware
├── include/                   <- en-têtes firmware
├── main/                      <- points d'entrée dirigeable / nacelle / ordinateur
├── lib/
│   ├── sensors/               <- bibliothèque capteurs
│   │   ├── library.json
│   │   ├── include/
│   │   ├── src/
│   │   └── test/              <- tests natifs des fonctions pures
│   └── kalman/                <- bibliothèque filtre
│       ├── library.json
│       ├── include/
│       ├── src/
│       └── test/              <- tests natifs du filtre
├── bench/
│   ├── test_bno085/           <- projet PlatformIO autonome
│   ├── test_bmp581/           <- projet PlatformIO autonome
│   ├── test_all/              <- acquisition multi-capteurs
│   ├── test_kalman/           <- rejeu du filtre : hôte, portabilité, cible
│   └── banc_cpu/              <- profilage
├── test/                      <- tests automatisés uniquement
├── commun/                    <- sous-module existant, inchangé
├── .gitignore
└── README.md
```

Le `platformio.ini` racine reste celui du **firmware**. Chaque dossier de `bench/` possède son propre `platformio.ini` et constitue un projet PlatformIO indépendant.

### 10.3 Rôles des dossiers

| Emplacement | Rôle | Ne doit pas contenir |
|---|---|---|
| `lib/sensors/` | Pilotes capteurs, décodage, conversions, configuration partagée, interfaces. | Du code spécifique au firmware, des globales de `AirshipState`, des boucles `loop()` de banc. |
| `lib/kalman/` | Filtre pur, types `FilterState` / `ControlState` / `FilterDiagnostics`, configuration du filtre, tests natifs. | `Arduino.h`, `Wire`, accès matériel, dépendance au firmware. |
| `bench/test_*` | Programmes prêts à exécuter : initialisation, lecture, affichage, diagnostic. | Des assertions de test unitaire à la place d'un programme de banc. |
| `bench/test_kalman/` | Rejeu de jeux de données ou données synthétiques à travers le filtre : banc hôte, portabilité de compilation et comparaison hôte/cible ; programme exécutable, sans assertions unitaires. | Le filtre lui-même : il doit être dans `lib/kalman/` ; des pilotes ou une dépendance `lib/sensors/`. |
| `test/` | Tests automatisés et assertions. | Des boucles infinies de lecture capteur. |
| `src/Capteurs/`, `include/Capteurs/` | Adaptateurs firmware et intégration des bibliothèques. | Une seconde copie des pilotes ou du filtre. |
| `src/Asservissement/`, `include/Asservissement/` | Consommation de `ControlState` par l'asservissement. | La logique de filtrage ou de décodage capteur. |
| `main/dirigeable/` | Câblage final : acquisition, filtre, asservissement, télémétrie. | Les détails d'implémentation des capteurs ou du filtre. |
| `commun/` | Sous-module partagé existant. | Une copie locale non déclarée. |

> **Distinction `test` :** `bench/test_kalman/` est un **programme de rejeu exécutable** ; les
> tests unitaires et les scénarios de validation du filtre vivent dans `lib/kalman/test/`
> (`pio test -e native`), et le dossier `test/` de la racine ne porte que des assertions.

### 10.4 Règles de dépendance

Ces règles doivent être respectées par tous les agents.

1. **`lib/kalman` est indépendant du matériel et du firmware.**
   - Pas d'inclusion de `Arduino.h`, `Wire`, `AirshipState`, `config.h` du firmware.
   - Dépendances autorisées : C++ standard, Eigen, ses propres en-têtes.
   - Le filtre reçoit ses mesures et sa configuration par des types explicites.

2. **`lib/sensors` peut dépendre d'Arduino et des bus.**
   - Les pilotes matériels peuvent utiliser `Wire`, `Serial`, etc.
   - Les fonctions pures de décodage, conversion et validation doivent être séparées pour être testables sur hôte.
   - Aucun pilote ne doit dépendre de `AirshipState` ou de la boucle principale du firmware.

3. **`bench/*` peut dépendre de `lib/*` et de bibliothèques externes.**
   - Chaque bench déclare ses dépendances dans son propre `platformio.ini`.
   - Un bench ne doit pas dépendre de `src/`, `main/` ou des globales du firmware.
   - Un bench doit pouvoir être construit et exécuté sans modifier le firmware.

4. **Le firmware peut dépendre de `lib/*`.**
   - `src/` et `include/` contiennent les adaptateurs et le câblage.
   - Aucun code de pilote ou de filtre ne doit être dupliqué dans `src/` ou `include/`.

5. **`test/` contient des assertions.**
   - Les tests natifs du filtre peuvent vivre dans `lib/kalman/test/` ou dans `test/` ; les tests natifs des fonctions pures capteurs vivent dans `lib/sensors/test/`.
   - Les tests matériels peuvent cibler la Teensy, mais ils doivent produire un résultat de test, pas un programme interactif permanent.

6. **`commun/` reste la source partagée existante.**
   - Ne pas en créer une copie parallèle.
   - Si un bench ou une bibliothèque l'utilise, la dépendance doit être déclarée et le sous-module initialisé.

### 10.5 Synthèse des changements dans `FromTo Github`

**Créé :**

- `lib/sensors/` : bibliothèque capteurs, configuration partagée, fonctions pures.
- `lib/kalman/` : filtre pur, types d'état, tests natifs.
- `bench/test_bno085/`, `bench/test_bmp581/`, `bench/test_all/`, `bench/test_kalman/`, `bench/banc_cpu/` : projets PlatformIO autonomes.
- `test/fixtures/` : petits jeux de données textuels, si nécessaire.
- Adaptateurs firmware dans `src/Capteurs/` et `include/Capteurs/`, si l'intégration l'exige.

**Modifié :**

- `main/dirigeable/main.cpp` : câblage acquisition → filtre → asservissement.
- `src/Asservissement/`, `include/Asservissement/` : consommation de `ControlState`.
- `platformio.ini` : environnements, dépendances, exclusions de `bench/`.
- `include/config.h` : alignement des constantes si nécessaire.
- `test/` : tests automatisés.
- `.gitignore` : artefacts PlatformIO, données, fichiers générés.
- `README.md` : structure, construction, exécution des benches, limites connues.

**Inchangé :**

- `commun/` : sous-module existant.
- Les environnements firmware non concernés par la tranche en cours.
- Les benches déjà validés, sauf correction explicite.
- L'API publique des bibliothèques, sauf décision documentée.

**Explicitement exclu du dépôt :**

- `Data/` : journaux et mesures brutes.
- `EKF_Theory/` : supports de cours.
- `Elec/` : schémas et fichiers binaires.
- `Materiel/Datasheets/` : fiches constructeurs.
- `old/` : archive historique.
- `.pio/`, `compile_commands.json`, artefacts de compilation.
- Artefacts LaTeX (`*.aux`, `*.log`, `*.out`, `*.toc`, `*.fls`, `*.fdb_latexmk`).
- Métadonnées système (`System Volume Information/`, `Thumbs.db`).

Les jeux de données nécessaires aux tests doivent être petits, textuels et versionnés dans `test/fixtures/`. Les jeux de données volumineux restent à l'extérieur du dépôt.

### 10.6 Points de vigilance propres au dépôt actuel

1. **`src_dir = .`** : le `platformio.ini` racine utilise la racine comme dossier source. Les benches doivent rester hors de `src/` et être explicitement exclus des filtres de compilation du firmware.
2. **`lib_extra_dirs = lib`** : cette directive pointe actuellement vers un dossier `lib/` inexistant. La création de `lib/` rend la configuration cohérente.
3. **`commun/`** : le sous-module est présent mais vide dans certains checkouts. Il doit être initialisé avant tout build qui l'utilise ; il ne doit pas être dupliqué.
4. **`compile_commands.json`** : fichier généré, actuellement suivi par Git ; il doit être ignoré.
5. **`test/` existant** : plusieurs programmes de `test/` sont des programmes de banc, pas des tests unitaires. Ils peuvent rester comme référence ou filet de non-régression, mais les nouveaux tests automatisés doivent être clairement séparés.
6. **`FromTo Github`** : le nom contient un espace. Les chemins doivent être cités dans les scripts et les configurations. Un renommage ultérieur est possible, mais il doit être traité comme une modification dédiée.
7. **Dépendances** : les versions de `platform` et de `lib_deps` doivent être épinglées pour que les benches et le firmware soient reproductibles.
8. **Pas de dossier `Programmation/Capteurs/`** : ce dossier est remplacé par `lib/sensors/` et `bench/` (§10.1) ; ne pas créer une seconde arborescence parallèle.

---

## 11. Documents associés

> La carte complète des documents (normatifs et non normatifs) et l'ordre de lecture sont
> tenus à jour dans [`Suivi_Avancement.md`](./Suivi_Avancement.md) §3, qui fait foi.
> Ce document s'appuie directement sur [`Suivi_Avancement.md`](./Suivi_Avancement.md)
> (point d'entrée), [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md)
> (structure de planification) et [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md)
> (détail d'implémentation de l'étape 1).

Documents d'ingénierie directement utilisés par le présent référentiel :
[`Formulations_Kalman.pdf`](../../Formulations_Kalman/Formulations_Kalman.pdf) (spécifications
théoriques de référence) et [`Implementation.pdf`](../../Implementation/Implementation.pdf)
(contraintes d'implémentation embarquée).
