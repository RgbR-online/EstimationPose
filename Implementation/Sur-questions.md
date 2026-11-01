Le PCB a déjà été conçu. Des petites modifications matérielles sont envisageables mais doit-être le plus limitées possibles.
## \subsubsection{Stratégie hybride compilation / initialisation}
Je pense que je préfère le masquage statique à condition d'avoir un temps de compilation assez faible (environ <5 min). Est-ce que ce sera le cas ?
## \subsection{Gestion de la Perte de Signal et Watchdog Sensoriel}
- L'information Fault/Disconnect devra être transmise à l'utilisateur
	- par exemple via la LED de la Teensy, mais attention elle est déjà utilisé pour autre chose dans le code complet
	- peut-être qu'il sera nécessaire de rajouter une led externe qu'en on passera au code complet
- Comment déterminer \gamma ?
## \subsection{Sorties des Centrales Inertielles : Angles d'Euler vs Quaternions}
- Erreur cmps12 n'est pas basé sur BNO080/085 mais sur BNO055 il me semble
- Le cmps12 ne donne que les angles d'Euler normallement
## \subsection{Données Accélérométriques : Avec ou Sans Gravité ?}
Option 2 à privilégier à condition d'évaluer le temps de calcul supplémentaire nécessaire
## \subsection{Cadencement Matériel : Polling, Interruption et Buffers}
- Analyse de la doc pour savoir quels capteurs ont un pin interrupteur et un buffer. Savoir si c'est recommandé ou pas
- Vérifier sur PCB si j'ai mis les interrupteurs (a priori non)
## \subsection{Modélisation Géométrique et Effet de Bras de Levier}
- Est ce que l'équation `a_{IMU} = a_{CG} + \dot{\omega} \times r_{IMU/G} + \omega \times (\omega \times r_{IMU/G})` a été prise en compte dans le fichier Théorie.pdf ?
- D'autres capteurs où j'aurais fait l'oubli du bras de levier ?
- Ai-je également oublier de mentionner `R_{sensor}^{body}` pour certains capteurs qui en ont besoin ?
## \subsection{Perturbations Environnementales : Moteurs et Magnétisme}
- Vérifier présence du filtre passe-base sur les différents IMU
- Pourquoi 40 Hz ?
- Quid du baromètre ? 
	- Perturbation via suppression générer par les moteurs
	- Possibilités de réglage intrinsèque au capteur ou dans Kalman ?
## \subsection{Précision Numérique et Robustesse Algorithmique}
Une grande partie de cette section ne devrait pas plutôt être dans Théorie.pdf ?