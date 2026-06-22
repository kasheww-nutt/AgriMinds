# Rice Handoff

Last verified: 23 June 2026  
Status: legacy experiment only; corrected runner not created

## Files and contract

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Rice/rice.ipynb` |
| Legacy class map | `crops/Rice/rice_classes.json` |
| Local data folder | `Data/prepro_pbl_dataset/rice` |

Architecture direction: ResNet18.

```text
0 = Bacterial Leaf Blight
1 = Brown Spot
2 = Healthy Rice Leaf
3 = Leaf Blast
```

## Historical notebook evidence

The saved notebook output reports approximately 1,798 train, 385 validation, and 386 test images. Historical validation accuracy increased from approximately 85.71% in phase one to 93.25% in phase two. Saved test accuracy and macro-F1 were approximately 88%.

Rice is one of the weaker historical crops and needs careful class-level analysis.

## Known legacy risks

- the original shared-transform split bug was patched later, but saved outputs belong to the historical workflow;
- non-stratified image-level split;
- incomplete seeding;
- phase two did not preserve the phase-one global champion;
- BatchNorm behaviour was not controlled;
- combined regularization and TTA obscure which choices helped;
- validation improvement did not translate fully to test performance;
- visually similar disease classes may require stronger evidence than top-1 accuracy;
- hardcoded paths and unvalidated severity logic remain.

## Required corrected work

1. Create the exact Rice class configuration.
2. Use the shared ResNet18 runner and manifest rules.
3. Report the full confusion matrix and per-class recall, especially among Blast, Brown Spot, and Bacterial Blight.
4. Treat phase two as an experiment, not an assumption.
5. Use calibrated single-pass inference and record uncertainty coverage.
6. Do not use ensemble gains as a substitute for correcting the base pipeline.
7. Update this handoff with every corrected result and failure mode.

## Next-chat start point

Read the shared documents first. Do not reopen model architecture or ensemble deployment unless new controlled evidence supports it. Dataset auditing remains out of scope without explicit permission.

