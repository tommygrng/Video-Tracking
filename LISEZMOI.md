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

## Version 5 : même précision en direct qu'en vidéo réimportée (23/10)

Constat : en direct, les vitesses étaient ~40 % trop basses (essai 18:07) alors que la même vidéo réimportée donnait des valeurs proches du Tendo. Cause : la série directe a utilisé une **échelle mm/pixel mémorisée d'une session précédente** (0,00103 m/px au lieu de ~0,0017), sans recalibrer ; la réimportation, elle, refaisait le calibrage. Le suivi lui-même était bon (0 % d'images perdues).

Corrections :
- **Échelle recalculée à chaque série** quand on suit le disque : diamètre du disque ÷ hauteur médiane du disque mesurée pendant la série (images où la barre est quasi immobile). Un calibrage ancien ou une distance téléphone–barre différente n'ont plus d'effet. L'ajustement −/+ reste appliqué (en %). L'échelle utilisée est affichée sous les résultats et exportée dans le JSON (`echelle_serie`).
- Mesure de la taille du disque sur les percentiles 1–99 % (au lieu de 2–98 %), qui sous-estimaient le diamètre d'environ 3 à 8 %.

Résultats (vidéos réimportées, calibrage automatique, sans ajustement manuel) :
- Essai 18:07 (Tendo 0,83 / 0,81 / 0,50 / 0,94) : 0,89 / 0,76 / 0,52 / 0,91 → biais moyen +0,4 %, écart par rep ±3 à 7 %. Résultat identique avec un ancien calibrage périmé forcé.
- Essai 17:21 (réf 0,80 / 0,86 / 0,78 / 0,72) : biais moyen −3,6 %.
- Squat synthétique : écarts −3,8 à +2,4 %.

## Version 6 : calibrage à la loupe et suivi par ajustement de cercle (24/10)

Essai 11:20 (développé couché, Tendo) : écarts de −10 à −40 %. Trois causes trouvées :
1. **Un autre disque vert touchait le disque suivi** (le disque de l'autre côté de la barre, vu en perspective). Le calibrage automatique a mesuré 231 px de haut au lieu de 203 : vitesses −11 %.
2. **Ce disque voisin tirait aussi le centre** : il bouge moins (plus loin de la caméra), donc l'amplitude mesurée était trop courte.
3. **Bornes de la montée** : une pause en bas comptait dans la durée de la rep (rep 2 : 0,17 m/s au lieu de 0,88).

Corrections :
- **Calibrage à la main avec loupe ×5** : poser le doigt près du bord, glisser jusqu'au bord exact, relâcher.
- **Suivi par ajustement de cercle** quand la taille du disque est connue (calibrage à la main, ou calibrage automatique si le disque est bien rond) : on cherche le cercle de ce diamètre plein de couleur à l'intérieur et vide juste à l'extérieur, position au sous-pixel. Un disque voisin ne peut plus déplacer le centre.
- **Calibrage automatique contrôlé** : si la zone n'est pas ronde (écart > 6 % entre hauteur et largeur), l'appli dit de calibrer à la main.
- **Bornes de la montée** : vitesse à 3 % du pic, interpolées entre deux images.
- **Échelle** : celle du calibrage, corrigée seulement si le disque paraît > 8 % plus grand ou plus petit (téléphone déplacé).
- **Alerte de taille** remplacée : on compare la taille du disque en bas et en haut du mouvement (vraie variation de distance), au lieu de la variation image par image (flou de bougé, reflets), qui déclenchait des alertes à tort.

Résultats (vidéos réimportées) :
- Essai 11:20, calibrage à la loupe : biais +3,0 %, 8 reps sur 10 à ±4 % (rep à 0,44 m/s : +11 %).
- Essai 18:07 (manuel ou automatique) : +4,1 / +2,5 / +2,8 / −4,5 %.
- Squat synthétique : −2,7 à −6,5 % (les pauses parfaitement immobiles de la vidéo synthétique allongent un peu la montée).

## Mise à jour
Remplacer `index.html`, puis augmenter le numéro de version dans `sw.js` (`vitesse-barre-v6` → `v7`…).
