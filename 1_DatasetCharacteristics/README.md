# Dataset Characteristics
**[Notebook](exploratory_data_analysis.ipynb)**

## Dataset Information

### Dataset Source
- **Dataset Link:** [ENS Challenge Data – Challenge #165](https://challengedata.ens.fr/participants/challenges/165/)
- **Dataset Owner/Contact:** Pierre Manceron - Head of Science at Raidium

### Dataset Characteristics
- **Number of Observations:** 2,000 CT scan slices (training set)
- **Number of Features:** Each image is a 2D grayscale grid of pixels; the number of features per image equals width × height (256 x 256).

### Target Variable/Label

- **Label Name:** Organ IDs (from `annotated_labels.json`)
- **Label Type:** Multi-label classification
- **Label Description:** For each CT slice, the label is the set of anatomical organs visible in that image. The prediction task is to identify which organs are present in a given scan slice.
- **Label Values:** 54 unique integer organ IDs, each corresponding to a specific anatomical structure. 
- **Label Distribution:** Partially annotated — each image has between a minimum (0) and maximum (27) number of organ labels, with a mean of approximately 10-11 organs per image. All organs appear in similar frequency in the dataset, from 18.7% to 21.8%. Labels are incomplete by design: some organs visible in a scan may not be annotated.

### Feature Description

- **Pixel intensity values:** Each image is a 2D array of grayscale values ranging from 0 (black) to 255 (white). In CT scans, darker pixels correspond to air or soft tissue and brighter pixels to denser structures such as bone.
- **Image mode:** All images are stored in grayscale format (PIL mode `L`) — no RGB channels are present.
- **Mean intensity (per image):** Average pixel brightness across the image. The dataset skews toward low values, consistent with CT imaging where most pixels represent air or soft tissue.
- **Std intensity (per image):** Pixel contrast/variance. A wide spread across images suggests varying levels of structural detail and possibly different scanner protocols.
- **Min / Max intensity (per image):** Range of pixel values per image. Most images use the full 0–255 range.
- **Width / Height:** Spatial dimensions of each image in pixels. 

## Exploratory Data Analysis

The exploratory data analysis is conducted in the [exploratory_data_analysis.ipynb](exploratory_data_analysis.ipynb) notebook, which includes:

- Data loading and initial inspection of both image files and the annotation JSON
- Statistical summaries of image dimensions and pixel intensity distributions
- Missing and corrupt image detection (none found)
- Label structure analysis: decoding the sparse multi-label encoding and building a binary organ presence matrix
- Label frequency distribution across the dataset and per-image label count statistics
- Data visualisation including intensity histograms, brightness vs. contrast scatter plots, organ frequency bar charts, and labels-per-image distributions
