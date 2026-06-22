# AgriMinds

AgriMinds is an offline-first plant disease diagnosis and crop-care project built around crop-specific deep-learning models. The current research covers six crops—Grape, Maize, Potato, Rice, Tomato, and Wheat—and explores reliable diagnosis, explainability, severity estimation, and longitudinal treatment tracking for farmers using affordable Android devices.

> **Current status:** research hardening and validation. AgriMinds is not yet an agriculturally validated production diagnostic system, and its outputs must not replace qualified agricultural advice.

## Why AgriMinds

Many plant-disease classifiers stop at a single prediction. AgriMinds is being designed as a complete evidence-and-recovery workflow:

1. capture one or more guided leaf images;
2. validate image quality and evidence diversity;
3. diagnose using a project-owned crop model;
4. explain the model's attention and reject uncertain cases;
5. provide grounded, reviewable care guidance;
6. schedule follow-ups and track whether the plant improves.

The core diagnosis remains under project control. Any future generative-AI integration is limited to explaining and localizing already-verified results; it must not override the trained crop model or invent treatment dosages.

## Supported crops and legacy baselines

| Crop | Classes | Current architecture | Historical notebook test accuracy* |
|---|---:|---|---:|
| Grape | 4 | ResNet18 | ~100% |
| Maize | 4 | ResNet18 | ~94% |
| Potato | 3 | EfficientNet-B0 | ~95% |
| Rice | 4 | ResNet18 | ~88% |
| Tomato | 4 | ResNet18 | ~98% |
| Wheat | 4 | ResNet18 | ~88% |

\*These figures are preserved from historical notebook outputs. They were produced before the current reproducibility and validation audit and must not be treated as production claims.

## Current engineering direction

AgriMinds uses one champion model per crop rather than a heavy always-on ensemble. Only the selected crop model should be loaded during Android inference. Official production evaluation will use a single forward pass matching mobile preprocessing; test-time augmentation and ensemble experiments remain research comparisons.

The corrected pipeline is being standardized around:

- deterministic execution and persisted stratified splits;
- independent train, validation, and test transforms;
- macro-F1 checkpoint selection across all training phases;
- calibrated confidence and explicit uncertainty rejection;
- safe checkpoint/class-map verification;
- Android/Python preprocessing parity;
- Grad-CAM++ as explanation, not lesion segmentation;
- separately validated severity and follow-up tracking.

The first corrected crop runner is [`Notebooks/crop approach/potato_corrected.ipynb`](Notebooks/crop%20approach/potato_corrected.ipynb). Existing notebooks under `crops/` are retained as legacy experiments.

## Repository map

```text
AgriMinds/
├── Backend/          # Future advisory/API services; diagnosis remains local-first
├── Data/             # Dataset documentation only; image data is not committed
├── Deployment/       # Android/export/benchmarking plans
├── Docs/             # Maintained architecture, roadmap, and limitations
├── Frontend/         # Future FarmWise web/mobile interface documentation
├── Models/           # Model cards and artifact policy; weights are not normal Git files
├── Notebooks/        # Corrected crop runners and research notebooks
├── crops/            # Legacy crop notebooks, class maps, and local checkpoints
├── md files/         # Internal historical notes pending consolidation
└── SML LOGIC/        # Historical ensemble research
```

## Local setup

Python 3.12 is the current research environment.

```bash
python -m venv .venv
# Windows PowerShell
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
jupyter lab
```

Datasets are intentionally excluded. Point corrected notebooks to the parent directory containing the six crop folders:

```powershell
$env:AGRIMINDS_DATA_ROOT = "D:\path\to\prepro_pbl_dataset"
```

Expected shape:

```text
prepro_pbl_dataset/
├── grape/
├── maize/
├── potato/
├── rice/
├── tomato/
└── wheat/
```

## Model and data policy

Datasets, generated artifacts, API credentials, and local environment files are excluded from Git. PyTorch/ONNX/TFLite checkpoints are also excluded from normal Git history; approved champion weights should later be distributed through a versioned artifact mechanism after integrity and licensing review.

No dataset is redistributed by this repository. Every contributor is responsible for verifying the license and attribution requirements of their data sources.

## Product roadmap

The immediate goal is to correct and retrain all six crop pipelines. Later stages add Android model parity, guided multi-image consensus, uncertainty-aware care guidance, expert-reviewed treatment protocols, reminders, and disease progression tracking. See [`Docs/ROADMAP.md`](Docs/ROADMAP.md).

## Responsible use

AgriMinds can produce incorrect predictions, especially for field images, unsupported crops, unfamiliar diseases, poor lighting, or visually similar symptoms. Chemical recommendations must be grounded in approved regional guidance and reviewed by qualified experts. Low-confidence and contradictory cases must be rejected or escalated rather than forced into a diagnosis.

## Intellectual property and licensing

This repository is currently intended to remain **private** while the project evaluates patent-sensitive workflow ideas. No open-source license has been selected. Do not redistribute the code, model artifacts, or unpublished invention details without the owner's permission.

