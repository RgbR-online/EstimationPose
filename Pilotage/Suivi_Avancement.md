# Suivi d'avancement — point d'entrée des sessions de programmation

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
5. Si le déclencheur d'un point du §6 a été atteint au cours du paquet, le trancher ou le
   replanifier explicitement : un point tranché quitte le §6, sa décision rejoint le
   document qui fait foi et le journal de l'annexe concernée.

---

## 6. Dette documentaire et points ouverts

> Le **§6.1** recense les travaux connus à faire (dette) ; le **§6.2** recense les choix
> en attente d'arbitrage (points non jugés) et est le **seul registre contraignant** :
> les idées non normatives détaillées vivent dans les annexes. Un point tranché quitte le
> §6 ; sa décision rejoint le document qui fait foi et le journal de l'annexe concernée.

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
| Cohérence §1.3 ↔ Annexe A du document d'étape 1 | placer le bloc 13 où le tableau §1.3 le dit (ou corriger le tableau), matérialiser le sous-bloc 12.3, ramener `CAP_LOG_SD_NOM` au bloc annoncé | l'Annexe A, copiée à `M0b`, ne doit diverger d'aucune table du document | `M0b` (copie du squelette) |

---

## 7. Versionnage Git local

> La documentation (`Programmation/` : `Pilotage/` et `Passations/`) et le code
> (`FromTo Github/`) sont suivis par **Git en local**.

Git fait foi pour le suivi des modifications et des versions, de la documentation comme du
code. Toute modification est tracée par un **commit** ; les dates de dernière révision ne
sont plus écrites dans les fichiers : la dernière modification d'un fichier et son
historique complet (dates, heures, diffs, messages) s'obtiennent par Git, par exemple
`git log -1 -- <fichier>` ou `git log <fichier>`. Les hach courts de commit consignés dans
la table du §1 et dans les notes de passation font la liaison entre paquets de travail et
versions du code.
