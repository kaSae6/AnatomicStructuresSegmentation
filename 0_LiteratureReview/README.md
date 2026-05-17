# Literature Review

Approaches or solutions that have been tried before on similar projects.

**Summary of Each Work**:

- **Source 1**: Marginal loss and exclusion loss for partially supervised multi-organ segmentation

  - **[Link](https://www.sciencedirect.com/science/article/abs/pii/S1361841521000256)**
  - **Objective**: Learn a single multi-organ segmentation network from a union of partially labeled datasets, where each dataset covers only a subset of organ classes
  - **Methods**: Two novel loss functions are proposed: a marginal loss that handles "merged" background labels using marginal probabilities, and an exclusion loss that leverages the mutual exclusiveness of non-overlapping organs to promote learning on pixels where only partial annotations exist
  - **Outcomes**: Experiments on a union of five benchmark datasets covering liver, spleen, kidneys, and pancreas demonstrate a conspicuous performance improvement for state-of-the-art methods without introducing any extra computation. 
  - **Relation to the Project**: Directly addresses the core challenge setup — multiple CT datasets where each scan only has some organs annotated. 



- **Source 2**: Multiorgan Segmentation from Partially Labeled Datasets with Conditional nnU-Net
  - **[Link](https://pubmed.ncbi.nlm.nih.gov/34311262/)**
  - **Objective**: To develop a framework that simultaneously learns multiorgan knowledge by training on a union of partially labeled CT datasets, addressing the impracticality of acquiring fully annotated data for every organ.
  - **Methods**: A 3D conditional nnU-Net is proposed, built on the nnU-Net backbone, which introduces a conditioning strategy by feeding auxiliary organ-class information into the decoder as an additional input layer, allowing the model to identify the target organ at the pixel level. Training combines Dice loss and focal loss.
  - **Outcomes**: The methos is able to segment liver, pancreas, spleen, and kidney across benchmark datasets sourced from different institutions with non-overlapping annotations.
  - **Relation to the Project**: The project dataset uses the same sparse, non-overlapping partial label structure, where each image carries only a subset of organ ID (similar to the paper). The conditioning strategy offers a direct architectural solution for training on such data without discarding unlabeled organ information.
