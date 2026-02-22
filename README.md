# Assemblage Panoramique d’Images — Détection SIFT, Appariement et Stitching par Homographie

Ce projet assemble plusieurs images qui se chevauchent pour former un panorama. Il utilise SIFT pour les points d’intérêt, le test de Lowe pour filtrer les correspondances et RANSAC pour estimer une homographie robuste.

---

## Objectifs et contenu

1. Détecte des points d’intérêt (SIFT) sur chaque image.
2. Fait l’appariement entre les images avec le test de Lowe.
3. Calcule une homographie (transformation perspective) via RANSAC.
4. Applique une transformation perspective pour aligner les images.
5. Assemble les images pour créer un panorama.

---

## Description des scripts

### Classe Panorama (SIFT, Lowe, RANSAC, homographie)
`panorama_stitcher.py`

Ce fichier contient la classe `Panaroma` avec la chaîne de traitement complète.

**Méthodes principales :**

- `image_stitch(images, lowe_ratio, max_Threshold, match_status)`  
  Point d’entrée : prend deux images (ou une image et un panorama déjà assemblé) et renvoie le panorama (et éventuellement la visualisation des correspondances).
- `detect_feature_and_keypoints(image)`  
  Détection SIFT des keypoints et calcul des descripteurs.
- `get_all_possible_matches()`  
  Appariement par force brute (distance euclidienne entre descripteurs).
- `get_all_valid_matches()`  
  Filtrage avec le test de Lowe : on garde une correspondance si  
  `distance(best) < lowe_ratio * distance(second_best)`.
- `compute_homography()`  
  Calcul de l’homographie avec RANSAC via `cv2.findHomography()`.
- `get_warp_perspective()`  
  Transformation perspective de l’image source.
- `draw_matches()`  
  Dessine les correspondances entre les deux images pour la visualisation.

**Paramètres :**

- `lowe_ratio` (par défaut 0.75) : plus bas = correspondances plus strictes.
- `max_Threshold` (par défaut 4.0) : seuil RANSAC pour les inliers.
- `match_status` : si `True`, renvoie aussi l’image des correspondances.

---

### Script interactif d'assemblage multi-images
`panorama_builder.py`

- Demande le nombre d’images à assembler.
- Demande les chemins des images dans l’ordre (gauche → droite).
- Redimensionne les images (par ex. `imutils.resize` à 400 px de largeur ou hauteur).
- Assemble les images en chaîne :
  - pour 2 images : un seul `image_stitch` ;
  - pour plus : on assemble deux dernières, puis on ajoute les précédentes une par une.
- Affiche :
  - les correspondances (option `match_status=True`),
  - le panorama final.
- Sauvegarde dans un dossier `output/` :
  - `matched_points.jpg`
  - `panorama_image.jpg`

**Dépendance supplémentaire :** `imutils` (pour `resize`).  
Si `imutils` n’est pas utilisé ailleurs, on peut le remplacer par `cv2.resize` directement.

---

## Installation des dépendances

```bash
pip install -r requirements.txt
```

`requirements.txt` doit contenir `opencv-contrib-python` (et non uniquement `opencv-python`) car SIFT est dans les modules contrib.

Pour `panorama_builder.py` :

```bash
pip install imutils
```

---

## Comment l'utiliser

### Depuis Python

```python
from panorama_stitcher import Panaroma
import cv2

img1 = cv2.imread('image1.jpg')
img2 = cv2.imread('image2.jpg')

panorama = Panaroma()
result = panorama.image_stitch([img1, img2])

cv2.imshow('Panorama', result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Script interactif

```bash
python TP_5_ELBAZ/panorama_builder.py
```

Puis suivre les instructions (nombre d’images, chemins des fichiers).

---

## Arborescence du projet

```
TP5_ELBAZ/
├── TP_5_ELBAZ/
│   ├── panorama_stitcher.py    # Classe Panorama (SIFT, Lowe, RANSAC)
│   └── panorama_builder.py     # Script interactif d'assemblage
├── requirements.txt
└── README.md
```

---

## Informations pratiques

- Utiliser `opencv-contrib-python` pour avoir SIFT.
- Les images doivent se chevaucher (30–50 % d’overlap conseillé).
- Pour plus de 2 images, `panorama_builder.py` assemble de droite à gauche et construit le panorama progressivement.

---

**Auteur :** ELBAZ
