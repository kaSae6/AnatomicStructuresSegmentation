# Model definition and evaluation

**Notebooks:** [model_definition_evaluation_gpu.ipynb](model_definition_evaluation_gpu.ipynb) (from-scratch models) ·
[raidium_pretrained_4models.ipynb](raidium_pretrained_4models.ipynb) (pre-trained encoder models)

## 1. Model selection

Seven models across two notebooks, in increasing order of sophistication:

| Model | Type | Key idea |
|---|---|---|
| ShallowSegNet | Supervised 3-layer CNN | Learns organ appearance from labelled images; minimal architecture, no skip connections |
| U-Net (small) | Supervised encoder-decoder | Skip connections preserve boundary detail |
| U-Net+ (wide) | Supervised encoder-decoder | Wider (base=48 vs 32) + class-weighted loss |
| ResNet-50 U-Net | Pre-trained encoder + U-Net decoder | ImageNet transfer learning |
| EfficientNet-B4 U-Net | Pre-trained encoder + U-Net decoder | Compound-scaled CNN encoder |
| ResNet-50 U-Net++ | Pre-trained encoder + U-Net++ decoder | Nested dense skip connections isolate decoder impact |
| MiT-B2 U-Net | Pre-trained transformer encoder + U-Net decoder | Long-range context, targets thin/tubular organs |

**Rationale for progression:** the first three models (trained from scratch)
establish how far architecture and capacity alone can go; the last four swap
in ImageNet-pretrained encoders via `segmentation_models_pytorch` to test
whether transfer learning closes the remaining gap.

## 2. Feature engineering

**From-scratch models (ShallowSegNet, U-Net, U-Net+):** single-channel
256×256 grayscale input, normalised to [0, 1]. No handcrafted features —
convolutional layers learn relevant filters directly.

**Pre-trained models:** grayscale CT slices replicated to 3 channels and
normalised with ImageNet statistics (mean `[0.485, 0.456, 0.406]`,
std `[0.229, 0.224, 0.225]`), since all four encoders expect RGB input.

**Class weights:** inverse-frequency weights, computed from the training
split and used in the loss for U-Net+ and all four pre-trained models
(min 0.0006, max 13.5847) so rare organs receive a proportionally larger
gradient signal.

## 3. Hyperparameter tuning

| Hyperparameter | ShallowSegNet | U-Net (small) | U-Net+ (wide) | Pre-trained (all 4) |
|---|---|---|---|---|
| Input resolution | 256×256 | 256×256 | 256×256 | 256×256 |
| Base channels | — | 32 | 48 | — (per encoder) |
| Epochs | 15 | 30 | 30 | 30 (5 frozen + 25 fine-tune) |
| Loss | CrossEntropy | CrossEntropy | Dice (class-weighted) | Dice (class-weighted) |
| Training strategy | single phase | single phase | single phase | two-phase (frozen encoder → differential LR) |
| Encoder LR (phase 2) | — | — | — | 3e-5 |
| Decoder LR | — | — | — | 3e-4 |
| Environment | Colab T4 GPU | Colab T4 GPU | Colab T4 GPU | Colab T4 GPU |

**Two-phase training** (pre-trained models only): epochs 1–5 train the
decoder only with the encoder frozen, preventing the randomly-initialised
decoder from destroying the pre-trained features with large early
gradients; epochs 6–30 unfreeze the encoder with a 10× lower learning rate
than the decoder for fine-tuning.

## 4. Implementation

**Data pipeline:** images loaded in numerical order; labels from
`y_train.csv` (transposed to `(N, H×W)`, reshaped to `(N, H, W)`). Split:
first 200 images → validation, remaining 1,800 → training.

**Environment:** both notebooks are designed to run on Google Colab with a
T4 GPU (`Runtime → Change runtime type → T4 GPU`), reading the dataset from
Google Drive. Pre-trained encoder weights download automatically via
`segmentation_models_pytorch` on first run.

## 5. Evaluation metrics

Mean DICE score: `2|A∩B| / (|A|+|B|)` per class per image, `nanmean` over
classes then images. Used instead of pixel accuracy because background
dominates every image — a background-only predictor scores >90% pixel
accuracy but DICE = 0.

## 6. Comparative analysis

All models evaluated on the same 200-image validation split.

| Model | Val DICE | Δ vs baseline |
|---|---|---|
| Watershed (baseline) | 0.0008 | — |
| ShallowSegNet | 0.0000 | 0× |
| U-Net (small) | 0.0024 | 3× |
| U-Net+ (wide) | 0.0705 | 88× |
| ResNet-50 U-Net | 0.1122 | 140× |
| EfficientNet-B4 U-Net | 0.0934 | 117× |
| ResNet-50 U-Net++ | 0.1194 | 149× |
| MiT-B2 U-Net | 0.1204 | 150× |

### Best model

**MiT-B2 U-Net** — DICE 0.1204, 150× the watershed baseline.
Submission file: `3_Model/submission_best.csv`

### Key findings

Transfer learning was the single biggest lever: swapping in ImageNet-pretrained
encoders lifted DICE from 0.07 (best from-scratch model) into the 0.09–0.12
range without changing the decoder. Class-weighted Dice loss was necessary
throughout — without it, rare organ classes contributed almost nothing to
the gradient and were effectively never learned. Two-phase training (encoder
frozen for 5 epochs, then fine-tuned at a 10× lower learning rate than the
decoder) avoided destabilising the pre-trained features early in training.
EfficientNet-B4 underperformed both ResNet-50 variants and MiT-B2 despite its
efficiency advantages on natural images, suggesting its compound scaling
transfers less well to CT images at this resolution. The MiT-B2 transformer
encoder and the ResNet-50 U-Net++ decoder variant were close runners-up,
consistent with long-range context and richer decoder skip connections both
helping with the harder, thin-structure organ classes.

## Baseline reference

See [2_BaselineModel/](../2_BaselineModel/README.md). Watershed DICE = 0.0008.
