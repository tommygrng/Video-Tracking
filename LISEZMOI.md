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
1. Choisir la cible :
   - **disque de couleur** (bumper vert, bleu, rouge, jaune) : aucune préparation, l'appli suit le disque entier ;
   - **disques noirs ou gris** : coller une pastille ronde de couleur vive (rose, vert ou orange fluo, 4 à 6 cm) au centre du disque extérieur.
2. Poser le téléphone fixe, de profil, objectif à hauteur des disques, à 1,5 à 3 m. Le trajet complet de la barre doit rester dans l'image.
3. Allumer la caméra, puis « Cibler la couleur » et toucher le disque (ou la pastille) à l'écran. Un cercle bleu doit le suivre.
4. Calibrer :
   - **disque suivi** : « Calibrer automatiquement », barre immobile une seconde ; le diamètre est réglé à 45 cm par défaut ;
   - **pastille** : « Calibrer sur le disque », puis toucher le bord haut et le bord bas du disque.
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

## Corrections après le premier essai réel (développé couché, disque Eleiko vert)
- Les disques de couleur mate (peu saturée) sont maintenant détectés ; avant, ils étaient écartés par un seuil de saturation trop strict.
- Ajout du calibrage automatique sur la hauteur du disque suivi.
- La sortie et le retour au rack ne sont plus comptés comme des reps (déplacement surtout horizontal, ou amplitude très inférieure aux autres reps).
- Résultat sur la vidéo d'essai : 4 reps détectées (1 + 3), cible suivie sur 100 % des images.

## Mode essai (validation avec un appareil de référence)
1. Cocher **« Filmer la série »** sous les boutons : la vidéo de la caméra est enregistrée pendant la série.
2. Faire la série avec l'appareil de référence branché en parallèle (capteur linéaire, centrale inertielle…).
3. Dans les résultats, ouvrir **« Valeurs de l'appareil de référence »** : indiquer l'appareil, puis saisir rep par rep la vitesse moyenne (et si possible la vitesse max et l'amplitude). L'écart avec l'appli s'affiche aussitôt.
4. Enregistrer la série, puis exporter :
   - **Télécharger la vidéo** et **Télécharger l'essai (JSON)**, ou **Partager vidéo + essai** sur téléphone ;
   - le JSON contient les réglages, la couleur ciblée, le calibrage, la position détectée image par image, les reps calculées et les valeurs de référence.
5. Envoyer la vidéo et le JSON pour l'amélioration de l'algorithme.

## Analyser une vidéo enregistrée
« Analyser une vidéo enregistrée… » ouvre une vidéo du téléphone, par exemple filmée avec l'appareil photo à 60 ou 120 images/s, plus précis que la caméra du navigateur. Ensuite :
- placer le curseur sur une image où la barre est immobile ;
- cibler la couleur et calibrer ;
- lancer « Analyser la vidéo ».

La lecture est ralentie (×0,25 par défaut) pour analyser chaque image. Un avertissement apparaît si des images ont été sautées.

## Version 4 : corrections après les essais avec référence (développé couché, 23/10)
Constat sur l'essai avec référence : l'appli sous-estimait la vitesse moyenne d'environ 13 % (≈ 0,1 m/s). Deux causes :
- **Calibrage faussé.** Des pixels de même couleur hors du disque (plafond vert pâle, éléments du décor) étaient comptés avec lui. Le disque paraissait donc plus grand (280 px au lieu d'environ 240), et toutes les distances et vitesses étaient trop petites.
- **Contour instable.** Pour la même raison, le cercle affiché était grand et changeait de taille.

Corrections :
- **Suivi limité à un cercle de la taille du disque** : les pixels de même couleur hors du disque sont ignorés, ce qui stabilise le centre et la taille.
- **Taille de la cible estimée dès le ciblage**, par détection du disque entier autour du point touché. Un avertissement apparaît si la zone reconnue n'est pas ronde (lettre ou reflet touché).
- **Recherche de la cible perdue** sur une fenêtre de la taille du disque, et non plus sur une petite case qui pouvait accrocher le décor.
- **Contour affiché de taille fixe**, égal au disque calibré : il doit épouser le bord du disque. Des boutons − / + permettent de l'ajuster finement, ce qui corrige le calibrage.
- **Alerte « disque ovale »** quand le téléphone n'est pas bien en face du disque.
- Les mouvements d'amplitude anormalement grande (téléphone repris en main) ne sont plus comptés.
- Option « temps de pose court » (Android) pour réduire le flou de bougé.

Résultat sur l'essai de 17 h 21, ré-analysé avec la nouvelle version :

| Rep | Appli | Référence | Écart |
|---|---|---|---|
| 1 | 0,81 m/s | 0,80 | +1,0 % |
| 2 | 0,84 m/s | 0,86 | −2,1 % |
| 3 | 0,82 m/s | 0,78 | +4,7 % |
| 4 | 0,68 m/s | 0,72 | −5,6 % |

Biais moyen −0,5 %, écart absolu moyen 3,4 % (contre −13 % de biais avant).

Bonnes pratiques de calibrage :
- calibrer quand le disque est **entièrement visible** et immobile (barre en haut, pas au rack derrière un montant) ;
- toucher la **surface colorée** du disque, loin des lettres ;
- vérifier que le contour bleu épouse le bord du disque.

## Mise à jour
Remplacer `index.html`, puis augmenter le numéro de version dans `sw.js` (`vitesse-barre-v4` → `v5`…).
