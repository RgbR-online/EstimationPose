# Organisation des capteurs et du filtre de Kalman dans `FromTo Github`

## 1. Objet du document

Ce document décrit l'organisation retenue pour développer les capteurs et le filtre de Kalman **dans le dépôt Git existant `FromTo Github`**, puis pour les intégrer progressivement au firmware.

Il sert de référence aux agents qui interviendront sur le dépôt. Il précise :

- ce qui doit être créé ;
- ce qui doit être modifié ;
- ce qui doit rester inchangé ;
- ce qui ne doit pas être versionné ;
- la correspondance entre les étapes de `Reference_Technique_Globale.md` et les dossiers du dépôt.

La feuille de route fournit l'ordre des travaux et les objectifs fonctionnels. Elle n'est pas normative sur les chemins exacts. En cas de divergence, **ce document fixe les emplacements et les frontières de responsabilité** ; la feuille de route fixe l'ordre des étapes.

Les modalités concrètes de branches, d'issues et de revues ne sont pas décrites ici : elles sont traitées dans un cadre séparé.

---

## 2. Décision d'organisation

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

---

## 3. Arborescence cible

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
│   │   └── src/
│   └── kalman/                <- bibliothèque filtre
│       ├── library.json
│       ├── include/
│       ├── src/
│       └── test/              <- tests natifs du filtre
├── bench/
│   ├── test_bno085/           <- projet PlatformIO autonome
│   ├── test_bmp581/           <- projet PlatformIO autonome
│   ├── test_all/              <- acquisition multi-capteurs
│   ├── kalman_replay/         <- rejeu de données, natif et/ou cible
│   └── banc_cpu/              <- profilage, si non conservé dans test/
├── test/                      <- tests automatisés uniquement
├── commun/                    <- sous-module existant, inchangé
├── .gitignore
└── README.md
```

Le `platformio.ini` racine reste celui du **firmware**. Chaque dossier de `bench/` possède son propre `platformio.ini` et constitue un projet PlatformIO indépendant.

---

## 4. Rôle des dossiers

| Emplacement | Rôle | Ne doit pas contenir |
|---|---|---|
| `lib/sensors/` | Pilotes capteurs, décodage, conversions, configuration partagée, interfaces. | Du code spécifique au firmware, des globales de `AirshipState`, des boucles `loop()` de banc. |
| `lib/kalman/` | Filtre pur, types `FilterState` / `ControlState` / `FilterDiagnostics`, configuration du filtre, tests natifs. | `Arduino.h`, `Wire`, accès matériel, dépendance au firmware. |
| `bench/test_*` | Programmes prêts à exécuter : initialisation, lecture, affichage, diagnostic. | Des assertions de test unitaire à la place d'un programme de banc. |
| `bench/kalman_replay/` | Rejeu de jeux de données ou données synthétiques à travers le filtre. | Le filtre lui-même : il doit être dans `lib/kalman/`. |
| `test/` | Tests automatisés et assertions. | Des boucles infinies de lecture capteur. |
| `src/Capteurs/`, `include/Capteurs/` | Adaptateurs firmware et intégration des bibliothèques. | Une seconde copie des pilotes ou du filtre. |
| `src/Asservissement/`, `include/Asservissement/` | Consommation de `ControlState` par l'asservissement. | La logique de filtrage ou de décodage capteur. |
| `main/dirigeable/` | Câblage final : acquisition, filtre, asservissement, télémétrie. | Les détails d'implémentation des capteurs ou du filtre. |
| `commun/` | Sous-module partagé existant. | Une copie locale non déclarée. |

---

## 5. Règles de dépendance

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
   - Les tests natifs du filtre peuvent vivre dans `lib/kalman/test/` ou dans `test/`.
   - Les tests matériels peuvent cibler la Teensy, mais ils doivent produire un résultat de test, pas un programme interactif permanent.

6. **`commun/` reste la source partagée existante.**
   - Ne pas en créer une copie parallèle.
   - Si un bench ou une bibliothèque l'utilise, la dépendance doit être déclarée et le sous-module initialisé.

---

## 6. Correspondance avec les étapes et les paquets de travail

La planification de référence est définie dans [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md). Les tableaux ci-dessous indiquent les conséquences de chaque paquet sur l'organisation du dépôt.

### Étape 1 — Capteurs en isolation

#### Paquet 1.1 — Validation indépendante par capteur

| Créé | Modifié | Inchangé |
|---|---|---|
| `lib/sensors/` : squelette, `library.json`, interfaces, pilotes BNO085 et BMP581 | `README.md` si les instructions de banc y sont ajoutées | `src/`, `include/`, `main/` du firmware |
| `bench/test_bno085/` avec son `platformio.ini` | `.gitignore` si nécessaire | `platformio.ini` racine, sauf exclusion explicite de `bench/` |
| `bench/test_bmp581/` avec son `platformio.ini` | | `commun/` |
| Tests des fonctions pures dans `lib/sensors/test/` ou `test/` | | |
| `lib/sensors/include/CapteursConfig.h` comme source unique de configuration | | |

**Point de reprise :** `C1.1`  
**Jalon :** `J1`

#### Paquet 1.2 — Acquisition multi-capteurs

| Créé | Modifié | Inchangé |
|---|---|---|
| `bench/test_all/` ou `bench/acquisition/` avec son `platformio.ini` | `lib/sensors/` pour la coordination des instances | `src/`, `include/`, `main/` du firmware |
| Structures de mémorisation des mesures, si nécessaire | `CapteursConfig.h` pour les instances actives | `platformio.ini` racine |
| Tests de cohérence des tables et des conversions | | `commun/` |

**Point de reprise :** `C1.2`

#### Paquet 1.3 — Caractérisation métrologique et jeux de données

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de caractérisation métrologique | `lib/sensors/` pour les conversions et les métadonnées | `src/`, `include/`, `main/` du firmware |
| Format de log et parseur unique | `.gitignore` pour les jeux volumineux | `platformio.ini` racine |
| Jeux de données réels et jeux de validation | `README.md` pour la procédure d'acquisition | `commun/` |
| Petits jeux de test dans `test/fixtures/` si nécessaire | | |

**Point de reprise :** `C1.3`  
**Jalon :** `J2`

### Étape 2 — Kalman en isolation

#### Paquet 2.1 — Validation indépendante du filtre

| Créé | Modifié | Inchangé |
|---|---|---|
| `lib/kalman/` : noyau pur, `FilterConfig`, types d'état, API de prédiction et de correction | `README.md` pour la procédure de rejeu | `src/`, `include/`, `main/` du firmware |
| `bench/kalman_replay/` avec environnements natif et/ou cible | `.gitignore` pour les jeux volumineux | `platformio.ini` racine |
| Tests et scénarios de validation dans `lib/kalman/test/` | | `commun/` |
| Rapport de validation du filtre | | |

**Point de reprise :** `C2.1`  
**Jalon :** `J3`

#### Paquet 2.2 — Validation matérielle du filtre sur banc autonome

| Créé | Modifié | Inchangé |
|---|---|---|
| Environnement cible de `bench/kalman_replay/` et scripts de rejeu sur cible | `README.md` pour la procédure de rejeu sur cible | `lib/kalman/` : source unique, aucune copie |
| Rapport de validation matérielle (comparaison hôte/cible) | | `src/`, `include/`, `main/` du firmware |
| | | `platformio.ini` racine, `commun/` |

**Point de reprise :** `C2.2`  
**Jalon :** `J3`

### Étape 3 — Intégration et validation sur cible

#### Paquet 3.1 — Intégration dans le PlatformIO global

| Créé | Modifié | Inchangé |
|---|---|---|
| Adaptateurs dans `src/Capteurs/` et `include/Capteurs/` si nécessaire | `main/dirigeable/main.cpp` pour le câblage acquisition → filtre → asservissement | `commun/` |
| Configuration firmware du filtre, si elle diffère de `FilterConfig` | `src/Asservissement/`, `include/Asservissement/` pour consommer `ControlState` | Les bancs restent autonomes |
| Environnements PlatformIO nécessaires dans `platformio.ini` | `platformio.ini` pour les dépendances et les exclusions de `bench/` | Les pilotes de `lib/sensors/` |
| | `include/config.h` si des constantes doivent être alignées | |

**Point de reprise :** `C3.1`

#### Paquet 3.2 — Tests multi-capteurs sur cible

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de tests sur cible | `bench/test_all/` si les scénarios de banc évoluent | `lib/kalman/` |
| Journal des acquisitions | `README.md` pour les procédures de test | `lib/sensors/`, sauf correction justifiée |
| | | `commun/` |

**Point de reprise :** `C3.2`

#### Paquet 3.3 — Tests d'intégration Kalman sur cible

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de rejeu sur cible | `main/dirigeable/`, `src/`, `include/` pour les modes dégradés | `lib/kalman/` : API stable |
| Rapport de temps et de mémoire | `platformio.ini` pour les environnements de test | `lib/sensors/` : pilotes stables |
| Documentation des modes dégradés | | `commun/` |

**Point de reprise :** `C3.3`  
**Jalon :** `J4`

### Étape 4 — Optimisation temps réel

#### Paquet 4.1 — Ordonnancement et horodatage

| Créé | Modifié | Inchangé |
|---|---|---|
| Instrumentation de mesure des temps, si elle n'existe pas déjà | `main/dirigeable/`, `src/`, `include/` pour l'ordonnancement | `lib/kalman/` : API stable |
| Documentation de l'ordonnancement | `platformio.ini` pour les environnements de mesure | `lib/sensors/` : pilotes stables |
| | | `commun/` |

**Point de reprise :** `C4.1`

### Étape 5 — Profilage

#### Paquet 5.1 — Profilage CPU, mémoire et latence

| Créé | Modifié | Inchangé |
|---|---|---|
| Résultats de profilage documentés | `bench/banc_cpu/` ou `test/banc_cpu/` selon l'emplacement retenu | `lib/kalman/` et `lib/sensors/` : uniquement des corrections justifiées |
| | `platformio.ini` pour l'environnement de profilage | `commun/` |
| | `README.md` pour les résultats et les limites | |

**Point de reprise :** `C5.1`  
**Jalon :** `J5`

### Étape 6 — Validation système et clôture

#### Paquet 6.1 — Validation système

| Créé | Modifié | Inchangé |
|---|---|---|
| Rapport de validation système | `README.md` pour les résultats, les limites et les travaux futurs | Les bibliothèques, sauf correction justifiée |
| Documentation des modes dégradés et de sécurité | `platformio.ini` si les environnements de validation évoluent | `commun/` |
| Liste des limites et des travaux futurs | | |

**Point de reprise :** `C6.1`  
**Jalon :** `J6`

---

## 7. Synthèse des changements dans `FromTo Github`

### Créé

- `lib/sensors/` : bibliothèque capteurs, configuration partagée, fonctions pures.
- `lib/kalman/` : filtre pur, types d'état, tests natifs.
- `bench/test_bno085/`, `bench/test_bmp581/`, `bench/test_all/`, `bench/kalman_replay/`, `bench/banc_cpu/` : projets PlatformIO autonomes.
- `test/fixtures/` : petits jeux de données textuels, si nécessaire.
- Adaptateurs firmware dans `src/Capteurs/` et `include/Capteurs/`, si l'intégration l'exige.

### Modifié

- `main/dirigeable/main.cpp` : câblage acquisition → filtre → asservissement.
- `src/Asservissement/`, `include/Asservissement/` : consommation de `ControlState`.
- `platformio.ini` : environnements, dépendances, exclusions de `bench/`.
- `include/config.h` : alignement des constantes si nécessaire.
- `test/` : tests automatisés.
- `.gitignore` : artefacts PlatformIO, données, fichiers générés.
- `README.md` : structure, construction, exécution des benches, limites connues.

### Inchangé

- `commun/` : sous-module existant.
- Les environnements firmware non concernés par la tranche en cours.
- Les benches déjà validés, sauf correction explicite.
- L'API publique des bibliothèques, sauf décision documentée.

### Explicitement exclu du dépôt

- `Data/` : journaux et mesures brutes.
- `EKF_Theory/` : supports de cours.
- `Elec/` : schémas et fichiers binaires.
- `Materiel/Datasheets/` : fiches constructeurs.
- `old/` : archive historique.
- `.pio/`, `compile_commands.json`, artefacts de compilation.
- Artefacts LaTeX (`*.aux`, `*.log`, `*.out`, `*.toc`, `*.fls`, `*.fdb_latexmk`).
- Métadonnées système (`System Volume Information/`, `Thumbs.db`).

Les jeux de données nécessaires aux tests doivent être petits, textuels et versionnés dans `test/fixtures/`. Les jeux de données volumineux restent à l'extérieur du dépôt.

---

## 8. Points de vigilance propres au dépôt actuel

1. **`src_dir = .`** : le `platformio.ini` racine utilise la racine comme dossier source. Les benches doivent rester hors de `src/` et être explicitement exclus des filtres de compilation du firmware.
2. **`lib_extra_dirs = lib`** : cette directive pointe actuellement vers un dossier `lib/` inexistant. La création de `lib/` rend la configuration cohérente.
3. **`commun/`** : le sous-module est présent mais vide dans certains checkouts. Il doit être initialisé avant tout build qui l'utilise ; il ne doit pas être dupliqué.
4. **`compile_commands.json`** : fichier généré, actuellement suivi par Git ; il doit être ignoré.
5. **`test/` existant** : plusieurs programmes de `test/` sont des programmes de banc, pas des tests unitaires. Ils peuvent rester comme référence ou filet de non-régression, mais les nouveaux tests automatisés doivent être clairement séparés.
6. **`FromTo Github`** : le nom contient un espace. Les chemins doivent être cités dans les scripts et les configurations. Un renommage ultérieur est possible, mais il doit être traité comme une modification dédiée.
7. **Dépendances** : les versions de `platform` et de `lib_deps` doivent être épinglées pour que les benches et le firmware soient reproductibles.
8. **Pas de dossier `Programmation/Capteurs/`** : ce dossier est remplacé par `lib/sensors/` et `bench/`. Les agents ne doivent pas créer une seconde arborescence parallèle.

---

## 9. Ce que ce document ne couvre pas

- Les commandes Git, les procédures d'issue, de branche ou de revue.
- Le contenu détaillé des pilotes, des trames ou des modèles de filtre.
- Les choix de matériel, d'adresses I2C ou de brochage.
- Les résultats de mesure et les campagnes de données.
- Les décisions de licence ou de diffusion du dépôt.

Ces points sont traités dans les documents spécialisés ou dans un cadre séparé.

---

## 10. Critères globaux de réussite

L'organisation est correctement appliquée si :

- il n'existe qu'un seul dépôt Git pour le firmware, les capteurs et le filtre ;
- `lib/sensors/` et `lib/kalman/` sont les sources uniques de vérité ;
- chaque bench est un projet PlatformIO autonome, constructible et exécutable ;
- `test/` contient des assertions, pas des programmes interactifs ;
- le firmware consomme les bibliothèques sans en copier le code ;
- les données volumineuses et les documents confidentiels restent hors du dépôt ;
- les étapes de la feuille de route sont traçables dans l'historique Git et dans les revues, sans duplication d'arborescence.
