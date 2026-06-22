# AgriMinds Crop Pipeline Specification

Version: working specification 1  
Last verified: 23 June 2026

## Purpose

This document defines the behaviour every corrected crop pipeline must follow. Crop chats may change crop configuration, plots, and crop-specific analysis. They must not invent a different split, checkpoint rule, metric definition, or inference contract.

The corrected Potato notebook is the current executable reference, but repeated logic should eventually move into a shared `agriminds_ml` package with thin crop notebooks.

## Ownership rule

- The **core-pipeline chat** owns shared data, training, evaluation, calibration, checkpoint, and inference logic.
- A **crop chat** owns only its crop configuration, class contract, runner notebook, results, and crop-specific findings.
- If a crop exposes a shared bug, document it and fix it in the core pipeline first. Do not patch five other notebooks independently.
- Legacy notebooks under `crops/` are read-only historical references unless the user explicitly asks to change them.

## Crop configuration contract

Every crop configuration must declare:

```text
crop identifier
dataset folder
ordered class_to_idx mapping
architecture
input size and normalization
batch size and worker settings
phase-one and optional phase-two settings
seed
artifact paths
```

The ordered class map must be checked against `ImageFolder.class_to_idx` before training. A mismatch is a hard error.

## Data-path rule

Default data root:

```text
PROJECT_ROOT/Data/prepro_pbl_dataset
```

Portable override:

```text
AGRIMINDS_DATA_ROOT
```

Each crop runner appends its lowercase crop directory. Do not embed developer-specific Windows or Linux paths.

## Dataset-audit boundary

The current instruction is **not to scan dataset contents**. Creating/loading crop folders, class mappings, and split manifests is allowed when running an approved crop notebook. Corruption scans, duplicate detection, source analysis, relabelling, or dataset modifications require a new explicit request.

## Split contract

Current corrected-runner behaviour:

- stratified 70% train, 15% validation, 15% test;
- deterministic crop seed;
- relative image paths saved in a JSON manifest;
- manifest records crop, seed, class map, and dataset signature;
- train, validation, and test membership must be disjoint;
- separate `ImageFolder` instances must carry train and evaluation transforms;
- an existing manifest is reused unless its signature or class map fails verification.

The final field-validation protocol is not yet defined. A random image-level split must not be described as farm/source-independent validation.

## Reproducibility contract

Seed Python, NumPy, PyTorch, CUDA, DataLoader generators, samplers, and workers. Disable cuDNN benchmarking and request deterministic algorithms with warnings rather than silent fallback.

Final training evidence should include seeds `42`, `1337`, and `2026`. Report the mean and spread of validation/test metrics rather than selecting a lucky seed. The latest Potato artifact represents one run only.

## Transform contract

Training may use randomized crop, horizontal flip, modest rotation, and conservative colour jitter. Validation, test, calibration, and production inference use deterministic resize, RGB conversion, tensor conversion, and ImageNet normalization.

No validation/test transform may mutate the training dataset. Production TTA is disabled.

Augmentation strength is a hyperparameter and requires validation evidence. Vertical flips, 90-degree rotations, aggressive colour shifts, MixUp, and CutMix are not enabled by default.

## Class-imbalance rule

Use one primary imbalance strategy at a time.

The corrected Potato runner currently uses balanced replacement sampling. Minority images are drawn more often, and the randomized training transform creates a fresh view on each draw. Its loss is unweighted cross-entropy.

Do not combine a balanced sampler with full inverse-frequency class weights unless a controlled experiment proves that double compensation helps. Other crops must compare imbalance strategies rather than copying Potato automatically.

## Model contract

Current architecture direction:

| Crop | Architecture |
|---|---|
| Grape | ResNet18 |
| Maize | ResNet18 |
| Potato | EfficientNet-B0 |
| Rice | ResNet18 |
| Tomato | ResNet18 |
| Wheat | ResNet18 |

Use ImageNet-pretrained weights and replace only the final classifier for the crop's class count.

## Phase-one contract

- Freeze the feature backbone.
- Freeze BatchNorm affine parameters and running statistics.
- Train the classifier head.
- Use AdamW unless a controlled comparison selects another optimizer.
- Select checkpoints by validation macro-F1.
- Save the best phase-one payload before attempting fine-tuning.

## Phase-two contract

Phase two is optional and is never assumed to be better.

- Reload the global champion before changing trainable layers.
- Evaluate immediately before and after configuring trainable layers; predictions must be identical before training.
- Keep BatchNorm parameters and running statistics frozen for small crop datasets unless an experiment specifically reopens this policy.
- Use a lower learning rate for the backbone than for the classifier.
- Continue comparing against the global phase-one macro-F1.
- Never reset the best score to zero.
- If phase two does not improve macro-F1, retain phase one and report that honestly.

Current corrected Potato settings:

```text
last EfficientNet feature stage + final convolution opened
backbone LR = 1e-6
classifier LR = 1e-5
maximum phase-two epochs = 3
BatchNorm frozen
```

These values are Potato evidence, not automatic ResNet settings.

## Checkpoint contract

Every corrected checkpoint payload should contain:

```text
model_state
architecture
ordered class names and class_to_idx
preprocessing specification
dataset signature
split-manifest identity
seed
phase and epoch
best validation macro-F1
```

Load checkpoints with the safest supported weights-only mechanism. Reject architecture, class-order, or dataset-signature mismatches.

Model binaries stay outside normal Git history. Approved releases later require a SHA-256 hash and model card.

## Evaluation contract

Checkpoint selection metric: validation macro-F1.

Official test report:

- single forward pass;
- no TTA;
- accuracy;
- balanced accuracy;
- macro-F1 and weighted F1;
- per-class precision, recall, F1, and support;
- confusion matrix;
- raw and calibrated NLL;
- expected calibration error;
- uncertainty coverage and accepted-case metrics;
- measured device and timing context.

Do not present accepted-case accuracy without coverage. Do not label desktop GPU forward time as Android latency.

## Calibration and test-set warning

The corrected Potato notebook currently fits temperature and the rejection policy on the validation set also used for checkpoint selection. This is acceptable for a research iteration but optimistic for a final claim.

The Potato test set has already been reviewed and used to guide pipeline changes. It is no longer a pristine final holdout. A final release needs a new untouched holdout or external evaluation protocol after calibration and rejection rules are frozen.

No crop chat may repeatedly tune against test results and continue calling the same set unbiased.

## Inference contract

Minimum prediction statuses:

```text
ok
uncertain
invalid_image
unsupported_input
```

An accepted result should include crop, disease, calibrated confidence, top-two margin, top alternatives, architecture, preprocessing identity, and class-map identity. Uncertain or invalid results must not trigger treatment guidance.

## Explainability and severity contract

Grad-CAM is model attention. Never label its area directly as infected tissue.

The desired visual report may include original image, leaf mask, attention map, lesion evidence, and final overlay. If image quality, diagnosis confidence, segmentation, or evidence agreement fails, the report must show `cannot_estimate` and a retake reason.

Numerical severity remains experimental until compared with expert-labelled lesion masks or severity grades.

## Completion gates for one crop

A crop runner is not complete until:

1. all cells compile and run from a clean kernel;
2. path and class mapping checks pass;
3. split manifest is generated/reused without overlap;
4. training is reproducible for the declared seed;
5. phase-one/phase-two global checkpoint logic is verified;
6. reports and artifacts are generated with no stale notebook errors;
7. per-class weaknesses are documented;
8. at least three seeds are evaluated before selecting a final training recipe;
9. final evaluation limitations are written in the crop handoff;
10. Android export is not claimed until parity and device benchmarks pass.

