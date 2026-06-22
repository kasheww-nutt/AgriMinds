# Tomato Handoff

Last verified: 23 June 2026  
Status: legacy experiment only; corrected runner not created

## Files and contract

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Tomato/tomato.ipynb` |
| Legacy class map | `crops/Tomato/tomato_classes.json` |
| Local data folder | `Data/prepro_pbl_dataset/tomato` |

Architecture direction: ResNet18.

```text
0 = Tomato_Early_blight
1 = Tomato_Late_blight
2 = Tomato_Leaf_Mold
3 = Tomato_healthy
```

## Historical notebook evidence

The saved notebook output reports approximately 3,816 train, 817 validation, and 819 test images. Historical validation accuracy increased from approximately 92.41% to 97.80%. Saved test accuracy and macro-F1 were approximately 98%.

These are historical outputs, not final model claims.

## Known legacy risks

- the original shared-transform split issue was patched after the saved experiment;
- non-stratified split and incomplete seeding;
- phase two did not compare against the global phase-one best;
- BatchNorm behaviour was not controlled;
- class imbalance exists among the four recorded classes;
- combined class weighting, label smoothing, MixUp, CutMix, augmentation, and TTA were not separated experimentally;
- hardcoded test-image paths and repeated inference examples remain;
- high benchmark performance has no field-validation evidence;
- severity output is not validated.

## Required corrected work

1. Add the exact Tomato configuration to the shared pipeline.
2. Generate a corrected runner without copying training code.
3. Choose one imbalance strategy through validation.
4. Use global macro-F1 checkpointing and single-pass evaluation.
5. Report per-class performance rather than relying on the high overall number.
6. Document uncertainty, calibration, and field limitations.
7. Update this handoff after every accepted result.

## Next-chat start point

Read the shared context, decisions, and specification. Do not treat the historical 98% as a production target. Do not inspect dataset contents unless explicitly requested.

