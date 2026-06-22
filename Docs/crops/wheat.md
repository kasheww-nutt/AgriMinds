# Wheat Handoff

Last verified: 23 June 2026  
Status: legacy experiment only; corrected runner not created

## Files and contract

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Wheat/wheat.ipynb` |
| Legacy class map | `crops/Wheat/wheat_classes.json` |
| Local data folder | `Data/prepro_pbl_dataset/wheat` |

Architecture direction: ResNet18.

```text
0 = Brown Rust
1 = Healthy Wheat
2 = Mildew
3 = Yellow Rust
```

## Historical notebook evidence

The saved notebook output reports approximately 6,591 train, 1,412 validation, and 1,414 test images. Historical validation accuracy increased from approximately 73.02% after phase one to 85.27% after iterative fine-tuning. Saved test accuracy was approximately 88%, with macro-F1 around 86%.

Wheat is one of the weaker historical crops.

## Useful legacy behaviour

Unlike most other legacy notebooks, Wheat carried the existing global best across its staged fine-tuning and reloaded the best checkpoint between stages. That principle should be retained in the shared pipeline.

## Known legacy risks

- non-stratified image-level split;
- incomplete seeding;
- class-specific heavier augmentation for Healthy images without controlled evidence;
- class weights, label smoothing, MixUp, CutMix, and TTA combined;
- BatchNorm behaviour not explicitly frozen;
- large phase-one/phase-two difference requires repeatable validation;
- hardcoded paths and example-image state;
- Grad-CAM/colour-based spread estimates are not validated;
- field generalization remains unknown.

## Required corrected work

1. Add the exact Wheat class configuration.
2. Use the shared ResNet18 pipeline while preserving global-best staged training.
3. Compare conservative layer4 fine-tuning with phase-one-only across seeds.
4. Report per-class recall for Mildew and both Rust classes.
5. Use single-pass calibrated evaluation and uncertainty rejection.
6. Treat lower historical performance as an error-analysis task, not a reason to add an ensemble immediately.
7. Update this handoff with corrected evidence.

## Next-chat start point

Read the shared documents. Keep Wheat-specific tuning in this chat and shared fixes in the core chat. Do not scan or alter dataset contents without explicit instruction.

