# Dataset Characteristics

## Dataset Information

### Dataset Source
- **Dataset Link:** [ENS Challenge Data – Challenge #165](https://challengedata.ens.fr/participants/challenges/165/)
- **Dataset Owner/Contact:** Pierre Manceron - Head of Science at Raidium

### Dataset Characteristics
- **Number of Observations:** 2,000 CT scan slices (training set), 500 CT scan slices (test set)
- **Number of Features:** Each image is a 2D grayscale grid of pixels; the number of features per image equals width × height = 256 × 256 = 65,536 pixels.

### Target Variable/Label

- **Label Name:** Per-pixel organ ID (from `y_train.csv`)
- **Label Type:** Pixel-level (semantic) segmentation
- **Label Description:** Each CT slice has a corresponding 256×256 label mask giving, for every pixel, the anatomical organ it belongs to. The prediction task is to assign one of 55 integer class IDs (background + 54 organs) to every pixel in the image.
- **Label Values:** Integer IDs 0–54 — 0 is background, 1–54 each correspond to a specific anatomical structure.
- **Label Distribution:** Class frequency varies substantially across the 54 organ classes and is heavily background-dominated at the pixel level; see the per-class DICE chart in [2_BaselineModel/baseline_model.ipynb](../2_BaselineModel/baseline_model.ipynb) for the actual per-class breakdown used to build the class-weighted loss.

### Feature Description

- **Pixel intensity values:** Each image is a 2D array of grayscale values ranging from 0 (black) to 255 (white). In CT scans, darker pixels correspond to air or soft tissue and brighter pixels to denser structures such as bone.
- **Image mode:** All images are stored in grayscale format (PIL mode `L`) — no RGB channels are present.
- **Width / Height:** Spatial dimensions of each image: 256 × 256 pixels.
