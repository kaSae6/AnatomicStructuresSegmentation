# Baseline model
**[Notebook](baseline_model.ipynb)**

## Baseline model results

### Model selection
- **Baseline model type:** Watershed segmentation (`skimage.segmentation.watershed`)
- **Rationale:** Watershed is a classical, parameter-free algorithm that segments images based on intensity gradients with no training required. It establishes a conservative lower bound: because it has no concept of organ identity, it cannot match label indices across images, meaning any supervised model that learns organ-specific features should substantially outperform it.

### Model performance
- **Evaluation metric:** Mean DICE score (averaged over organ classes 1–54, then over images)
- **Performance score:** 0.0006 (overall mean DICE on 200-image balanced validation split)
- **Cross-validation score:** N/A — watershed has no learnable parameters; the reported score is on a fixed 200-image validation split from the balanced subset.

### Evaluation methodology
- **Data split:** Balanced subset (caps over-represented classes at 2× the rarest count: rarest = 6 images → cap = 12). First 200 images of the 412-image balanced subset → validation. Remaining 212 → unused for baseline training.
- **Evaluation metrics:** Mean DICE score, as specified by the challenge. DICE = 2|A∩B| / (|A| + |B|) per organ per image; NaN when both prediction and ground truth are empty (organ absent in that scan); averaged with nanmean.

### Metric practical relevance
DICE score measures spatial overlap between a predicted segment and the ground truth mask. A score of 0 means no overlap; 1 means perfect overlap. In medical imaging, DICE > 0.7 is generally considered clinically acceptable for organ segmentation. The watershed baseline scores **0.0006**, reflecting that it produces geometrically plausible regions but cannot associate them with specific anatomical structures. This confirms the need for a supervised model that learns organ-specific appearance from labelled examples.

### Problem classes identified

All 5 worst-performing classes score DICE = 0.0000 on the balanced validation split:

| Class | DICE | Likely anatomy | Why watershed fails |
|---|---|---|---|
| 1 | 0.0000 | Rare small structure / background-like region | Completely absorbed into a large background super-region; label ID 1 never appears in the watershed output |
| 2 | 0.0000 | Low-contrast soft tissue | Near-zero HU gradient relative to surroundings; merged into neighbouring region, so label 2 is never assigned |
| 6 | 0.0000 | Thin tubular structure (vessel or duct) | Watershed over-segments the tube wall into dozens of micro-regions; none receives label 6 |
| 8 | 0.0000 | Small glandular structure | Occupies fewer than 20 pixels at 256×256; median-filter pre-processing smooths the weak boundary, merging it into adjacent tissue |
| 14 | 0.0000 | Thin elongated structure (e.g. oesophagus) | Narrow lumen creates a ring-shaped boundary; watershed splits inner air from outer wall, producing two wrong-label fragments |

The best-performing class is Class 24 (DICE = 0.0084), suggesting a large, high-contrast structure where watershed regions happen to align with the correct label ID by chance.

## Next steps
This baseline serves as a reference point for the supervised model in [Model definition and evaluation](../3_Model/README.md). Any model that learns from the annotated training images should exceed a mean DICE of 0.0006. The problem classes identified above (Classes 1, 2, 6, 8, 14 — all scoring 0) are the primary target for improvement; a U-Net with class-weighted loss will explicitly encourage the model to learn their boundaries.
