# AgriMinds Project Context

Last verified: 23 June 2026  
Repository: `https://github.com/kasheww-nutt/AgriMinds` (private)  
Default branch: `main`

## How to use this document

This is the main handoff document for new chats and contributors. Read it before changing the project. Statements are labelled so ideas are not confused with completed work:

- **Confirmed:** verified in the repository, artifacts, or an accepted decision.
- **Historical:** preserved from earlier experiments and not automatically trustworthy.
- **Proposed:** discussed direction that has not been implemented.
- **Open:** unresolved decision.

## Project goal

**Confirmed:** AgriMinds is the crop-disease diagnosis part of the wider FarmWise application concept. The intended user is a farmer who may have a low-cost Android phone and unreliable internet. The project aims to provide understandable crop-disease results, reject uncertain evidence, and later support care reminders and follow-up tracking.

**Confirmed:** AgriMinds is still a research project. It is not an agriculturally validated production diagnostic system.

## Supported crops, classes, and current architecture choices

| Crop | Classes | Architecture direction | Current corrected runner |
|---|---|---|---|
| Grape | Black Rot, Downy Mildew, Healthy, Powdery Mildew | ResNet18 | Not created |
| Maize | Blight, Common Rust, Gray Leaf Spot, Healthy | ResNet18 | Not created |
| Potato | Early Blight, Late Blight, Healthy | EfficientNet-B0 | Created and run |
| Rice | Bacterial Leaf Blight, Brown Spot, Healthy Rice Leaf, Leaf Blast | ResNet18 | Not created |
| Tomato | Early Blight, Late Blight, Leaf Mold, Healthy | ResNet18 | Not created |
| Wheat | Brown Rust, Healthy Wheat, Mildew, Yellow Rust | ResNet18 | Not created |

The exact stored class names are recorded in the crop handoff documents and the class JSON files. Class order is part of the model contract and must not change silently.

## Repository state

**Confirmed:** The repository contains:

- historical crop notebooks and class maps under `crops/`;
- a historical ensemble notebook under `SML LOGIC/`;
- maintained documentation under `Docs/`;
- the corrected Potato notebook at `Notebooks/crop approach/potato_corrected.ipynb`;
- locally generated artifacts under `artifacts/`, which are intentionally ignored by Git;
- local model weights under crop/artifact folders, also ignored by Git.

**Confirmed:** Datasets, model binaries, generated outputs, credentials, and local environment files are excluded from normal Git history.

## Legacy notebook status

**Historical:** The six original notebooks contain useful experiments, plots, checkpoints, Grad-CAM code, and saved metrics. They also contain hardcoded paths, inconsistent split/evaluation choices, duplicated code, and notebook-state risks. They are evidence of earlier work, not the production pipeline.

**Confirmed issues found in the historical workflow include:**

- non-stratified image-level random splits;
- incomplete reproducibility controls;
- transform sharing previously affecting Maize, Rice, and Tomato (patched in the legacy files, but legacy metrics remain historical);
- phase-two checkpoints in five notebooks starting their comparison from zero rather than the phase-one best;
- frozen backbones whose BatchNorm statistics could still change;
- class weighting, label smoothing, MixUp, CutMix, augmentation, and TTA combined without controlled ablations;
- reported TTA metrics that do not match a lightweight single-pass Android path;
- Grad-CAM/severity outputs that could continue after low confidence or failed segmentation;
- machine-specific paths and stale saved runtime errors.

## Corrected Potato pipeline

**Confirmed file:** `Notebooks/crop approach/potato_corrected.ipynb`

**Confirmed dataset mapping:**

```text
Data/prepro_pbl_dataset/potato
```

**Confirmed class contract:**

```text
0 = Potato___Early_blight
1 = Potato___Late_blight
2 = Potato___healthy
```

**Confirmed implemented behaviour:**

- fixed, persisted stratified 70/15/15 split manifest;
- deterministic seed and DataLoader worker setup;
- independent training, validation, and test dataset objects;
- runtime class balancing with `WeightedRandomSampler`;
- fresh random training augmentation on every sampled draw;
- unweighted cross-entropy to avoid compensating twice for imbalance;
- EfficientNet-B0 with a frozen-backbone phase;
- BatchNorm parameters and running statistics frozen during both phases;
- conservative phase two with backbone LR `1e-6`, classifier LR `1e-5`, and at most three epochs;
- transition check proving that phase-two configuration does not change predictions before training;
- one global validation macro-F1 champion across both phases;
- temperature calibration and a validation-fitted uncertainty policy;
- official single-pass test evaluation without TTA;
- safe inference result that can return `uncertain`.

### Latest Potato run

**Confirmed artifact:** `artifacts/potato/reports/potato_test_report.json`  
**Artifact status:** local and ignored by Git.

| Metric | Latest value |
|---|---:|
| Test size | 323 |
| Accuracy | 97.21% |
| Balanced accuracy | 96.77% |
| Macro-F1 | 96.20% |
| Weighted F1 | 97.22% |
| Healthy-class F1 | 93.62% (23 test images) |
| Calibrated ECE | 2.20% |
| Accepted coverage | 96.59% |
| Accepted accuracy | 98.40% |

**Confirmed:** The final champion remained `phase1_head`. Phase two became stable but did not beat the best phase-one validation macro-F1. This is acceptable; fine-tuning is optional.

**Important limitations of this result:**

- it is one run, not a multi-seed conclusion;
- the Healthy test class has only 23 images;
- the validation set was used for checkpoint selection, calibration, and rejection-policy fitting;
- the current test set has already influenced development decisions and is no longer a pristine final holdout;
- no duplicate/source-group audit or external field evaluation has been completed;
- the reported ~2 ms timing is desktop GPU forward time, not Android latency.

## Data status

**Confirmed local path:** `Data/prepro_pbl_dataset`

**User instruction:** Do not perform a dataset audit unless the user explicitly requests it. Mapping a crop notebook to its crop folder and class names is allowed; scanning images for corruption, duplicates, or source structure is not currently authorized.

**Historical/user-provided context:** The dataset is described as a mixture of roughly three to four sources with both laboratory and on-site imagery. This has not been independently verified or documented in the repository.

## Production direction

**Confirmed:** The default mobile direction is one champion model per crop. Only the selected crop model should be loaded. Heavy live inference using three architectures per crop is excluded from the default production path.

**Open:** The final packaging format, precision, APK/AAB size, and whether any advisory functionality requires a backend remain unresolved until Android benchmarks exist.

**Confirmed boundary:** Project-owned crop models provide diagnosis. Gemini or another LLM may later explain an accepted result, translate it, or organize grounded care guidance. It must not replace the diagnosis, override the disease class, or invent chemical dosages.

## Explainability and severity

**Confirmed:** Grad-CAM is treated as model-attention visualization, not lesion segmentation.

**Proposed:** A safe five-panel report may show the original image, leaf mask, Grad-CAM attention, lesion evidence, and final overlay. It must refuse severity when confidence, segmentation, or evidence reliability fails.

**Confirmed decision:** Numerical severity remains experimental until it is tested against an expert-labelled subset. The intended validation metrics include Dice/IoU for masks, severity MAE, Spearman correlation, and weighted kappa for severity categories.

## Proposed product features

The following have been discussed but are not implemented:

- guided capture of two or three views;
- image-quality checks and duplicate-view rejection;
- confidence/quality/diversity-weighted multi-image consensus;
- failure-specific retake instructions;
- patch-versus-whole contradiction handling;
- background-reliance acceptance checks;
- same-plant healthy-reference capture;
- treatment episodes, local reminders, and follow-up photographs;
- improving/stable/worsening progression tracking;
- expert escalation;
- RAG-grounded, multilingual care explanations.

No new chat should describe these as completed features.

## Intellectual-property position

**Confirmed:** The GitHub repository remains private while possible patent-sensitive workflow ideas are evaluated. No open-source license has been selected.

**Provisional invention direction:** The strongest discussed concept is a diversity-aware, failure-guided evidence-completion workflow that requests specific missing images, accepts a diagnosis only after evidence requirements are met, and connects the result to follow-up verification. Patentability has not been established and requires a formal prior-art and legal review.

## Communication and documentation rules

- Write naturally and directly; avoid inflated or AI-generated-sounding language.
- Separate confirmed facts from historical results, proposals, and open questions.
- Do not claim production validation, field validation, or patentability without evidence.
- Keep user-facing answers concise when requested, but do not hide material risks.
- Update the relevant handoff and project-state document after completing work.

