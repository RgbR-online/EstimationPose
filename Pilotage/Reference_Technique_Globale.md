# Référentiel technique global — étapes et paquets

> **Rôle du document.** Référentiel technique **unique** du développement des capteurs, du
> filtre de Kalman et de leur intégration dans le dirigeable autonome. Il décrit le contenu
> technique de chaque étape et de chaque paquet, et **pointe vers les documents détaillés**
> des étapes (§2.1). Il ne définit **ni identifiant, ni jalon, ni règle de progression** :
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
   renvoi (§2.2, règle 7) ;
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
   critères de paquet : le présent référentiel. Détail d'implémentation : le document de
   référence de l'étape concernée.
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
   référentiel est réduite à l'objectif et au renvoi vers ce document : le détail technique
   d'une étape n'existe qu'à un seul endroit.

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
- un rapport de validation ;
- une API publique documentée ;
- le document de référence détaillé de l'étape (`Reference_Technique_Etape_2_Kalman.md`),
  rédigé selon la checklist §2.1.1.

**Critères de sortie :**

- le filtre se compile et s'exécute sur hôte ;
- les tests unitaires et les scénarios de validation passent ;
- les invariants numériques sont vérifiés ;
- les jeux de données et les simulations sont rejouables ;
- l'API publique est stable et documentée ;
- le filtre ne dépend ni d'Arduino, ni du firmware, ni d'un pilote matériel.

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

## 10. Documents associés

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
