# Vitesse Barre

Prototype de mesure de la vitesse de barre avec la caméra du téléphone.
Il compte les reps et mesure l'amplitude, la vitesse moyenne concentrique, la vitesse maximale et la perte de vitesse.
Tout est calculé sur le téléphone : aucun envoi de vidéo, aucun compte. L'appli fonctionne hors ligne une fois installée.

## Mise en ligne (GitHub Pages)
1. Créer un nouveau dépôt public, par exemple `vitesse-barre`.
2. Y déposer les 6 fichiers à la racine : `index.html`, `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `LISEZMOI.md`.
3. Aller dans Settings › Pages › Deploy from a branch › `main` / `(root)` › Save.
4. Après 1 à 2 minutes, l'appli est disponible à l'adresse `https://<utilisateur>.github.io/vitesse-barre/`.

L'accès à la caméra exige une adresse https : GitHub Pages la fournit.

## Installation
- iPhone : Safari › Partager › « Sur l'écran d'accueil ».
- Android : Chrome › ⋮ › « Installer l'application ».
- À la première ouverture, autoriser l'accès à la caméra.

## Utilisation au rack
1. Coller une pastille ronde de couleur vive (rose, vert ou orange fluo, 4 à 6 cm) au centre du disque extérieur.
2. Poser le téléphone fixe, de profil, objectif à hauteur des disques, à 1,5 à 3 m. Le trajet complet de la barre doit rester dans l'image.
3. Allumer la caméra, puis « Cibler la pastille » et toucher la pastille à l'écran.
4. « Calibrer sur le disque » : toucher le bord haut puis le bord bas du disque. Le diamètre est réglé à 45 cm par défaut.
5. « Démarrer la série ». Un bip retentit à chaque rep, et la série s'arrête seule après 6 s sans mouvement.
6. Renseigner le mouvement et la charge, puis « Enregistrer la série ».

## Export
- **Exporter l'historique (CSV)** : une ligne par rep, avec date, mouvement, charge, amplitude, vitesses, perte de vitesse et indicateurs de qualité.
- **Exporter la trajectoire (CSV)** : hauteur, vitesse et déplacement horizontal image par image, pour la série affichée (utile pour comparer à un capteur linéaire).

## Validation réalisée
Test sur une vidéo de synthèse de 5 reps de 45 cm, avec des vitesses connues (0,41 à 0,56 m/s), à 30 images/s :
- 5 reps sur 5 détectées ;
- amplitude à ±0,3 cm près ;
- vitesse moyenne concentrique à ±4 % près ;
- vitesse maximale à ±2 % près.

Ce test valide les calculs, mais pas les conditions réelles (lumière, flou de bougé, perspective). Une comparaison avec un capteur linéaire sur de vraies séries reste nécessaire.

## Mise à jour
Remplacer `index.html`, puis augmenter le numéro de version dans `sw.js` (`vitesse-barre-v1` → `v2`…).
