# Model Definition and Evaluation

**[Notebook](model_definition_evaluation)**

## Baseline reference

The watershed baseline (see [2_BaselineModel/](../2_BaselineModel/README.md)) scored a mean DICE of **0.0006** on the 200-image balanced validation split. All models defined in this notebook are evaluated on the same split for a fair comparison.

| Model | Mean DICE (val) | Notes |
|---|---|---|
| Watershed baseline | 0.0006 | Parameter-free; no training required |
| This model | TBD | Fill in after training |

Any supervised model that learns organ-specific appearance from labelled examples should substantially exceed 0.0006. The five classes that scored 0.0000 at baseline (Classes 1, 2, 6, 8, 14) are the primary target for improvement.
