# Agriminds Final Phase - Part 3 How To Make The Project Better

## Scope
This file covers only **Part 3**: what to do next to improve the project substantially.

## Goal for this phase
Convert strong notebook experimentation into a reliable, reproducible, deployable crop-disease system.

## 1) Priority roadmap (what to do first)

## Phase A (Immediate, highest ROI)

### A1. Fix the dataset transform leakage bug
- Update `maize.ipynb`, `rice.ipynb`, `tomato.ipynb` to use separate dataset instances for train/val/test transforms.
- This is the single highest-impact quality fix.

### A2. Define one canonical artifact registry
- Create one `artifacts/` structure inside this folder (or one agreed project path):
  - `artifacts/models/<crop>/best.pt`
  - `artifacts/models/<crop>/phase1.pt`
  - `artifacts/meta/<crop>_classes.json`
  - `artifacts/reports/<crop>/confusion_matrix.png`
  - `artifacts/reports/<crop>/training_plot.png`
- Move or copy current best files from `TRASH` and root to this canonical structure.

### A3. Freeze environment for reproducibility
- Add `requirements.txt` or `environment.yml`.
- Pin critical versions: torch, torchvision, torchcam, opencv-python, scikit-learn, numpy, matplotlib, pillow.

### A4. Centralize config (remove hardcoded absolute paths)
- Introduce a small config file (`config.yaml` or `config.json`) with:
  - dataset roots
  - output roots
  - batch size, LR, epochs, seed
- Read paths/config from one place in all notebooks/scripts.

## Phase B (Short-term quality uplift)

### B1. Standardize splitting strategy
- Use stratified split for class balance where possible.
- Keep fixed seed and persist split indices (`splits/<crop>_train_idx.npy`, etc.) for exact reproducibility.

### B2. Persist preprocessing audit logs
- Save `corrupt_log` to file (`artifacts/preprocessing/corrupt_files.log`).
- Save summary counts as JSON (`artifacts/preprocessing/summary.json`).

### B3. Create one reusable training script per crop template
- Move notebook core logic into scripts:
  - `train_crop.py`
  - `evaluate_crop.py`
  - `predict_crop.py`
- Keep notebooks for exploration and visuals, but scripts as source of truth.

### B4. Add metric tracking table per run
- Store run-level metadata and metrics to CSV:
  - crop, backbone, seed, split id, val acc, test acc, f1 macro, inference ms/img, checkpoint path
- This prevents metric confusion across folders.

### B5. Enforce checkpoint loading policy
- Use `weights_only=True` consistently wherever possible.
- Keep one helper function for safe checkpoint loading.

## Phase C (Model performance improvement)

### C1. Target the weak crops (rice and wheat)
- Run dedicated tuning sweeps only for rice/wheat:
  - input resolution trials (224 vs 256 vs 300)
  - stronger but controlled augmentation
  - class-balanced sampler vs weighted loss comparison
  - backbone trial: ConvNeXt-Tiny / EfficientNetV2-S baseline

### C2. Calibrate confidence
- Add post-training temperature scaling using validation set.
- Report calibrated confidence, not only softmax max score.

### C3. Improve robustness evaluation
- Evaluate on perturbations:
  - brightness/contrast shift
  - blur/noise
  - mild occlusion
- Report robustness drop per crop.

### C4. Better severity estimation validation
- Current spread analysis is heuristic-heavy.
- Add a small manually annotated severity set to validate correlation between estimated spread % and human labels.

## Phase D (Deployment readiness)

### D1. Build a single inference service interface
- Create one callable API:
  - input: image + crop
  - output: class, confidence, top-k, severity estimate, GradCAM overlay path

### D2. Export deployable model formats
- For each chosen crop model, export TorchScript or ONNX.
- Benchmark latency on target hardware.

### D3. Add minimal CI checks
- Smoke test:
  - model load
  - one forward pass
  - one inference output schema validation

### D4. Add model cards
- Per crop, document:
  - classes
  - dataset composition
  - best model
  - known failure modes
  - intended use / non-use

## 2) Architecture recommendation (practical target)

Recommended structure:
- `configs/`
- `src/data/` (preprocess, splits)
- `src/train/`
- `src/eval/`
- `src/infer/`
- `artifacts/`
- `notebooks/`

This keeps experimentation and production paths both clean.

## 3) Suggested final model selection (from your current benchmarks)

Using current benchmark evidence:
- Grape: ResNet18 (or EfficientNetB0 tie; ResNet18 slightly faster in your logs)
- Maize: ResNet18
- Potato: EfficientNetB0
- Rice: ResNet18
- Tomato: ResNet18
- Wheat: ResNet18

Use this as the current baseline champion set, then improve rice/wheat first.

## 4) 14-day execution plan

### Days 1-2
- Fix transform leakage bug.
- Consolidate artifacts and class maps.
- Add environment lock file.

### Days 3-5
- Create config-driven paths.
- Persist split indices.
- Save preprocessing logs.

### Days 6-9
- Scriptify training/evaluation/inference.
- Add run metrics CSV.

### Days 10-12
- Rice/wheat targeted tuning sweep.
- Confidence calibration.

### Days 13-14
- Export deployable models.
- Build minimal inference API + smoke tests.

## 5) Success criteria for “project improved”

You should consider this phase successful when:
- Retraining is one-command reproducible.
- Best artifacts are unambiguous and centrally stored.
- Metrics are comparable across runs.
- Rice/wheat performance improves measurably.
- Inference runs outside notebooks with stable outputs.

---
This completes Part 3 only.


