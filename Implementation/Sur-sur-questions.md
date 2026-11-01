## \subsubsection{Signalement de l'état de défaut à l'opérateur}
La led13 est déjà utilisé mais sert de gestion multi-erreur (un signal spécifique pour chaque type d'erreur avec une alternance si plusieurs erreurs ou qqc du style). La question est de savoir si cette fonctionnalité est déjà saturer ou si je peux rajouter la gestion des Fault/Disconnect des capteurs
Supprime l'option 1 (mentionne juste la télémétrie en dernier recours)
## \subsubsection{Rejet des mesures aberrantes et détermination rigoureuse de \texorpdfstring{$\gamma$}{gamma}}
Cette section ne devra pas plutôt être présente dans Théorie.pdf ?
## \subsection{Sorties des Centrales Inertielles : Angles d'Euler vs Quaternions}
Il te manquait un peu de contexte.
- J'ai deux type d'IMU : le cmps12 et l'Adafruit IMU BNO085
- Le cmps12 ne donne que les angles d'Euler (pas les quaternions) et l'accélération avec gravité (pas sans gravité), à l'inverse de l'adafruit
## \subsubsection{Stratégie de traitement selon le capteur utilisé}
Pour cette section s'appuyer les stratégies définies dans Théorie.pdf en fonction de la configuration utilisé