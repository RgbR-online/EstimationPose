# ANNEXE — Variantes et historique de l'étape 2

> ## AVERTISSEMENT — DOCUMENT NON NORMATIF
> ## NE PAS UTILISER COMME SUPPORT DE PROGRAMMATION
>
> Ce fichier conserve les **pistes écartées**, les **variantes** et l'**historique des
> décisions** de l'étape 2. Il n'est **jamais** une spécification : aucune de ses lignes ne
> doit être implémentée. Le support de programmation de l'étape 2 est
> [`Reference_Technique_Etape_2_Kalman.md`](../Reference_Technique_Etape_2_Kalman.md) ;
> le point d'entrée des sessions est [`Suivi_Avancement.md`](../Suivi_Avancement.md).
>
> Les statuts `Adopté` sont rappelés pour mémoire : la décision qui fait foi est celle du
> document de référence. En cas de contradiction, **le document de référence fait foi**.

> **Document compagnon** de
> [`Reference_Technique_Etape_2_Kalman.md`](../Reference_Technique_Etape_2_Kalman.md).
> Planification : [`Planification_Etapes_Paquets_Jalons.md`](../Planification_Etapes_Paquets_Jalons.md).
> Les écarts `E-2-nn` cités ci-dessous sont définis au §0.3 du document de référence ; les
> repères internes `N0` à `N4` au §6.1 du même document.

---

## 1. Vue d'ensemble

| Idée | Statut | Motif / suite |
| :--- | :--- | :--- |
| `n` du référentiel = dimension d'erreur | **Adopté** | `Formulations` §2.5.2 (nominal 16) et §2.6.1 (nominal 17) ; écart `E-2-09` (référence étape 2 §1.4) |
| Correction séquentielle par voie | **Adopté** | `Implementation` §5.1.1 proscrit le synchrone ; équivalence séquentielle pour $R$ bloc-diagonale (référence étape 2 §2.6, `E-2-04`) |
| Mesure de cap CMPS12 scalaire | **Adopté** | `Implementation` §2.1.3, Option 3 = configuration de référence (référence étape 2 §2.6.3, `E-2-05`) |
| Aucun biais inertiel dans l'état | **Adopté** | `Formulations` §2.5.5 ; ZUPT en amont (référence étape 2 §3.2, `E-2-06`) |
| Force spécifique brute dans l'état `a_b` | **Adopté** | Une seule soustraction de pesanteur, en navigation (référence étape 2 §2.0.5, `E-2-03`) |
| Innovation d'attitude locale $\hat q^*\otimes q_m$ | **Adopté** | Cohérente avec le reset $q\leftarrow q\otimes\delta q$ (référence étape 2 §2.3, `E-2-02`) |
| `quatToEuler` corrigé | **Adopté** | $\theta=+\arcsin(2(q_wq_y-q_zq_x))$ ; le `-` du §2.2.4 du PDF contredit son propre $R(q)$ (référence étape 2 §2.0.4, `E-2-01`) |
| Situation 1 en variante Euler uniquement | **Adopté** | Référentiel §4 et `Implementation` §1.2.1 : quatre types, pas cinq (§3 ci-dessous) |
| Situation 3 en variante Euler | **Rejeté** | Référentiel §4 : seul `INERTIEL_AVEC_BIAIS` (variante B) est listé |
| Forme de Joseph comme chemin nominal | **Rejeté** | Coût ×3 (`Implementation` §5.5.1) ; conservée comme oracle de test (`FILTER_JOSEPH`) |
| Normalisation après propagation **et** après reset | **Adopté** | Réconcilie `Implementation` §5.5.2 et `Formulations` §2.2.3 (référence étape 2 §1.6) |
| Repliement Euler sur les trois angles | **Adopté** | Étend le §2.4.1 du PDF, qui ne cite que $\psi$ (`E-2-08`) |
| Temps logique de rejeu (`dt` passé en argument) | **Adopté** | Déterminisme hôte/cible ; aucune horloge dans `lib/kalman` |
| Enregistrement binaire de rejeu (`.krep`) | **Adopté** | Un seul analyseur CSV (paquet `1.3`), aucun parseur CSV sur cible |
| Tolérances fixées par mesure (quantile $99{,}9\,\%$) | **Adopté** | Aucune tolérance « à l'œil » ; `V-2-13` |
| Double `platformio.ini` par bench | **Rejeté** | `bench/test_kalman/` garde un seul `platformio.ini`, environnements successifs |
| Copie d'Eigen dans `lib/kalman/` | **Rejeté** | Dépendance épinglée, jamais dupliquée (référentiel §10.4, §10.6) |
| Filtrage passe-bas logiciel dans le noyau | **Rejeté** | Décale la phase et fausse l'innovation ; la coupure 40 Hz reste matérielle (`Implementation` §4.1) |
| Recalage du biais baro par moyenne glissante | **Rejeté** | Non observable sans $Z$ absolu ; passe par l'état de la Situation 3 |
| Observabilité $(Z,b)$ : gel du biais sans $Z$ absolu | **Adopté** | Séparation fréquentielle (`Formulations` §2.6.4) ; `V-2-15` |
| Estimation conjointe en un seul filtre « tout-en-un » (Euler + quaternion commutables) | **Rejeté** | Aiguillage dynamique proscrit (`Implementation` §1.2.1) ; quatre types statiques |

---

## 2. Écarts entre sources — décisions et justification

Les quatre écarts **bloquants** ou **majeurs** de la référence étape 2 (§0.3) ont été tranchés
avant `N0`. Leur justification complète n'est pas répétée ici ; ce qui suit enregistre le
raisonnement qui a conduit à trancher, pour éviter qu'une future session ne rouvre la
question sans preuve nouvelle.

### 2.1 Signe du tangage (`E-2-01`)

Le `Formulations` §2.2.4 donne $\theta=-\arcsin(2(q_wq_y-q_zq_x))$. Deux contrôles
indépendants du même document montrent que ce signe est faux : pour un quaternion d'axe
pur $q=[\cos\frac{\theta}{2},0,\sin\frac{\theta}{2},0]$, la formule rend $-\theta$, alors
que $R(q)$ du §2.2.2 redonne exactement $R_{b\to\text{nav}}(\Omega)$ du §2.2.1, dont
$R[2][0]=-\sin\theta$, dont l'extraction du §2.3.3 ($\theta=-\arcsin R[2][0]$) rend
$+\theta$. Les formules $\phi$ et $\psi$ du §2.2.4 sont, elles, les formes standards : seul
le signe de $\theta$ diverge. **Décision** : forme corrigée opposable au §2.0.4 de la
référence, encadrée par le test `T1` (aller-retour sur $10^5$ triplets) et par la cohérence
avec l'extraction matricielle.

### 2.2 Ordre de l'innovation ESKF (`E-2-02`)

Le paradigme retenu (`Formulations` §2.2.3) définit l'erreur **locale** :
$q=\hat q\otimes\delta q(\delta\theta)$, réinjectée par $\hat q\leftarrow\hat q\otimes
\delta q$. Dans cette convention, la mesure donne $\delta q_m=\hat q^*\otimes q_m$ — c'est
ce que les §2.4.2 et §2.5.2 du même document utilisent. La variante `Implementation`
§2.1.2 ($\delta q=z_q\otimes\hat q^{-1}$) définit une erreur **globale** ; elle exigerait
une réinjection $\hat q\leftarrow\delta q\otimes\hat q$ et une linéarisation de cap
différente. Les deux formes ne sont pas interchangeables ; mélanger innovation d'un côté et
reset de l'autre produit un signe de correction faux sur l'attitude. **Décision** : erreur
locale partout (référence étape 2 §2.3, §2.6.3, §2.6.4).

### 2.3 Soustraction de la pesanteur (`E-2-03`)

Si l'état `a_b` portait une accélération déjà corrigée en repère corps (§2.5.4 du PDF),
l'équation de propagation (§2.5.2 du même document, $v\mathrel{+}=(Ra^b-\mathbf g_n)dt$)
soustrairait deux fois la pesanteur. La seule lecture auto-cohérente des deux équations,
et celle d'`Implementation` §2.2/§2.1.2, est : `a_b` = force spécifique brute $f^b$, une
seule soustraction en navigation. **Conséquences opposables** (référence étape 2 §2.0.5) :
voie IMU sur `SH2_ACCELEROMETER`, la voie `LIN` ignorée par le noyau, invariant de repos
$\lVert\hat a_b\rVert\approx g$ (test `T2`).

### 2.4 Correction synchrone ou par voie (`E-2-04`)

Le vecteur de mesure $z\in\mathbb{R}^{13}$ et la matrice $H$ $13\times15$ du §2.5.4 du PDF
décrivent une correction **consolidée**. `Implementation` §5.1.1 montre que la
consolidation synchrone est proscrite (réinjection de la prédiction, blocage sur le capteur
le plus lent) et §5.1.3 retient les mises à jour partielles asynchrones. La réconciliation
est exacte et non approximative : pour des bruits de mesure mutuellement indépendants
($R$ bloc-diagonale), le traitement séquentiel des blocs est égal à la mise à jour conjointe
(test `T6`). **Conséquences** : seuils de porte par voie ($m$ du bloc, pas du vecteur
complet), `H` implémentée comme blocs de lignes, test `T5` de reconstitution de $H$.

### 2.5 Cap du CMPS12 (`E-2-05`)

Le `Formulations` propose de convertir le triplet CMPS12 en quaternion et d'appliquer une
innovation d'attitude $m=3$ ; `Implementation` §2.1.3 (Option 3, configuration de référence)
n'exploite que le cap scalaire. **Décision** : voie scalaire $m=1$ en configuration de
référence, avec la linéarisation cohérente avec l'erreur locale,
$\partial\psi/\partial\delta\theta=[0,\sin\hat\phi/\cos\hat\theta,\cos\hat\phi/\cos\hat\theta]$
(troisième ligne de $R_c$) ; la voie $m=3$ reste disponible en filtre Euler et pour un
CMPS12 seul. Le garde-fou de tangage (§1.6, règle 5 de la référence) borne le facteur
$1/\cos\theta$.

### 2.6 Autres écarts (résumé)

`E-2-06` (biais gyro hors état, ZUPT en amont), `E-2-07` (bloc $P_0$ mal étiqueté :
$\sigma^2_{\omega}I_3$ et $\sigma^2_{a}I_3$, pas de « biais »), `E-2-08` (repliement des
trois angles), `E-2-09` ($n$ = erreur), `E-2-10` (une seule table $\gamma(m,\alpha)$),
`E-2-11` (acceptation forcée bornée par $R$ gonflée puis voie en défaut), `E-2-12`
(`ControlState` en `double`, contrat figé, coût mesuré en `5.1`), `E-2-13` (ligne dupliquée
du tableau d'organisation du paquet `2.2`, consolidée au §1.7 de la référence) : les décisions
sont au §0.3 de la référence et ne sont pas rediscutées ici.

---

## 3. Modèles écartés

### 3.1 Situation 1, variante B (ESKF nominal 7 / erreur 6)

Le `Formulations` §2.4.2 expose une variante ESKF de la Situation 1. Elle n'est **pas**
retenue : le référentiel §4 et `Implementation` §1.2.1 n'énumèrent que quatre types, et
`CINEMATIQUE_MINIMAL` y est explicitement la variante Euler ($n=6$) ; le §2.4.3 du PDF
recommande d'ailleurs la variante A pour les deux capteurs. La variante B reste
**documentée** dans le PDF et ne doit pas être implémentée sans une révision préalable du
référentiel et de `Implementation` §1.2.1.

### 3.2 Situation 3, variante A (Euler, 16 états)

Même motif : le référentiel et `Implementation` §1.2.1 ne conservent que la variante B pour
la Situation 3.

### 3.3 Biais gyroscopique et accélérométrique dans l'état (21 états)

Écartée par le §2.5.5 du PDF (auto-calibration des modules, recalages absolus à haute
cadence, coût temps réel). Le biais issu du ZUPT (`Implementation` §3.2, §3.3) est appliqué
**en amont** comme offset de conditionnement ; il n'existe aucun état de biais inertiel dans
`lib/kalman`.

### 3.4 Correction synchrone consolidée

Proscrite (`Implementation` §5.1.1). Voir §2.4 ci-dessus.

### 3.5 Voie cap vectorielle par quaternion complet du CMPS12

Conservée comme **repli** (filtre Euler, CMPS12 seul) ; écartée en configuration de
référence. Voir §2.5 ci-dessus.

### 3.6 Filtrage passe-bas logiciel dans le noyau

La coupure à 40 Hz est matérielle et déjà appliquée en amont (`Implementation` §4.1). Un
passe-bas redondant dans le noyau introduirait un déphasage supplémentaire sans réduire le
bruit au-delà de ce que le filtre de Kalman fait déjà : son rôle est précisément d'intégrer
un bruit coloré correctement pondéré.

### 3.7 Recalage du biais barométrique par moyenne glissante

Écarté : la moyenne glissante ne sépare pas la dérive lente de la dynamique verticale sans
$Z$ absolu, n'est pas couverte par la porte $\chi^2$, et duplique un rôle que l'état
$b_{\text{baro}}$ de la Situation 3 porte déjà. En l'absence de $Z$ absolu, le biais est
gelé (référence étape 2 §2.4, `V-2-15`).

---

## 4. Journal des décisions

| Date | Décision | Référence |
| :--- | :--- | :--- |
| 2026-11-01 | `Reference_Technique_Etape_2_Kalman.md` créé (paquets `2.1`/`2.2`), selon la checklist du référentiel §2.1.1 | Référence étape 2 |
| 2026-11-01 | Vérification des sources avant report : quatre écarts bloquants/majeurs tranchés (`E-2-01` à `E-2-04`), huit mineurs consignés (`E-2-05` à `E-2-12`, `E-2-13`) | Référence étape 2 §0.2, §0.3 |
| 2026-11-01 | Dimensions : `n` du référentiel = dimension d'erreur ; dimensions nominales et index de $\delta x$ figés par type | Référence étape 2 §1.4 |
| 2026-11-01 | Frontière du filtre : mesures conditionnées en entrée, aucune horloge, aucune dépendance Arduino | Référence étape 2 §1.1, §3 |
| 2026-11-01 | Convention de pesanteur : `a_b` = force spécifique brute, une seule soustraction en navigation | Référence étape 2 §2.0.5 |
| 2026-11-01 | Blocs d'observation par voie ; seuil de porte par voie ; équivalence séquentielle testée | Référence étape 2 §2.6, §4.5 (`T5`, `T6`) |
| 2026-11-01 | Biais baro : signe normatif $z=Z+b+v$, gel sans $Z$ absolu | Référence étape 2 §2.4 |
| 2026-11-01 | Rejeu temps logique, enregistrement binaire commun hôte/cible, références hôte dans `bench/test_kalman/reference/` | Référence étape 2 §4.2, §4.3, Annexe C |
| 2026-11-01 | Tolérances hôte/cible fixées par mesure, jamais choisies | Référence étape 2 §4.4, `V-2-13` |
| 2026-11-01 | Blocs « Organisation du dépôt » des paquets `2.1`/`2.2` transférés du référentiel §4 vers la référence étape 2 (§1.7), conformément à la règle 7 du §2.2 du référentiel ; les objectifs, périmètres, livrables et critères de paquet restent au référentiel (table d'autorité, planification §1) | `Reference_Technique_Globale.md` §4 ; référence étape 2 §1.7 |
| 2026-11-01 | Statut **Rédigé** porté au tableau §2.1 du référentiel ; document inscrit dans la carte des documents de `Suivi_Avancement.md` §3 | `Reference_Technique_Globale.md` §2.1 ; `Suivi_Avancement.md` §3 |
| 2026-11-01 | Doutes `V-2-02` à `V-2-15` enregistrés au registre (la ligne `V-2-01` existait déjà) | [`Suivi_Valeurs_Et_Sources.md`](../Suivi_Valeurs_Et_Sources.md) §3 |
| — | Consignation de la création dans la note de passation | **à faire à `C2.1`** (le paquet `2.1` n'est pas exécuté) |

---

## 5. Pistes ouvertes (non retenues, non programmables)

| Piste | Condition d'activation |
| :--- | :--- |
| Forme exacte de $\Delta q$ ($\cos,\sin$) au lieu du premier ordre | Si un scénario réel montre $\lVert\omega\rVert dt$ approchant $10^{-1}$ rad (jamais le cas documenté : $\le5\cdot10^{-3}$) |
| État de vitesse Doppler GNSS (extérieur, $z$ à 15 composantes) | Si l'étape 6 requiert un vol extérieur ; nécessite une révision du référentiel (GPS hors périmètre) |
| Inflation de $R_{\text{baro}}$ par consigne moteur | Paquet `3.3` (les jeux `1.3` ne contiennent pas la consigne de gaz) |
| Double tampon de `CapteursState` | Étape 4 (`Implementation`), sans changement d'interface |
| Tests NEES (cohérence du filtre) en plus des NIS | Si la revue `J3` exige une validation statistique plus forte |
| Table de seuils $\gamma$ générée par script au build | Si un niveau de risque par voie devient nécessaire ; une table `constexpr` suffit aujourd'hui |

---

## 6. Comment promouvoir une idée

1. Vérifier qu'elle ne contredit pas les invariants de la référence étape 2 (aucune horloge,
   aucune dépendance Arduino, aucune allocation dynamique, corrections par voie, erreur
   locale).
2. Décrire le besoin concret et le coût de maintenance.
3. Ajouter les `static_assert` et les tests natifs correspondants (`T1` à `T18`).
4. Mettre à jour le document de référence de l'étape, puis déplacer la ligne du statut
   « piste » vers « adopté » dans ce document, avec la date.
