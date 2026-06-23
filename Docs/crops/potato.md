# Potato Handoff

Last verified: 23 June 2026  
Status: corrected runner implemented and executed; safe visual report code added, demo execution pending

## Ownership

The Potato chat owns Potato configuration, runner outputs, Potato-specific analysis, and this handoff. Shared training logic belongs to the core-pipeline chat.

## Files

| Purpose | Path |
|---|---|
| Legacy notebook | `crops/Potato/potato_fixed.ipynb` |
| Corrected runner | `Notebooks/crop approach/potato_corrected.ipynb` |
| Legacy class map | `crops/Potato/potato_classes.json` |
| Local data folder | `Data/prepro_pbl_dataset/potato` |
| Local corrected artifacts | `artifacts/potato/` (Git-ignored) |

## Class contract

```text
0 = Potato___Early_blight
1 = Potato___Late_blight
2 = Potato___healthy
```

Architecture: EfficientNet-B0.

## Corrected runner behaviour

- stratified persisted 70/15/15 split;
- current notebook seed `2026`;
- runtime class balancing with `WeightedRandomSampler`;
- randomized transforms applied on every sampled training draw;
- unweighted cross-entropy;
- phase-one frozen backbone and classifier-head training;
- BatchNorm parameters/statistics frozen;
- conservative phase two with backbone LR `1e-6`, classifier LR `1e-5`, maximum three epochs;
- transition assertion before phase-two training;
- global validation macro-F1 checkpoint across phases;
- single-pass test evaluation;
- validation-fitted temperature and rejection policy;
- inference can return `uncertain`.

## Latest verified local run

Artifact: `artifacts/potato/reports/potato_test_report.json`

| Metric | Value |
|---|---:|
| Test images | 323 |
| Accuracy | 97.21% |
| Balanced accuracy | 96.77% |
| Macro-F1 | 96.20% |
| Weighted F1 | 97.22% |
| Early Blight F1 | 97.28% |
| Late Blight F1 | 97.70% |
| Healthy F1 | 93.62% (23 images) |
| Calibrated ECE | 2.20% |
| Rejection coverage | 96.59% |
| Accepted accuracy | 98.40% |

Champion: phase-one head. Phase two was stable but remained below the phase-one global validation macro-F1.

## Do not overclaim

- This is one run, not a three-seed conclusion.
- The Healthy test support is only 23.
- Validation selected the checkpoint, calibration temperature, and rejection rule.
- The test set has already influenced development and is not a pristine final holdout.
- Runtime balancing creates varied views of the same minority images, not new biological evidence.
- The recorded ~2 ms timing is desktop GPU forward time, not Android latency.
- No external field evaluation, source-group audit, or duplicate audit has been completed.

## Known remaining work

0. Potato safe leaf/symptom segmentation report code has been added to the corrected notebook; run the demo cell and inspect output next.
1. Keep it post-inference only: it must not change training, checkpoint selection, or test metrics.
2. Use the accepted Potato prediction plus calibrated confidence before generating any visual estimate.
3. Add gates for image quality, leaf-mask trust, low confidence, healthy predictions, and attention/evidence disagreement.
4. Five artifacts are produced by `create_visual_evidence_report`: original image, leaf region, model attention, symptom-coloured evidence, and final possible-affected overlay.
5. JSON summary saving is implemented with `ok` or `cannot_estimate` and human-readable gate failures.
6. Do not restore the legacy severity logic unchanged; reuse ideas only after adding refusal gates and safer labels.
7. Extract reusable logic into the shared pipeline package.
8. Add synthetic tests for balanced sampling, BatchNorm freezing, global checkpoint retention, class mapping, and visual-report refusal gates.
9. Decide the final calibration/holdout protocol before more test-driven tuning.
10. Run the controlled seed set only after the shared pipeline is stable.
11. Export and benchmark only after the final training protocol is frozen.

## Legacy comparison

Historical Potato outputs used class weights, class-specific augmentation, MixUp/CutMix, TTA, a random image-level split, and phase-two checkpoint logic that did not preserve the phase-one global best. Those results are not directly comparable with the corrected run.

## Next-chat start point

Read `Docs/PROJECT_CONTEXT.md`, `Docs/DECISIONS.md`, `Docs/PIPELINE_SPEC.md`, and this file. Do not inspect the dataset unless explicitly requested. Continue by helping extract or validate the shared pipeline; do not tune again against the current test report.

