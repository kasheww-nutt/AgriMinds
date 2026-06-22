# Grape Handoff

Last verified: 23 June 2026  
Status: legacy experiment only; corrected runner not created

## Files and contract

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Grape/grape.ipynb` |
| Legacy class map | `crops/Grape/grape_classes.json` |
| Local data folder | `Data/prepro_pbl_dataset/grape` |

Architecture direction: ResNet18.

```text
0 = Black Rot
1 = Downy Mildew
2 = Healthy
3 = Powdery Mildew
```

## Historical notebook evidence

The saved notebook output reports approximately 3,465 train, 742 validation, and 744 test images. Phase-one and phase-two validation accuracy were both approximately 99.06%. The saved test report displayed approximately 100% accuracy, with macro metrics around 99%.

These are historical values, not corrected production evidence.

## Known legacy risks

- non-stratified random split;
- only the split generator was seeded;
- class-specific heavier augmentation was applied to Healthy images without a controlled comparison;
- class weights, label smoothing, MixUp, CutMix, and TTA were stacked;
- phase two started its own best score from zero rather than carrying phase one;
- frozen-backbone BatchNorm behaviour was not controlled;
- hardcoded Linux paths and saved FileNotFound output exist;
- near-perfect results require especially careful leakage/background-bias review later;
- Grad-CAM/spread code can produce conclusions beyond its evidence.

## Required corrected work

1. Create a Grape crop configuration using the exact class order above.
2. Use the shared ResNet18 builder and pipeline specification.
3. Generate/reuse a stratified manifest only when runner execution is approved.
4. Select checkpoints by validation macro-F1 with a global phase-one/phase-two champion.
5. Compare imbalance/regularization choices rather than copying legacy stacking.
6. Report single-pass results without TTA.
7. Treat the old near-perfect result as a risk signal, not a target to reproduce at any cost.
8. Record all results and caveats in this handoff.

## Next-chat start point

Read the project context, decisions, and pipeline specification first. Do not modify shared training logic inside the Grape chat. Do not perform dataset auditing unless explicitly authorized.

