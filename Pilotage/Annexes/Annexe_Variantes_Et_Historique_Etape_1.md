# ANNEXE — Variantes et historique de l'étape 1

> ## AVERTISSEMENT — DOCUMENT NON NORMATIF
> ## NE PAS UTILISER COMME SUPPORT DE PROGRAMMATION
>
> Ce fichier conserve les **pistes écartées**, les **variantes** et l'**historique des
> décisions** de l'étape 1. Il n'est **jamais** une spécification : aucune de ses lignes ne
> doit être implémentée. Le support de programmation de l'étape 1 est
> [`Reference_Technique_Etape_1_Capteurs.md`](../Reference_Technique_Etape_1_Capteurs.md) ;
> le point d'entrée des sessions est [`Suivi_Avancement.md`](../Suivi_Avancement.md).
>
> Les statuts `Adopté` sont rappelés pour mémoire : la décision qui fait foi est celle du
> document de référence. En cas de contradiction, **le document de référence fait foi**.

> **Document compagnon** de
> [`Reference_Technique_Etape_1_Capteurs.md`](../Reference_Technique_Etape_1_Capteurs.md).
> Planification : [`Planification_Etapes_Paquets_Jalons.md`](../Planification_Etapes_Paquets_Jalons.md).
> Chaque entrée indique son statut, ses avantages, ses inconvénients et, le cas échéant, la
> condition qui la rendrait pertinente.

---

## 1. Vue d'ensemble

| Idée | Statut | Motif / suite |
| :--- | :--- | :--- |
| Simulation réservée à `DEV` | **Adopté** | `source` + `#if` + `static_assert` (référence étape 1 §1.2.1) |
| Sélecteur `CAP_TEST_CIBLE` | **Adopté** | Filtre le périmètre de test sans changer le mode |
| `FilterState` dédié | **Adopté** | État nominal distinct de `CapteursState` ; `P`/`dx` internes (référence étape 1 §7.2) |
| `ControlState` projeté | **Adopté** | L'asservissement ne lit jamais `CapteursState` (référence étape 1 §7.3) |
| Structures conditionnelles | **Rejeté** | Disposition fixe ; `valid` + `FilterConfig`, `#if` sur les fonctions (référence étape 1 §7.4) |
| `ValsImuBNO` stocké | **Rejeté** | *Slicing* ; `ValsImu` sur-ensemble + `valid` (référence étape 1 §2.6) |
| Colonne `mode` par ligne | **Rejeté** | Divergence `USAGE`/`VALIDATION`, `id` instable |
| Masque `modes` avec invariants | **Différé** | Seulement si un besoin matériel par mode est démontré |
| Table `CFG_*_SIM[]` séparée | **Variante** | Plus isolante, mais duplique structures et `id` |
| Override de table par projet | **Rejeté** | Duplication et dérive de la source unique |
| Configuration séparée par mode | **Rejeté** | Duplication et dérive |
| Rejeu en `VALIDATION` | **Rejeté** | Risque de confondre dataset et mesure réelle |
| Cible de build `simulation` | **Piste** | Ergonomie ; ne remplace pas le garde-fou compile-time |
| Tests hôtes des prédicats | **Adopté** | `env:native` obligatoire ; `cap_predicats.h` sans Arduino (référence étape 1 §1.7, §6) |
| `i2cDoublon`/`nbActifs` sur le périmètre effectif | **Adopté** | Prédicats triples : `actif` + famille + cible (référence étape 1 §1.2.1, Annexe A) |
| Cohérence `CAP_SIM_ACTIF`/`source` | **Adopté** | `static_assert` du bloc 10 |
| Stabilité `id` vs index | **Adopté** | `id == i + 1` par `static_assert` (référence étape 1 §1.2.1) |
| Topologie 2 CMPS12 + 2 MPL3115A2 | **Rejeté** | Aucun document d'ingénierie ne la décrit ; capacité conservée, `actif = false` (référence étape 1 §0, §1.4) |
| Signalisation sur 4 LED (11 priorités) | **Rejeté** | Motifs d'`Implementation` §6.5 sur LED 13 ; RGB réservée (référence étape 1 §1.3.3) |
| Axe de compilation `NODE_*` (3 cartes) | **Rejeté** | Un seul calculateur (référence étape 1 §1.2, §4.1) |
| Ultrasons (`CFG_US`) | **Rejeté** | Absents de tous les documents d'ingénierie (référence étape 1 §4.1) |
| Config générée (YAML/CSV → header) | **Piste** | Réduire les erreurs de saisie |
| Hash de configuration dans les logs | **Piste** | Traçabilité des campagnes |
| Validation ODR×OSR×IIR | **Piste** | `static_assert` ou test hôte |

---

## 2. Tables d'instances et mode

### 2.1 Rappel de la décision retenue

La table d'instances décrit la **topologie capteurs** ; le mode décrit les **fonctions
compilées**. Une instance physique conserve donc la même `id`, la même adresse et le
même bus dans tous les modes. Seule l'admissibilité de `source` dépend du mode :
`SOURCE_REEL` partout, `SOURCE_SIMULE` en `DEV` uniquement.

### 2.2 Colonne `mode` par ligne

**Principe.** Ajouter une colonne `mode` (ou `modes`) à chaque ligne de `CFG_IMU[]` /
`CFG_ALTI[]`, et dériver l'activation de `modes & CAP_MODE_RUNTIME`.

**Avantages.**

- Exprime directement « ce capteur existe dans ce mode » dans une seule table.
- Permet un banc réduit sans seconde table.
- Reste `constexpr` et filtrable à la compilation.

**Inconvénients.**

- **Divergence `VALIDATION` / `USAGE`** : rien n'empêcherait de valider un jeu de
  capteurs différent de celui du vol ; les σ et les matrices `R` mesurés ne
  s'appliqueraient plus à l'ensemble embarqué.
- **Instabilité de `id`** : le même capteur physique pourrait changer d'`id` selon le
  mode, alors que `id` est la clé du log, de `CapteursState` et du rejeu.
- **Double source de vérité** : l'axe `CAP_MODE_RUNTIME` existe déjà (blocs 11/12) ;
  une colonne `mode` créerait deux vérités à synchroniser.
- **Échec silencieux** : une ligne non taguée `USAGE` désactiverait un capteur en vol
  sans erreur de compilation.
- **Complexité** : balayage `constexpr` conditionnel, `static_assert` plus fragiles,
  colonne à renseigner pour chaque ligne et chaque nouveau mode.

**Condition d'acceptation.** Un besoin réel et permanent de périmètre matériel par mode
(par exemple un capteur physiquement absent en vol mais présent au banc), jamais pour la
simulation seule.

### 2.3 Masque `modes` avec invariants monotones

**Principe.** Variante défendable de 2.2 : une colonne `modes` (masque de bits), avec
les invariants `actifs(USAGE) ⊆ actifs(VALIDATION) ⊆ actifs(DEV)` et un `static_assert`
garantissant que `actifs(USAGE)` est exactement le jeu matériel cible.

**Avantages.**

- Conserve la souplesse d'une colonne de mode.
- Les invariants empêchent la dérive la plus dangereuse (jeu de vol non validé).
- Une ligne peut appartenir à plusieurs modes sans duplication.

**Inconvénients.**

- Reste une seconde source de vérité à côté de `CAP_MODE_RUNTIME`.
- Les invariants doivent être maintenus et testés.
- Ne règle pas la stabilité de `id` si l'on crée des lignes distinctes par mode.
- Toujours plus complexe que la règle `source` unique.

**Statut.** Différé ; à n'introduire que si un besoin matériel par mode est démontré.

### 2.4 Table `CFG_*_SIM[]` séparée

**Principe.** Garder la table physique pure et ajouter une table `CFG_IMU_SIM[]` /
`CFG_ALTI_SIM[]`, compilée uniquement en `DEV`, fusionnée par la fabrique.

**Avantages.**

- Le réel et le simulé sont totalement isolés.
- Impossible d'activer une ligne simulée dans la table physique.
- La table physique ne contient aucun concept de simulation.

**Inconvénients.**

- Deux structures à maintenir en cohérence.
- Espace d'`id` à gérer (collision ou convention de plage).
- La fabrique doit fusionner deux tables et définir la priorité.
- Plus de code que la colonne `source` pour quelques lignes simulées.

**Statut.** Variante viable si le nombre d'instances simulées augmente ou si une
séparation stricte est exigée.

### 2.5 Override de table par projet

**Principe.** Chaque projet de test redéfinit sa propre table via `build_flags` ou
`#ifdef` dans l'en-tête.

**Avantages.**

- Souplesse maximale ; aucun mécanisme de sélection à coder.

**Inconvénients.**

- Duplication de la table dans chaque projet.
- Dérive inévitable entre les copies.
- Violation de la « source unique de vérité ».
- Une table `constexpr` est difficile à redéfinir proprement par `build_flags`.
- Un projet peut rester sur une table obsolète sans que personne ne le voie.

**Statut.** Rejeté.

### 2.6 Configuration séparée par mode

**Principe.** Un fichier de configuration par mode (`CapteursConfig_usage.h`,
`CapteursConfig_validation.h`, `CapteursConfig_dev.h`).

**Avantages.**

- Isolation totale ; préprocesseur trivial.

**Inconvénients.**

- Duplication de la configuration.
- Dérive garantie entre les modes.
- Perte du bénéfice d'une source unique.
- Les contrôles de parité entre modes deviennent beaucoup plus difficiles.

**Statut.** Rejeté.

---

## 3. Simulation

### 3.1 Pourquoi la simulation n'est pas admise en `VALIDATION`

Un jeu de données rejoué produit des octets indiscernables d'une mesure réelle dans la
trame canonique. Ouvrir la simulation à `VALIDATION` exposerait à confondre une campagne
de mesures avec un rejeu. Le rejeu reste possible en `DEV` avec
`CAP_DEV_SERIAL_LIBRE = 0` (trame canonique sur la série) ou avec le puits SD : le
contrat d'octets de la référence étape 1 §1.3.1 est inchangé, seul le mode de build diffère.

### 3.2 Variante : rejeu en `VALIDATION` avec garde-fou

**Principe.** Autoriser `SOURCE_SIMULE` en `VALIDATION` si un champ de métadonnées
(`# source=simule`) et un drapeau de build explicite l'indiquent.

**Avantages.**

- Permet de tester la chaîne de validation sans matériel.
- Réutilise la trame canonique.

**Inconvénients.**

- Un dataset rejoué peut être confondu avec une mesure réelle.
- Les métadonnées `#` sont facultatives pour un parseur pressé.
- Ajoute un chemin de code de validation qui n'existe pas en vol.
- Le gain est déjà couvert par un build `DEV` avec `CAP_DEV_SERIAL_LIBRE = 0`.

**Statut.** Rejeté.

### 3.3 Variante : cible de build dédiée

**Principe.** Une cible PlatformIO `env:simulation` qui compile `CAP_MODE_DEV` avec les
drapeaux de simulation.

**Avantages.**

- Commande de build unique et explicite.
- Réduit le risque d'oublier `CAP_SIM_ACTIF`.

**Inconvénients.**

- Ajoute une cible à maintenir.
- Ne remplace pas le `static_assert` qui protège les autres builds.
- Peut donner l'illusion que la simulation est disponible partout.

**Statut.** Piste d'ergonomie ; le garde-fou compile-time reste nécessaire.

---

## 4. Améliorations du mécanisme de configuration

### 4.1 Prédicats `constexpr` testables sur hôte

**Idée.** Extraire `nbActifs`, `sourceAutorisee`, `imuActive`, `altiActive` et
`i2cDoublon` dans un en-tête pur C++ testable sur PC (PlatformIO `native`).

**Bénéfices.** Détecter les erreurs de table avant le flash ; documenter les invariants ;
permettre des tests de non-régression.

**Limite.** Un `static_assert` qui échoue n'est pas un test exécutable ; on teste les
prédicats, pas l'échec de compilation.

### 4.2 `i2cDoublon` et `nbActifs` sur le périmètre effectif

**Idée.** Leur passer le même prédicat d'activation que la fabrique (`imuActive`,
`altiActive`), pour que les lignes non sélectionnées par `CAP_TEST_CIBLE` ne créent ni
faux doublon ni faux compte.

**Bénéfice.** Cohérence entre la garantie de compilation et le comportement réel.

**Risque si omis.** Un doublon entre deux lignes non sélectionnées ferait échouer un
projet de test qui n'utilise aucune des deux.

**Statut.** **Adopté** — référence étape 1 §1.2.1 et Annexe A : les prédicats combinent `actif`, le
drapeau de famille et `CAP_TEST_CIBLE` ; `i2cDoublon()` vit dans `lib/sensors/include/cap_predicats.h`.

### 4.3 Cohérence `CAP_SIM_ACTIF` / `source`

**Idée.** `static_assert` : une ligne `SOURCE_SIMULE` active exige `CAP_SIM_ACTIF` ;
`CAP_SIM_ACTIF` hors `DEV` est une erreur. Le drapeau de capability et la table restent
ainsi synchronisés.

**Bénéfice.** Évite un `nullptr` silencieux dans la fabrique.

### 4.4 Stabilité `id` vs index de table

**Idée.** Documenter et figer la convention : l'index de tableau `i` est la position de
la ligne (utilisé par `CapteursState`), `id` est l'identité logique stable (utilisé par
les logs). `CAP_TEST_CIBLE` ne renumérote ni l'un ni l'autre.

**Bénéfice.** Un log `VALIDATION` reste rapprochable d'un rejeu `DEV` et d'une session
de l'étape 2.1.

**Statut.** **Adopté** — référence étape 1 §1.2.1 : `id == index + 1` garanti par `static_assert`,
ajout en fin de table, retrait par `actif = false`.

### 4.5 Source unique générée

**Idée.** Décrire les instances dans un fichier YAML/CSV et générer `CapteursConfig.h`
(build PlatformIO ou script Python).

**Avantages.** Réduit les erreurs de saisie ; permet de valider les combinaisons
(bus/adresse/`id`) avant compilation ; facilite la revue de la table.

**Inconvénients.** Ajoute un outil et une étape de génération ; le header généré doit
rester lisible et versionné.

**Statut.** Piste à moyen terme.

### 4.6 Hash de configuration dans les logs

**Idée.** Inclure dans les métadonnées `#` un hash de la configuration active (tables,
modes, drapeaux `CAP_*`).

**Bénéfice.** Traçabilité des campagnes : un log ne peut plus être attribué à la
mauvaise configuration.

**Statut.** Piste ; à coordonner avec le contrat de trame de la référence étape 1 §1.3.1.

### 4.7 Validation des combinaisons ODR/OSR/IIR

**Idée.** Ajouter des `static_assert` ou des tests hôtes pour les combinaisons
ODR × OSR × IIR admissibles du BMP581, et pour les périodes de rapport BNO085.

**Bénéfice.** Une combinaison invalide échoue à la compilation ou au test, pas sur la
cible après flash.

**Statut.** Piste.

---

## 5. Journal des décisions

| Date | Décision | Référence |
| :--- | :--- | :--- |
| 2026-10-29 | Simulation réservée à `DEV` ; `source` + `static_assert` | Référence étape 1 §1.2.1 |
| 2026-10-29 | `CAP_TEST_CIBLE` pour le périmètre de test | Référence étape 1 §1.2.1 |
| 2026-10-29 | Colonne `mode` rejetée ; masque `modes` différé | Ce document §2.2–2.3 |
| 2026-10-29 | Table `CFG_*_SIM[]` séparée laissée en variante | Ce document §2.4 |
| 2026-10-29 | `FilterState` dédié, pas d'extension de `CapteursState` | Référence étape 1 §7.1 |
| 2026-10-29 | `ControlState` projeté depuis `FilterState`, jamais `CapteursState` | Référence étape 1 §7.2 |
| 2026-10-29 | Structures fixes ; `valid` + `FilterConfig`, `#if` sur les fonctions | Référence étape 1 §7.3 |
| 2026-10-29 | `ValsImu` sur-ensemble, `ValsImuBNO` non stocké ; `ValsAlti` commun | Référence étape 1 §2.6, §3.6, §7 |
| 2026-10-29 | Topologie = configuration de référence (BNO085 + CMPS12 + BMP581) ; capacité 3/3 conservée, lignes supplémentaires `actif = false` ; MPL3115A2 = repli comparatif | Référence étape 1 §0, §1.4 |
| 2026-10-29 | `CapteursConfig.h` absorbe `HardwareConfig.h` (traçabilité `ENABLE_*` → `CAP_*`) | Référence étape 1 §1.1.1 |
| 2026-10-29 | Signalisation : motifs d'`Implementation` §6.5 sur la **LED 13** seule ; RGB réservée, broches `À définir` | Référence étape 1 §1.3.3 |
| 2026-10-29 | Sélection par nœud (`NODE_*`) non reprise ; `CFG_US` (ultrasons) hors périmètre | Référence étape 1 §1.2, §4.1 |
| 2026-10-29 | Timeouts **dérivés** (`4 × periode_us`), périodes par instance ; plus de `CAP_PERIOD_*` ni de `CAP_TIMEOUT_*_MS` fixes | Référence étape 1 §1.3 bloc 3/7, §5.3 |
| 2026-10-29 | `id == index + 1` garanti par `static_assert` ; ajout en fin de table, retrait par `actif = false` | Référence étape 1 §1.2.1 (adopte §4.4 ci-dessous) |
| 2026-10-29 | Toutes les macros surchargeables passent sous `#ifndef` (+ défaut `CAP_REQUIRE_SINGLE_SENSOR = (cible ≠ TEST_TOUS)`) | Référence étape 1 §1.2, Annexe A |
| 2026-10-29 | `CFG_UWB` devient une **structure** ; adresses BNO085/BMP581 dérivées de la table | Référence étape 1 §1.3 bloc 3, Annexe A |
| 2026-10-29 | ODR BMP581 = **paramètre de compromis à tester**, pas une constante ; une seule table code → Hz | Référence étape 1 §3.3 |
| 2026-10-29 | `FilterType` : quatre valeurs alignées sur `Implementation` §1.2.1 (`INERTIEL_EULER` inclus) | Référence étape 1 §7.2.2 |
| 2026-10-29 | `FilterState`/`ControlState`/`FilterDiagnostics` extraits dans un fichier de contrats dédié (extraction annulée le 2026-10-30) | Référence étape 1 §7 |
| 2026-10-30 | Contrats d'interface **réintégrés** dans la référence étape 1 ; fichier `Contrats_Interfaces_Estimation.md` supprimé et archivé | Référence étape 1 §7, Annexe C |
| 2026-10-30 | Renommage des documents du dossier : `Reference_Technique_*` (technique), `Planification_*` (normatif), `Annexe_*` (non normatif) | `Suivi_Avancement.md` §7 |
| 2026-10-30 | `cap_predicats.h` hébergé dans `lib/sensors/include/` — le sous-module `commun/` reste inchangé | Référence étape 1 §1.7, Annexe A |
| 2026-10-30 | Tests natifs des fonctions pures hébergés dans `lib/sensors/test/` (`env:native` de la bibliothèque) ; les `bench/` restent des programmes de banc, sans assertions | Référence étape 1 §1.7 ; `Organisation` §4 à §6 |
| 2026-10-30 | Références corrigées dans la référence étape 1 §8 (changelog déplacé ici) : « Implementation §2.6 » → **§2.5.1** (fil volant `H_INTN`) ; « Formulations_Kalman §2 » → **§2.2.1** et **§2.2.4** ; « §1.3.5 de Formulations_Kalman » confirmé valide (tableau de synthèse métrologique, cible du report des σ) | Référence étape 1 §8 |

---

## 6. Structures d'estimation — options écartées

Options écartées lors de la conception des structures d'estimation. Le détail **retenu** est
dans [`Reference_Technique_Etape_1_Capteurs.md`](../Reference_Technique_Etape_1_Capteurs.md) §7
(contrats d'interface) et Annexe C (squelettes).

### 6.1 État du filtre : `FilterState` dédié

| Option | Avantages | Inconvénients | Verdict |
| :--- | :--- | :--- | :--- |
| Extension de `CapteursState` | un seul objet à passer, accès direct | mélange **entrée** (mesures) et **sortie** (estimation) ; la dimension du filtre contamine la mémoire capteurs ; `P`/`Eigen` entrent dans le chemin des capteurs ; l'Étape 1 devrait compiler le filtre ; risque d'ODR/ABI dès que `FilterType` change | **rejeté** |
| Structure dédiée `FilterState` | séparation entrée/sortie ; type stable, copiable et testable ; consommable par l'asservissement et le log ; `P`/`dx` restent internes ; aucun impact sur l'Étape 1 | un type de plus et une projection de plus vers l'asservissement | **retenu** |
| État privé + getters seuls | encapsulation maximale, aucun type exposé | pas de valeur copiable ni de type commun pour le log ; multiplication des getters ; la loi de commande reste dépendante du filtre concret | rejeté comme interface ; conservé comme détail interne |
| `std::variant` / union | empreinte minimale | coût et complexité ; `union` non triviale avec `Eigen` ; impose un `valid` et un `visit` partout ; aucun gain mémoire significatif | rejeté |

Points saillants du rejet de l'extension : `CapteursState` est une mémoire d'**entrée**,
`FilterState` une **sortie** ; la dimension d'erreur dépend du modèle (`6`/`15`/`16`), donc
l'encapsuler ferait varier `sizeof(CapteursState)` avec `FilterType`, ce qui viole l'ODR
entre unités de compilation ; l'Étape 1 ne doit embarquer ni `Eigen` ni la covariance ;
l'alignement `Eigen` reste confiné au filtre (`EIGEN_MAKE_ALIGNED_OPERATOR_NEW`).

### 6.2 Asservissement : projection `ControlState`

| Option | Avantages | Inconvénients | Verdict |
| :--- | :--- | :--- | :--- |
| Tout `CapteursState` | tout est disponible, aucun adaptateur | couplage fort à la topologie capteurs ; l'asservissement devrait choisir les cellules, les drapeaux de fraîcheur et refaire une partie de la fusion ; signature instable à chaque ajout de capteur ; impossible à tester sans les pilotes | **rejeté** |
| Sous-ensemble de `CapteursState` | moins de champs, lecture directe | reste une mesure brute, contourne le filtre ; la sélection est arbitraire et diverge de l'état filtré ; la fraîcheur et la qualité restent à interpréter par la commande | **rejeté** (sauf mesure brute explicitement nommée) |
| `ControlState` projeté depuis `FilterState` | interface stable et testable ; une seule source d'estimation ; aucune dépendance aux pilotes ; le contrôle peut tourner sur PC | une conversion de plus, sans coût significatif (une fois par période de commande) | **retenu** |

### 6.3 Variantes conditionnelles — variantes rejetées

* champs sous `#if` dans une structure (`#if CAP_LOG ... float foo;`) : la disposition
  change d'une unité de compilation à l'autre → ODR, parité des logs et tests hôtes cassés ;
* `CapteursStateUsage` / `CapteursStateValidation` / `CapteursStateDev` : duplication,
  dérive garantie, remappage des `id`/colonnes ;
* une structure par type de filtre : l'asservissement et le log devraient reproduire les
  `#if`, et le filtre ne serait plus interchangeable à interface constante ;
* `std::variant`/`union` pour les payloads : complexité et coût pour quelques octets ;
* sélection **runtime** du type de filtre : le choix reste statique (`FilterConfig.h`).

---

## 7. Idées issues du code legacy — évaluées et écartées

Le dépôt `FromTo Github` et les sketches de `NvCapteurs` sont traités comme des
**précédents**, jamais comme une autorité. Chaque idée a été confrontée aux documents
d'ingénierie avant d'être retenue ou écartée.

| Idée trouvée dans le code | Évaluation | Verdict |
| :--- | :--- | :--- |
| Tables par nœud `NODE_DIRIGEABLE`/`NODE_NACELLE`/`NODE_ORDINATEUR` | contredite par `Implementation` §5.1 (une Teensy centralise tout) et `Materiel` §5 (une carte) | **rejetée** ; référence étape 1 §4.1 ne reprend plus la « sélection par nœud » |
| `CfgIMU` avec `TwoWire*` brut | un pointeur brut rend la table **non `constexpr`**, or l'anti-doublon et la table elle-même sont compilés | **rejetée** : l'enum `CapI2CBus` dérivé en `TwoWire*` par `busOf()` est retenu |
| `CfgIMU` avec `serie`/`baud` (CMPS12 série) | aucune source d'ingénierie ne décrit une CMPS12 série | **non retenu** ; à ajouter seulement si un besoin documenté apparaît |
| `CFG_US` / `MAX_ULTRASON` | aucune mention d'ultrasons dans `Materiel`, `Implementation`, `Formulations` | **hors périmètre** ; retiré de la migration §4.1 |
| `MarqueIMU::AUTRE` + classe stub | la procédure d'extension (référence étape 1 §1.5) impose une classe réelle, une table et des `static_assert` | **rejetée** |
| `change_defaut_addr.txt` (0xA0, 0xAA, 0xA5, `addr<<1`, 20 ms, seul sur le bus) | détail concret et utile, mais non probant | **retenu comme précédent**, à valider sur `cmps12_doc.pdf` (§8 de la référence étape 1) |
| Accès direct aux grandeurs (`getValsIMUDirigeable`) | contourne le filtre et couple l'asservissement aux pilotes | **écarté** pour la commande ; conservé pour la télémétrie |

---

## 8. Comment promouvoir une idée

1. Vérifier qu'elle ne contredit pas les invariants de la référence étape 1 (source unique, `id`
   stable, `VALIDATION` représentatif de `USAGE`).
2. Décrire le besoin concret et le coût de maintenance.
3. Ajouter les `static_assert` et les tests hôtes correspondants.
4. Mettre à jour la feuille de route et déplacer la ligne du statut « piste » vers
   « adopté » dans ce document.
