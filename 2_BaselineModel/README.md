# Baseline model

**[Notebook](baseline_model.ipynb)**

## 1. Choice of model

**Model:** Watershed segmentation (`skimage.segmentation.watershed`)

Watershed treats a grayscale image as a topographic surface and floods it
from local intensity minima, separating regions at gradient ridge lines.
Chosen because it requires no training, is fully deterministic, and
establishes a conservative lower bound: it cannot assign semantic organ
labels, so any supervised model that learns organ identity should
substantially outperform it.

## 2. Feature selection

No feature engineering applied. Watershed operates on raw pixel intensities
with three fixed pre-processing steps from the challenge starter code:
Sobel edge filter → median denoising (disk 2) → gradient threshold for seed
markers. No learned features, no dimensionality reduction.

## 3. Implementation

Each image processed independently:
1. Sobel edges on raw grayscale image
2. Median filter (disk radius 2) for denoising
3. Gradient threshold (< 20) → seed markers
4. `scipy.ndimage.label` on markers
5. `watershed(edges, markers, compactness=0.0001)`

**Data split:** balanced subset of 412 images (cap = 2× rarest class = 12).
First 200 → validation; remaining 212 → unused for this baseline.

## 4. Evaluation

**Metric:** mean DICE score — `2|A∩B| / (|A|+|B|)` per class per image,
`nanmean` over classes then images. DICE chosen over pixel accuracy because
background dominates every image; a background-only predictor scores >90%
accuracy but DICE = 0.

**Result:** mean DICE = **0.0008** on the 200-image validation split.

**Cross-validation:** N/A — watershed has no learnable parameters.

**Problem classes:** Classes 1, 2, 6, 8, 14 all score DICE = 0.0000.
See the notebook for the per-class bar chart.

**Practical relevance:** DICE > 0.7 is clinically acceptable for organ
segmentation. The 0.0008 score confirms that geometric segmentation without
semantic understanding is insufficient — supervised learning is required.

## Next steps

This baseline (DICE = 0.0008) is the reference point for
[Model Definition and Evaluation](../3_Model/README.md).
Classes 1, 2, 6, 8, 14 are the primary improvement target.
