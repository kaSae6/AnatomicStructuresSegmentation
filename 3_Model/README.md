# Model definition and evaluation

**[Notebook](model_definition_evaluation.ipynb)**

## 1. Model selection

Three models of increasing sophistication, each adding one capability over
the previous:

| Model | Type | Key idea |
|---|---|---|
| K-Means (k=10) | Unsupervised clustering | Spatial features fix watershed's label-assignment problem |
| ShallowSegNet | Supervised 3-layer CNN | Learns organ appearance from labelled images |
| U-Net | Supervised encoder-decoder | Skip connections preserve boundary detail |

## 2. Feature engineering

**K-Means:** `[normalised intensity, x/W, y/H]` per pixel. Spatial coordinates
prevent merging of distant same-intensity regions.

**CNN models:** single-channel 128×128 grayscale input normalised to [0, 1].
Inverse-frequency class weights in the loss function up-weight rare organs.

## 3. Hyperparameter tuning

| Hyperparameter | K-Means | ShallowSegNet | U-Net |
|---|---|---|---|
| Input resolution | 256×256 | 128×128 | 128×128 |
| Clusters / classes | 10 | 55 | 55 |
| Learning rate | — | 1e-3 | 1e-3 |
| LR schedule | — | None | StepLR ×0.5 / 10 epochs |
| Epochs | — | 10 | 20 |
| Batch size | — | 8 | 8 |
| Loss | — | CE (weighted) | CE (weighted) |

## 4. Implementation

Images loaded with `cv2.imread` in numerical order; labels from `y_train.csv`
(mandatory transpose). Balanced subset: 412 images (cap = 2× rarest class = 12).
First 200 → validation; 212 → training.

`torch._dynamo.disable()` set globally — `torch.compile()` requires MSVC
(`cl.exe`) on Windows, which is unavailable without Visual Studio.

## 5. Evaluation metrics

Mean DICE score: `2|A∩B| / (|A|+|B|)` per class per image, `nanmean` over
classes then images. Used instead of pixel accuracy because background
dominates every image — a background-only predictor scores >90% accuracy
but DICE = 0.

## 6. Comparative analysis

| Model | Val DICE | Δ vs baseline |
|---|---|---|
| Watershed (baseline) | 0.0006 | — |
| K-Means (k=10, spatial) | 0.0007 | +0.0001 |
| ShallowSegNet | 0.0021 | +0.0015 |
| U-Net | TBD | TBD |

All models evaluated on the same 200-image balanced validation split.
Known limitation: 128×128 training collapses thin structures; classes 6, 8,
14 expected near-zero DICE at this resolution.

## Baseline reference

See [2_BaselineModel/](../2_BaselineModel/README.md). Watershed DICE = 0.0006.
