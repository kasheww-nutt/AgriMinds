# Maize Handoff

Last verified: 23 June 2026  
Status: legacy experiment only; corrected runner not created

## Files and contract

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Maize/maize.ipynb` |
| Legacy class map | `crops/Maize/maize_classes.json` |
| Saved legacy reports | `crops/Maize/*.png` |
| Local data folder | `Data/prepro_pbl_dataset/maize` |

Architecture direction: ResNet18.

```text
0 = Blight
1 = Common_Rust
2 = Gray_Leaf_Spot
3 = Healthy
```

## Historical notebook evidence

The saved notebook output reports approximately 2,930 train, 627 validation, and 629 test images. Historical validation accuracy increased from approximately 91.07% in phase one to 92.66% in phase two. Saved test accuracy was approximately 94%, with macro-F1 around 93%.

These outputs predate the corrected pipeline and remain historical.

## Known legacy risks

- the original notebook shared one transformed dataset across random-split subsets; the transform mutation was patched later, but saved metrics remain from the earlier workflow;
- the split is still non-stratified;
- phase two did not preserve the global phase-one champion;
- BatchNorm behaviour was not controlled;
- class weights, label smoothing, MixUp, CutMix, and TTA were combined without ablation;
- Gray Leaf Spot was the minority class in the recorded training split;
- official metrics used TTA rather than Android-equivalent single-pass inference;
- paths and example images are machine-specific;
- severity/colour analysis is not validated.

## Required corrected work

1. Create a Maize configuration with the exact class contract.
2. Use the shared ResNet18 pipeline and independent dataset objects.
3. Choose an imbalance method through validation; do not automatically copy Potato sampling.
4. Preserve the global champion across phases and freeze BatchNorm according to the shared policy.
5. Produce single-pass per-class metrics, calibration, and uncertainty results.
6. Investigate Gray Leaf Spot recall and confusion without tuning against the test set.
7. Update this handoff with corrected artifacts and limitations.

## Next-chat start point

Read the shared documents. Keep the Grape/Potato logic out of this chat unless it lives in the shared package. Do not scan the Maize dataset unless the user explicitly asks.

