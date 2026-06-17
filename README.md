# Anatomic structures segmentation with missing labels

## Repository Link

https://github.com/kaSae6/AnatomicStructuresSegmentation

## Description

Semi-supervised segmentation of anatomical structures from 2D CT scan slices.
The challenge provides 800 partially annotated and 1,200 unannotated grayscale
images (256×256 px). The goal is to identify and separate visible anatomical
structures per image using pixel-level labels, evaluated with mean DICE score.

## Task type

Image Segmentation

## Setup

Requires Python 3.10–3.12 and [uv](https://docs.astral.sh/uv/).

```
uv sync
uv run python -m ipykernel install --user \
    --name ct-segmentation --display-name "CT Segmentation (uv)"
jupyter notebook
```

Select kernel **CT Segmentation (uv)** when opening any notebook.


## Results summary

### Best model performance

- **Best model:** U-Net (4-level encoder-decoder with skip connections)
- **Evaluation metric:** Mean DICE score (averaged over 54 organ classes, then over images)
- **Final performance:** TBD

### Model comparison

| Model | Val DICE | Notes |
|---|---|---|
| Watershed (baseline) | 0.0006 | Parameter-free, no training |
| K-Means (k=10, spatial) | TBD | Unsupervised |
| ShallowSegNet | TBD | 3-layer CNN, 10 epochs |
| U-Net | TBD | 4-level, 20 epochs, class weights |

- **Baseline performance:** 0.0006 (watershed)
- **Improvement over baseline:** TBD after supervised model training
- **Best alternative model:** TBD

### Key insights

- **Most important features:** Spatial pixel coordinates (x, y) improve unsupervised
  clustering; inverse-frequency class weights improve supervised learning on rare organs
- **Model strengths:** U-Net's skip connections recover fine spatial detail lost
  during downsampling, which is critical for thin structures
- **Model limitations:** Training at 128×128 collapses thin structures (vessels,
  oesophagus) to 1–2 pixels; classes 6, 8, 14 remain near-zero DICE
- **Practical impact:** DICE > 0.7 is clinically acceptable for organ segmentation;
  the current models establish a learning trajectory from a near-zero baseline

## Documentation

1. [Literature Review](0_LiteratureReview/README.md)
2. [Dataset Characteristics](1_DatasetCharacteristics/exploratory_data_analysis.ipynb)
3. [Baseline Model](2_BaselineModel/baseline_model.ipynb)
4. [Model Definition and Evaluation](3_Model/model_definition_evaluation.ipynb)
5. [Presentation](4_Presentation/README.md)


## Cover Image

![Project Cover Image](CoverImage/cover_image.png)