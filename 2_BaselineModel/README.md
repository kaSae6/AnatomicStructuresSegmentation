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

**Problem classes:** Classes 1, 2, 6, 8, 14 all score DICE = 0.0000. See the notebook for per-class analysis.

## Next steps

This baseline (DICE = 0.0006) serves as the reference point for [Model Definition and Evaluation](../3_Model/README.md). The five zero-DICE classes are the primary improvement target.
