# Suivi d'avancement — point d'entrée des sessions de programmation

*Dernière révision : 2026-10-30 10:27*

> **Lire ce fichier en premier, avant tout autre document.**
> Il indique où en est le projet, quel paquet exécuter ensuite, et où trouver chaque
> information. Il ne contient **aucune spécification** : les règles sont dans la
> planification, le contenu technique dans les documents de référence.

---

## 1. État d'avancement

> Objets, points de reprise et jalons : [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §4,
> qui fait foi. Ce tableau ne porte que l'état d'avancement.

| Paquet | État | Date | Point de reprise | Note de passation | Jalon | Commit |
|---|---|---|---|---|---|---|
| `1.1` | `à faire` | — | `C1.1` | — | `J1` | — |
| `1.2` | `à faire` | — | `C1.2` | — | — | — |
| `1.3` | `à faire` | — | `C1.3` | — | `J2` | — |
| `2.1` | `à faire` | — | `C2.1` | — | — | — |
| `2.2` | `à faire` | — | `C2.2` | — | `J3` | — |
| `3.1` | `à faire` | — | `C3.1` | — | — | — |
| `3.2` | `à faire` | — | `C3.2` | — | — | — |
| `3.3` | `à faire` | — | `C3.3` | — | `J4` | — |
| `4.1` | `à faire` | — | `C4.1` | — | — | — |
| `5.1` | `à faire` | — | `C5.1` | — | `J5` | — |
| `6.1` | `à faire` | — | `C6.1` | — | `J6` | — |

États admis : `à faire`, `en cours`, `bloqué`, `terminé`. Un paquet n'est `terminé` que si
ses critères d'acceptation sont vérifiés **et** que la note de passation existe
(planification §7, règle 4).

Colonnes : `Date` = date de la dernière mise à jour de la ligne (AAAA-MM-JJ) ;
`Note de passation` = lien vers la note `../Passations/C<identifiant>.md`, sous la forme
`[C1.1](../Passations/C1.1.md)`, `—` tant qu'aucune note n'existe ; `Commit` = hach court
du commit du dépôt de documentation (`Programmation/`), les hach de code
(`FromTo Github`) étant consignés dans la note de passation.

> **Prochain paquet de travail : `1.1` — validation indépendante par capteur.**
> Contenu : [`Reference_Technique_Globale.md`](./Reference_Technique_Globale.md#paquet-1-1) §3, paquet `1.1`.
> Détail d'implémentation et ordre de lecture : [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md#ordre-de-lecture).

### Jalons

| Jalon | Évalué après | État | Décision consignée |
|---|---|---|---|
| `J1` | `C1.1` | `non atteint` | — |
| `J2` | `C1.3` | `non atteint` | — |
| `J3` | `C2.2` | `non atteint` | — |
| `J4` | `C3.3` | `non atteint` | — |
| `J5` | `C5.1` | `non atteint` | — |
| `J6` | `C6.1` | `non atteint` | — |

---

## 2. Ordre de lecture imposé en début de session

1. **Ce fichier** (§1 pour l'état, §3 pour la carte des documents).
2. [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) —
   identifiants, niveaux, jalons, règles de progression, gabarit de note de passation.
3. [`Reference_Technique_Globale.md`](./Reference_Technique_Globale.md) — le paquet à
   exécuter : objectif, périmètre, livrables, critères de sortie.
4. Le document de référence détaillé de l'étape concernée
   (`Reference_Technique_Etape_<n>_*.md`), s'il existe.
5. Les documents d'ingénierie cités par ce document d'étape, **uniquement** pour le point
   traité.

Les fichiers `Annexe_*` ne sont **jamais** un support de programmation : ils ne se lisent
que si la session porte explicitement sur une variante ou un historique.

---

## 3. Carte des documents

| Document | Rôle | Normatif ? | Quand le lire |
|---|---|---|---|
| [`Suivi_Avancement.md`](./Suivi_Avancement.md) | Point d'entrée : état, ordre de lecture, carte | non (état) | **toujours, en premier** |
| [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) | Niveaux, identifiants, jalons, règles de progression, gabarit de passation | **oui** | début de session |
| [`Reference_Technique_Globale.md`](./Reference_Technique_Globale.md) | Référentiel technique global : objectifs, paquets, critères, renvois par étape | **oui** | pour le paquet à exécuter |
| [`Reference_Technique_Etape_1_Capteurs.md`](./Reference_Technique_Etape_1_Capteurs.md) | Détail de l'étape 1 + contrats d'interface (§7) + squelettes (Annexe C) | **oui** | toute session sur `1.x` |
| [`Organisation_FromTo_Github.md`](./Organisation_FromTo_Github.md) | Dossiers et bibliothèques du dépôt `FromTo Github` | **oui** | avant de créer ou déplacer du code |
| [`Annexe_Variantes_Et_Historique_Etape_1.md`](./Annexes/Annexe_Variantes_Et_Historique_Etape_1.md) | Variantes écartées et historique des décisions de l'étape 1 | **non — ne pas programmer dessus** | uniquement sur demande explicite |
| [`Annexe_Variantes_Et_Historique_Globale.md`](./Annexes/Annexe_Variantes_Et_Historique_Globale.md) | Variantes écartées et historique du référentiel global | **non — ne pas programmer dessus** | uniquement sur demande explicite |
| [`Annexe_Archive_Contrats_Interfaces.md`](./Annexes/Annexe_Archive_Contrats_Interfaces.md) | Archive allégée de l'ancien fichier de contrats (copie intégrale dans l'historique Git) | **non — ne pas programmer dessus** | uniquement pour traçabilité |

Les trois fichiers `Annexe_*` sont regroupés dans le sous-dossier [`Annexes/`](./Annexes/) ;
tous les renvois des documents normatifs pointent vers cet emplacement.

Documents d'ingénierie (hors dossier, font autorité) : `Materiel.pdf`,
`Implementation.pdf`, `Formulations_Kalman.pdf`, `Materiel/Datasheets/`,
`Elec/Schéma électrique capteurs.pdf`.

`Reference_Technique_Etape_2_Kalman.md` à `Reference_Technique_Etape_6_Validation.md`
n'existent pas encore : ils sont créés au démarrage du premier paquet de leur étape.

---

## 4. Règles d'une session de programmation

> Les règles de progression font foi dans
> [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §7 ;
> cette section n'est qu'un **rappel** orienté session. En cas de divergence, la
> planification fait foi.

1. Travailler sur **un seul paquet** — celui désigné au §1 — et s'arrêter à son point de
   reprise, sans enchaîner sur le suivant (planification §7, règles 1 à 3).
2. Ne rien redéfinir : identifiants, jalons, livrables et critères d'acceptation sont
   ceux de la planification et du référentiel (planification §7, règles 4, 9 et 10).
3. Règles techniques permanentes : aucune duplication de code entre bancs, bibliothèques
   et firmware ; éléments hors périmètre non implémentés ; fichiers `Annexe_*` jamais
   support de programmation (planification §7, règles 5 à 7 ; référentiel §2.2, règle 6).
4. Un paquet est `terminé` lorsque ses critères d'acceptation sont vérifiés **et** que sa
   note de passation est rédigée (planification §7, règles 3 et 4).
5. En cas de contradiction entre deux documents : appliquer la préséance du §1 de la
   planification et signaler l'écart dans la note de passation.

---

## 5. Fin de session

1. Rédiger la note de passation dans `Programmation/Passations/`
   ([`../Passations/README.md`](../Passations/README.md)), sous le nom `C<identifiant>.md`,
   selon le gabarit de
   [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §6.
2. Mettre à jour le §1 de ce fichier : état du paquet, date, lien vers la note, commit.
3. Si un jalon vient d'être évalué, renseigner son état et la décision au §1.
4. Mettre à jour la ligne « **Prochain paquet de travail** ».
5. Si un document normatif a été modifié, mettre à jour sa ligne « Dernière révision »
   (date et heure).
6. Si le déclencheur d'un point du §6 a été atteint au cours du paquet, le trancher ou le
   replanifier explicitement : un point tranché quitte le §6, sa décision rejoint le
   document qui fait foi et le journal (§8 ou annexe).

---

## 6. Dette documentaire et points ouverts

> Le **§6.1** recense les travaux connus à faire (dette) ; le **§6.2** recense les choix
> en attente d'arbitrage (points non jugés) et est le **seul registre contraignant** :
> les idées non normatives détaillées vivent dans les annexes. Un point tranché quitte le
> §6 ; sa décision rejoint le document qui fait foi et le journal (§8 ou annexe).

### 6.1 Dette documentaire

| Point | Détail | À traiter |
|---|---|---|
| Documents d'étape 2 à 6 | `Reference_Technique_Etape_2..6_*.md` non rédigés ; le référentiel global en tient lieu jusque-là, sa section d'étape étant réduite à l'objectif et au renvoi **à la création** de chaque document (référentiel §2.2, règle 7) | au démarrage de chaque étape (checklist : référentiel §2.1.1) |
| `Organisation_FromTo_Github.md` | Références aux anciens noms de fichiers mises à jour le 2026-10-30 ; reste à réviser le fond : vocabulaire « feuille de route » résiduel (§1, §10) et préséance propre du document, à intégrer à la table unique (§6.2, « Préséance unique ») | autre session |

### 6.2 Points non jugés

> Points en attente d'arbitrage (revue du 2026-10-30 et points antérieurs). Chaque ligne
> porte son critère de décision et son déclencheur ; un point sans déclencheur ne
> s'inscrit pas ici.

| Point | Options | Critère de décision | À trancher avant |
|---|---|---|---|
| Destination des contrats d'interface (`Reference_Technique_Etape_1_Capteurs.md` §7 et Annexe C) | transfert de la spécification des types au document de l'étape 2 (l'étape 1 ne conservant que §7.1 et les renvois), ou conservation pérenne dans l'étape 1 | l'étape 2 doit pouvoir spécifier ses types sans dépendre du document d'une autre étape, et le paquet `1.2` doit figer ses logs sans attendre | `C1.3` (décision consignée dans la note de passation) |
| Définition de « fini » de l'étape 1 par paquet (`Reference_Technique_Etape_1_Capteurs.md` §6) | marquer chaque critère de son paquet (`1.1`/`1.2`/`1.3`) et ajouter la correspondance `M0a–M4 ↔ C1.1/C1.2/C1.3`, ou laisser l'agrégat tel quel | aucun critère relevant de `C1.2` ne doit être exigé à `C1.1` | note de passation `C1.1` |
| Destination des livrables du paquet `1.3` | jeux de données hors dépôt + catalogue versionné (p. ex. `test/fixtures/` et répertoire externe documenté) ; chemin du parseur unique ; emplacement du rapport de caractérisation ; support de consignation des σ (document du dépôt, pas `Formulations_Kalman.pdf`) | le paquet `2.1` doit pouvoir référencer ses entrées par un chemin unique (référentiel §2.2, règle 4) | `C1.3` |
| Préséance unique entre documents | une seule table (dans `Planification` §1 ou `Suivi` §3) incluant `Organisation_FromTo_Github.md` et les documents d'ingénierie, les autres documents y renvoyant | toute contradiction entre documents doit pouvoir être tranchée par une règle écrite | avant la première divergence réelle (autre session possible) |
| Fusion des journaux de décisions | `Suivi` §8 devient un index renvoyant aux journaux des annexes ; renommages conservés au §7 seul | un fait consigné à un seul endroit | autre session |
| Cohérence §1.3 ↔ Annexe A du document d'étape 1 | placer le bloc 13 où le tableau §1.3 le dit (ou corriger le tableau), matérialiser le sous-bloc 12.3, ramener `CAP_LOG_SD_NOM` au bloc annoncé | l'Annexe A, copiée à `M0b`, ne doit diverger d'aucune table du document | `M0b` (copie du squelette) |

---

## 7. Historique des renommages (2026-10-30)

| Ancien nom | Nouveau nom | Motif |
|---|---|---|
| `Structure_Etapes_Paquets_Jalons.md` | `Planification_Etapes_Paquets_Jalons.md` | Rôle normatif de pilotage explicite |
| `Feuille_de_Route_Programmation_Kalman.md` | `Reference_Technique_Globale.md` | Référentiel technique global, et non « feuille de route » |
| `Feuille_de_Route_Etape_1.md` | `Reference_Technique_Etape_1_Capteurs.md` | Convention `Reference_Technique_Etape_<n>_<thème>` |
| `Feuille_de_Route_Etape_1_Alternatives_et_Ameliorations.md` | `Annexe_Variantes_Et_Historique_Etape_1.md` | Convention `Annexe_*` = non normatif |
| `Contrats_Interfaces_Estimation.md` | *(supprimé)* — contenu repris dans `Reference_Technique_Etape_1_Capteurs.md` §7 et Annexe C | Fichier devenu redondant ; copie gelée dans `Annexe_Archive_Contrats_Interfaces.md` |
| `Feuilles_de_Route/` (dossier) | `Pilotage/` | Cohérence avec la convention de nommage (`Planification_*`, `Reference_Technique_*`, `Annexe_*`) ; annexes regroupées dans `Annexes/` |

Jusqu'au 2026-10-30, le dossier n'était pas versionné par Git et ces renommages ne
conservaient pas d'historique automatique : ce tableau est la trace des renommages
antérieurs. Depuis cette date, la documentation est versionnée (dépôt Git à la racine de
`Programmation/`, couvrant `Pilotage/` et `Passations/`) ; l'historique complet est dans
Git.

---

## 8. Journal des décisions documentaires

| Date | Décision | Effet |
|---|---|---|
| 2026-10-30 | `Suivi_Avancement.md` devient le point d'entrée unique des sessions | Ordre de lecture et carte des documents (§2, §3) |
| 2026-10-30 | `Reference_Technique_Globale.md` devient le référentiel technique global et pointe vers un document par étape | §2.1 du référentiel |
| 2026-10-30 | Contrats d'interface réintégrés dans le document de l'étape 1 ; fichier de contrats supprimé | `Reference_Technique_Etape_1_Capteurs.md` §7 et Annexe C |
| 2026-10-30 | Déduplication : diagramme de séquencement et table des jalons conservés uniquement dans la planification | Planification §3, §5 |
| 2026-10-30 | Chaque fichier technique dispose d'une annexe variantes/historique explicitement non normative | `Annexe_Variantes_Et_Historique_*` |
| 2026-10-30 | Table de suivi §1 réduite aux colonnes d'état ; objets, points de reprise et jalons renvoyés à la planification §4 | §1 |
| 2026-10-30 | Critères de sortie d'étape : agrégat explicite (étapes multi-paquets) ou renvoi au critère de paquet (étape à paquet unique) | Planification §4 |
| 2026-10-30 | Tables « Documents associés » des documents normatifs remplacées par des renvois à la carte du présent fichier | Planification §8, Référentiel §10 |
| 2026-10-30 | Notes de passation logées dans `Programmation/Passations/` (`C<identifiant>.md`) | §5, Planification §6 |
| 2026-10-30 | Checklist de création d'un document d'étape ; le premier paquet de chaque étape porte sa rédaction | Référentiel §2.1.1 |
| 2026-10-30 | Règle de réduction de la section du référentiel global à la création d'un document d'étape | Référentiel §2.2 |
| 2026-10-30 | Paquets complétés des rubriques `Entrées` / `Hors périmètre` (`1.1` à `6.1`) | Référentiel §3 à §8 |
| 2026-10-30 | Destination des contrats d'interface de l'étape 1 consignée comme point ouvert | §6 |
| 2026-10-30 | Ligne « Dernière révision » (date et heure) ajoutée aux documents normatifs ; mise à jour en fin de session | documents normatifs |
| 2026-10-30 | Références internes du document d'étape 1 corrigées (« feuille de route maître », « document compagnon ») | `Reference_Technique_Etape_1_Capteurs.md` |
| 2026-10-30 | Dossier vide `Programmation/Capteurs/` supprimé | dépôt |
| 2026-10-30 | Rubrique « Écarts et contradictions détectés » ajoutée au gabarit de passation ; règle 4 du §7 complétée : « terminé » = critères vérifiés **et** note de passation rédigée | `Planification` §6, §7 |
| 2026-10-30 | Rubriques `Entrées` / `Hors périmètre` effectivement ajoutées aux paquets `1.2` et `1.3` (la ligne du 2026-10-30 ci-dessus devient exacte) | Référentiel §3 |
| 2026-10-30 | En-tête du document d'étape 1 conforme à la règle 5 du référentiel : rappel de l'interdiction de redéfinir un identifiant | `Reference_Technique_Etape_1_Capteurs.md` |
| 2026-10-30 | `cap_predicats.h` hébergé dans `lib/sensors/include/` ; tests natifs des fonctions pures dans `lib/sensors/test/` ; le sous-module `commun/` reste inchangé | Référence étape 1 §1.7, Annexe A ; `Organisation` §4 à §6 |
| 2026-10-30 | Noms de bancs tranchés : `bench/test_all/` et `bench/banc_cpu/` (alternatives « ou `acquisition/` », « ou `test/banc_cpu/` » supprimées) | `Organisation` §3, §6, §7 |
| 2026-10-30 | Arbitrage « destination des contrats d'interface » programmé à `C1.3`, critère de décision écrit au §6.2 | §6.2 |
| 2026-10-30 | §4 réduit à un rappel renvoyant à la planification §7 ; §6 scindé en dette (§6.1) et points non jugés (§6.2) | §4, §6 |
| 2026-10-30 | Dossier renommé `Pilotage/` ; annexes regroupées dans `Annexes/` ; archive des contrats allégée (copie intégrale conservée dans l'historique Git) ; documentation versionnée sous Git (dépôt `Programmation/`) | §3, §7 ; `Annexe_Archive_Contrats_Interfaces.md` |
| 2026-10-30 | Changelog « Références corrigées » du document d'étape 1 déplacé vers son annexe ; ancres `#paquet-<x>-<y>` généralisées dans le référentiel | Référence étape 1 §8 ; Référentiel |
