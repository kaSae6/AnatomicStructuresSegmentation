# Raidium Challenge 2025 — CT Scan Organ Segmentation

**Contributors - Karolina Saegner & Vaishali Vinod**

## Repository Link

https://github.com/kaSae6/AnatomicStructuresSegmentation

## Description

Semi-supervised segmentation of anatomical structures from 2D CT scan slices.
The challenge provides 2,000 training images and 500 test images (256×256 px,
grayscale) across 54 organ classes, labelled at the pixel level. The goal is
per-pixel organ labelling, evaluated with mean DICE score.

## Task type

Image Segmentation

## Results summary

### Best model

- **Model:** MiT-B2 U-Net (Mix Transformer encoder + U-Net decoder)
- **Metric:** Mean DICE score
- **Score:** 0.1204  (150× watershed baseline)

### Model comparison

| Model | Val DICE | vs Baseline |
|---|---|---|
| Watershed (baseline) | 0.0008 | 1× |
| ShallowSegNet (from scratch) | 0.0000 | 0× |
| U-Net small (from scratch) | 0.0024 | 3× |
| U-Net+ wide (from scratch) | 0.0705 | 88× |
| ResNet-50 U-Net | 0.1122 | 140× |
| EfficientNet-B4 U-Net | 0.0934 | 117× |
| ResNet-50 U-Net++ | 0.1194 | 149× |
| **MiT-B2 U-Net** | **0.1204** | **150×** |

### Key findings

- Transfer learning (ImageNet pre-trained encoders) was the single biggest
  improvement — from DICE 0.07 to 0.11-0.12
- Class-weighted loss was essential — rare organs were invisible without it
  (inverse-frequency weights, max ~13.6×)
- A wider from-scratch U-Net (U-Net+) already closes most of the gap to the
  baseline-scale pre-trained encoders, showing capacity mattered as much as
  pre-training for the early jump
- EfficientNet-B4 underperformed the ResNet-50 and MiT-B2 encoders despite
  its efficiency advantages — compound scaling transfers less well to
  medical images at this resolution
- DICE 0.12 is meaningful progress over the near-zero baseline but far from
  clinical acceptance (DICE > 0.7)

## Setup

Requires Python 3.10–3.12 and [uv](https://docs.astral.sh/uv/).

```
uv sync
uv run python -m ipykernel install --user \
    --name ct-segmentation --display-name "CT Segmentation (uv)"
jupyter notebook
```

Select kernel **CT Segmentation (uv)** when opening any notebook.

> **GPU training:** the pre-trained encoder models were trained on Google
> Colab (T4 GPU) — see `3_Model/model_definition_evaluation_gpu.ipynb` and
> `3_Model/raidium_pretrained_4models.ipynb`. The watershed baseline and
> from-scratch models run on CPU.

## Documentation

1. [Literature Review](0_LiteratureReview/README.md)
2. [Dataset Characteristics](1_DatasetCharacteristics/README.md)
3. [Baseline Model](2_BaselineModel/baseline_model.ipynb)
4. [Model Definition and Evaluation](3_Model/README.md)
5. [Presentation](4_Presentation/README.md)

## Cover Image

![Project Cover Image](CoverImage/cover_image.png)
