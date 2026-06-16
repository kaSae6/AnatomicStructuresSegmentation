# Anatomic structures segmentation with missing labels

## Repository Link

https://github.com/kaSae6/AnatomicStructuresSegmentation

## Description

CT scans provide highly accurate 3D images of the human body (up to 0.5 mm resolution), enabling us to grasp the human anatomy. The aim of this challenge is to automatically segment the anatomical structures of the human body, as well as tumors, on a CT scan. In other words, the aim is to identify the visible shapes on a CT scan which have no exhaustive annotations.

[Short project description here. Briefly summarize the problem you are trying to solve **and the approach you're taking**.]

### Task Type

Image Classification / Segmentation

### Results Summary

## Results

| Model | Val DICE | Notes |
|---|---|---|
| Watershed baseline | ~0.001 | Classical edge-based; balanced subset, 200 val images |
| U-Net (next step) | TBD | Supervised; ResNet encoder, 256×256, CE + Dice loss |

## Lessons Learned So Far

**Data imbalance.** The 54 organ classes are highly imbalanced: common structures (lungs, liver) appear in nearly every training image while rare structures (adrenal glands, small glands) appear in fewer than 50. Training on all 2000 images uncritically causes a model to optimise for the dominant classes and effectively ignore the rare ones. We addressed this by downsampling over-represented classes so no class contributes more than 2× the rarest-class count, producing a `balanced_indices.npy` file with the selected image indices.

**Redundant samples.** Even within a class, many images show nearly identical anatomy (consecutive CT slices). Including all redundant images inflates training time without adding information and can push the model toward memorising common configurations. The balanced subset selection simultaneously removes this redundancy by capping per-class image counts.

**Resolution.** A side-by-side comparison (256×256 vs 128×128 vs 64×64) showed that thin and tubular structures — vessels, bronchi, small intestine — remain visible at 128×128 but collapse to single-pixel artefacts or disappear entirely at 64×64. Training at 64×64 would make it structurally impossible for the model to learn to segment these structures. We therefore use 256×256 (native resolution) throughout.

#### Key Insights
- **Most Important Features:** Raw pixel intensities at 256×256 resolution; spatial context via convolutional receptive fields
- **Model Strengths (watershed):** Fast, no training required, produces coherent regions
- **Model Limitations (watershed):** Labels carry no semantic meaning; CT soft-tissue HU overlap defeats edge-based segmentation
- **Next Step:** Supervised U-Net with CE + Dice loss on the balanced subset

## Documentation

1. **[Literature Review](https://github.com/kaSae6/AnatomicStructuresSegmentation/blob/main/0_LiteratureReview/README.md)**
2. **[Dataset Characteristics](1_DatasetCharacteristics/exploratory_data_analysis.ipynb)** — EDA + class imbalance analysis + balanced subset selection
3. **[Watershed Baseline](notebooks/02_baseline_watershed.ipynb)** — classical baseline with per-class DICE evaluation
4. **[Raidium Starter (Colab)](2_BaselineModel/raidium_baseline_colab.ipynb)** — U-Net baseline for GPU training on Colab
5. **[Model Definition and Evaluation](3_Model/model_definition_evaluation)**
6. **[Presentation](4_Presentation/README.md)**

## Cover Image

![Project Cover Image](CoverImage/cover_image.png)
