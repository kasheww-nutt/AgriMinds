# Known Limitations

AgriMinds is a research project under active correction. The following limitations are product and safety constraints, not cosmetic backlog.

## Evidence quality

Historical results come from notebook experiments whose splitting, reproducibility, TTA usage, and checkpoint selection are being audited. Metrics from legacy notebooks are not directly comparable with corrected production candidates.

## Dataset generalization

The project includes benchmark-style leaf imagery and may learn acquisition backgrounds or laboratory conditions. Performance on farms, different phones, growth stages, mixed symptoms, pests, nutrient deficiencies, and unfamiliar diseases is not yet established.

## Forced classification

Legacy classifiers always choose a known class. Production inference must reject unsupported crops, non-leaf images, unfamiliar disease patterns, low-quality evidence, and low-confidence results.

## Confidence

Raw softmax scores are not reliable probabilities. Confidence must be calibrated on validation data and monitored after deployment.

## Explainability and severity

Grad-CAM visualizes influential regions but does not prove biological causation or affected area. Severity estimates require expert-labelled validation and must support refusal when evidence is inadequate.

## Treatment safety

Incorrect pesticide, fertilizer, concentration, timing, or crop-stage advice can cause harm. Care guidance must come from reviewed, versioned sources and include local regulatory and label constraints. Generative output cannot be the sole authority.

## Deployment

The target APK size, on-device latency, RAM use, and model compatibility have not yet been measured on representative farmer devices. Model inclusion decisions remain provisional until those benchmarks exist.

