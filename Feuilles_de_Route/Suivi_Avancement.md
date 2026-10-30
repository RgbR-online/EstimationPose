# Suivi d'avancement — point d'entrée des sessions de programmation

*Dernière révision : 2026-10-30 09:49*

> **Lire ce fichier en premier, avant tout autre document.**
> Il indique où en est le projet, quel paquet exécuter ensuite, et où trouver chaque
> information. Il ne contient **aucune spécification** : les règles sont dans la
> planification, le contenu technique dans les documents de référence.

---

## 1. État d'avancement

> Objets, points de reprise et jalons : [`Planification_Etapes_Paquets_Jalons.md`](./Planification_Etapes_Paquets_Jalons.md) §4,
> qui fait foi. Ce tableau ne porte que l'état d'avancement.

| Paquet | État | Point de reprise | Note de passation | Jalon | Commit |
|---|---|---|---|---|---|
| `1.1` | `à faire` | `C1.1` | — | `J1` | — |
| `1.2` | `à faire` | `C1.2` | — | — | — |
| `1.3` | `à faire` | `C1.3` | — | `J2` | — |
| `2.1` | `à faire` | `C2.1` | — | — | — |
| `2.2` | `à faire` | `C2.2` | — | `J3` | — |
| `3.1` | `à faire` | `C3.1` | — | — | — |
| `3.2` | `à faire` | `C3.2` | — | — | — |
| `3.3` | `à faire` | `C3.3` | — | `J4` | — |
| `4.1` | `à faire` | `C4.1` | — | — | — |
| `5.1` | `à faire` | `C5.1` | — | `J5` | — |
| `6.1` | `à faire` | `C6.1` | — | `J6` | — |

États admis : `à faire`, `en cours`, `bloqué`, `terminé`. Un paquet n'est `terminé` que si
ses critères d'acceptation sont vérifiés **et** que la note de passation existe.

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
| [`Annexe_Variantes_Et_Historique_Etape_1.md`](./Annexe_Variantes_Et_Historique_Etape_1.md) | Variantes écartées et historique des décisions de l'étape 1 | **non — ne pas programmer dessus** | uniquement sur demande explicite |
| [`Annexe_Variantes_Et_Historique_Globale.md`](./Annexe_Variantes_Et_Historique_Globale.md) | Variantes écartées et historique du référentiel global | **non — ne pas programmer dessus** | uniquement sur demande explicite |
| [`Annexe_Archive_Contrats_Interfaces.md`](./Annexe_Archive_Contrats_Interfaces.md) | Archive gelée de l'ancien fichier de contrats (supprimé) | **non — ne pas programmer dessus** | uniquement pour traçabilité |

Documents d'ingénierie (hors dossier, font autorité) : `Materiel.pdf`,
`Implementation.pdf`, `Formulations_Kalman.pdf`, `Materiel/Datasheets/`,
`Elec/Schéma électrique capteurs.pdf`.

`Reference_Technique_Etape_2_Kalman.md` à `Reference_Technique_Etape_6_Validation.md`
n'existent pas encore : ils sont créés au démarrage du premier paquet de leur étape.

---

## 4. Règles d'une session de programmation

1. Travailler sur **un seul paquet** : celui désigné au §1.
2. S'arrêter au **point de reprise** du paquet ; ne pas enchaîner sur le suivant.
3. **Aucun identifiant nouveau** : les paquets, points de reprise et jalons sont ceux de la
   planification.
4. Les **livrables et critères d'acceptation** sont ceux de `Reference_Technique_Globale.md`.
5. **Aucune duplication de code** entre bancs, bibliothèques et firmware : la bibliothèque
   est la source unique de vérité.
6. Les éléments **hors périmètre** listés par le paquet ne sont pas implémentés.
7. Ne jamais utiliser un fichier `Annexe_*` comme spécification.
8. En cas de contradiction entre deux documents : appliquer la préséance du §1 de la
   planification et **signaler l'écart** dans la note de passation.

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

---

## 6. Dette documentaire et points ouverts

| Point | Détail | À traiter |
|---|---|---|
| Documents d'étape 2 à 6 | `Reference_Technique_Etape_2..6_*.md` non rédigés ; le référentiel global en tient lieu jusque-là (section réduite à l'objectif et au renvoi à la création de chaque document — Référentiel §2.2) | au démarrage de chaque étape (checklist : Référentiel §2.1.1) |
| Destination des contrats d'interface (`Reference_Technique_Etape_1_Capteurs.md` §7 et Annexe C) | Trancher à la création de `Reference_Technique_Etape_2_Kalman.md` : transfert de la spécification des types au document de l'étape 2 (l'étape 1 ne conservant que §7.1 et les renvois), ou conservation pérenne dans l'étape 1 | à la création du document d'étape 2 |
| `Organisation_FromTo_Github.md` | Références aux anciens noms de fichiers mises à jour le 2026-10-30 ; reste à réviser le fond du document | autre session |

---

## 7. Historique des renommages (2026-10-30)

| Ancien nom | Nouveau nom | Motif |
|---|---|---|
| `Structure_Etapes_Paquets_Jalons.md` | `Planification_Etapes_Paquets_Jalons.md` | Rôle normatif de pilotage explicite |
| `Feuille_de_Route_Programmation_Kalman.md` | `Reference_Technique_Globale.md` | Référentiel technique global, et non « feuille de route » |
| `Feuille_de_Route_Etape_1.md` | `Reference_Technique_Etape_1_Capteurs.md` | Convention `Reference_Technique_Etape_<n>_<thème>` |
| `Feuille_de_Route_Etape_1_Alternatives_et_Ameliorations.md` | `Annexe_Variantes_Et_Historique_Etape_1.md` | Convention `Annexe_*` = non normatif |
| `Contrats_Interfaces_Estimation.md` | *(supprimé)* — contenu repris dans `Reference_Technique_Etape_1_Capteurs.md` §7 et Annexe C | Fichier devenu redondant ; copie gelée dans `Annexe_Archive_Contrats_Interfaces.md` |

Le dossier n'étant pas versionné par Git, ces renommages ne conservent pas d'historique
automatique : ce tableau est la trace de référence.

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
