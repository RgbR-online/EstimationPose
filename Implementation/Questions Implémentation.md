## Plus implémentation algorithmique

- Comment gérer le choix du filtre de Kalman (cinématique simple, etc) ? Fichier de config ? Eviter que ce soit que du conditionnel dans le code exécuté -> peut-être fichier de config pour la compilation
- Comment gérer l'absence de capteur (= capteur qu'on a enlevé pour l'ensemble de l'exécution du code) ? Pê définie dans un fichier de config de compilation ou qqc comme ça
- Comment gérer une perte de capteur (= capteur qui n'est pas absent, mais dont on ne reçoit plus le signal) ?
- Paramètres des capteurs
	- Précision à déterminer pour matrice de covariance du bruit de mesure
	- Phase de calibration nécessaire des capteurs et du Kalman (notamment pour matrice de passage) ?
	- Quelles sorties ont utilisent pour les centrales inertielles ?
		- Pour l'orientation et ces dérivées, récupération en angle d'euler ou en quaternion ? Puis conversion ?
		- Pour la position et ces dérivées, récupération de l'accélération avec ou sans gravité ? Puis conversion ?
		- Propre à chaque centrale inertielle utilisé
		- Pê définie dans un fichier de config de compilation (à définir pour chaque capteur)
	- Fréquences de mesure des capteurs à déterminer
		- A déterminer via doc puis exp
		- Sont-elles réglables ?
		- Lancement de nouvelles mesures automatique ou manuelle ?
		- Buffer ou non ?
- Comment gérer la calibration ?
	- Quand est-ce que chaque calibration est nécessaire (différent pour chaque type de calibration) ?
		- Changement de position des balises au sol
		- Changement de position des capteurs sur le dirigeable
		- Débranchement d'un capteur
		- Extinction de la teensy 4.1
		- Enlèvement de la carte SSD
- Comment gérer les différences de fréquences de mesure des capteurs ? 1 stratégies à choisir parmi 2 :
	1) On lance la phase de correction à chaque réception d'une nouvelle mesure -> comment dire que les autres mesures ne sont pas bonnes ? Problème : toute la phase de prédiction (calcul fait pour tous capteurs) lancée fréquemment pour un seul capteur
	2) Si des fréquences de plusieurs capteurs sont proches, on attend que tous les capteurs avec des cadences similaires ont donnés leur mesure (avec time limit) pour lancer la phase de prédiction -> quand dire que des fréquences sont similaires ? comment bien fixé le time limit ?
	3) Y a-t-il mieux ?
- Comment gérer le cas où un capteur n'a pas donné de mesure depuis longtemps ?
- Comment gérer le cas où il y a plusieurs capteurs du même type (ça va être le cas, mais normalement c'est simple)
- Comment gérer la fréquence de prédiction par rapport à celle des capteurs/celle de la phase de prédiction ?
	- Ce que je pense
		- Temps d'exécution de la phase de prédiction << temps d'exécution de la phase de correction (peut importe le type de capteur)
		- On peut cependant régler le déclenchement
	- Stratégie que j'envisage (à vérifier s'il y a mieux)
		- Si fréquence max beaucoup plus élevée pour la phase de prédiction, alors on peut la faire tourner plusieurs fois sans phase de correction. Dès qu'une nouvelle série de données est en attente, on lance la phase de correction (après fin de la dernière instruction)
- Comment gérer le tempo de ces phases par rapport aux autres fonctionnalités gérer par la teensy 4.1 ?
- Comment tirer le plein potentiel de la teensy 4.1 ? 
	- attention consommation, on est sur batterie. Mais a priori consommation teensy << consommation moteurs
	- dans un contexte où on aimerait faire tourner des phases le plus fréquemment possible (phase de prédiction), mais que d'autres phases sont peut-être plus prioritaire (commande des moteurs)
- Faire attention aux approximations (par ex, quaternion) en fonction du pas de temps de calcul
## Plus implémentation physique
- Comment bien calibrer les capteurs et les matrices de passages ?
- Positionnement des capteurs
	- Estimation nécessaire de leur position par rapport au centre de gravité
	- Eviter la singularité des cmps 12 associée aux angles d'Euler
- Vérification de la précision des capteurs dans leur environnement d'utilisation (moteur qui tourne, échauffement, etc)