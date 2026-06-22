# Agriminds Final Phase - Part 2 Detailed Loopholes

## Scope
This file covers only **Part 2**: detailed loopholes in the current project state.

## Severity legend
- `Critical`: likely causing misleading training/evaluation behavior
- `High`: major risk for reproducibility, maintainability, or deployment readiness
- `Medium`: quality/process gaps that can become larger risks later

## 1) Critical loopholes

### 1.1 Transform leakage bug in maize/rice/tomato training splits (`Critical`)
**Where found**
- `maize.ipynb`
- `rice.ipynb`
- `tomato.ipynb`

**Evidence pattern in code**
- `full_dataset = ImageFolder(DATASET_PATH, transform=train_transform)`
- `train_ds, val_ds, test_ds = random_split(...)`
- `val_ds.dataset.transform  = val_transform`
- `test_ds.dataset.transform = val_transform`

**Why this is a loophole**
- `random_split` creates subsets that share the same underlying dataset object.
- Changing `val_ds.dataset.transform` changes `train_ds.dataset.transform` too, because all point to the same `full_dataset`.
- Net effect: train transform can be unintentionally replaced with validation transform, weakening augmentation strategy and making training/evaluation assumptions inconsistent.

**Impact**
- Training behavior and reported metrics may not reflect intended augmentation policy.
- Results can look stable while model robustness is lower than expected.

## 2) High-severity loopholes

### 2.1 Non-stratified random split in per-crop notebooks (`High`)
**Where found**
- `grape.ipynb`, `maize.ipynb`, `potato_fixed.ipynb`, `rice.ipynb`, `tomato.ipynb`, `wheat.ipynb`

**Evidence pattern**
- `random_split(...)` with manual seed, but no class-wise stratification.

**Why this is a loophole**
- For imbalanced classes (documented in `dataset_info.md`), random splitting can skew class distribution between train/val/test.
- Reported performance can vary by split luck rather than true model quality.

### 2.2 Inconsistent experiment reproducibility controls (`High`)
**Where found**
- Per-crop notebooks mostly set `torch.Generator().manual_seed(42)` for split.
- No visible full deterministic setup (`random.seed`, `np.random.seed`, CUDA deterministic mode).

**Why this is a loophole**
- Split is fixed, but training randomness remains partially uncontrolled.
- Re-running can produce drift in metrics and different checkpoint quality.

### 2.3 Artifact fragmentation and missing canonical outputs in root (`High`)
**Where found**
- Root contains only `grape` + `potato` class maps and final checkpoints.
- Many maize/rice/tomato/wheat artifacts exist in `TRASH`.
- Collective comparison notebook saves outputs to external path: `/home/ares/Projects/Agriminds/Phases/Phase-3/...`

**Evidence from inventory**
- Root JSONs: `grape_classes.json`, `potato_classes.json`
- Root final models: `grape_final_model.pt`, `potato_final_model.pt`
- Root confusion matrix PNGs: none

**Why this is a loophole**
- No single canonical location for "current best" artifacts.
- Reuse/inference/integration can pick wrong version by mistake.

### 2.4 Hardcoded absolute paths throughout notebooks (`High`)
**Where found**
- Multiple notebooks use direct paths like:
  - `/home/ares/Projects/Agriminds/Dataset/prepro_pbl_dataset/...`
  - `/home/ares/Projects/Agriminds/test...`

**Why this is a loophole**
- Code portability is low.
- Running on another machine/environment breaks without manual path edits.

### 2.5 No environment lock / dependency manifest in this folder (`High`)
**Where found**
- No `requirements.txt`, `environment.yml`, `pyproject.toml`, or equivalent at root.

**Why this is a loophole**
- Recreating a working runtime for training/inference is fragile.
- Version mismatch risk is high for torch/torchvision/torchcam/OpenCV combinations.

## 3) Medium-severity loopholes

### 3.1 Version ambiguity due parallel copies (`Medium`)
**Where found**
- Similar notebooks exist in root and `tempstore`, but hashes are not identical.

**Evidence**
- `grape.ipynb == tempstore/grape.ipynb : False` (same pattern for all six crop notebooks)

**Why this is a loophole**
- Multiple diverged copies without explicit version intent create confusion on which notebook represents latest truth.

### 3.2 Security/compatibility inconsistency in checkpoint loading (`Medium`)
**Where found**
- Per-crop notebooks use safer form in places: `torch.load(..., weights_only=True)`
- Collective notebook output logs show warnings from loads without `weights_only=True`.

**Why this is a loophole**
- Inconsistent load policy can cause future compatibility/security issues when handling checkpoints.

### 3.3 Notebook-centric architecture without production entrypoint (`Medium`)
**Where found**
- Core logic exists only inside notebooks.
- No centralized script/package API for training/evaluation/inference in this folder.

**Why this is a loophole**
- Harder CI testing, reuse, deployment, and regression tracking.

### 3.4 Evaluation protocol inconsistency risk (`Medium`)
**Where found**
- TTA is enabled in per-crop evaluation (`ENABLE_TTA = True`).
- Comparison notebook reports single-pass speed/accuracy metrics.

**Why this is a loophole**
- If metrics across artifacts mix TTA and non-TTA runs, cross-file comparison is not apples-to-apples.

### 3.5 Data preprocessing traceability gap (`Medium`)
**Where found**
- `Preprocessing.ipynb` builds `corrupt_log` and prints it, but no persistent audit file is written in this folder.

**Why this is a loophole**
- Hard to later verify exactly which raw files were excluded in a specific dataset build.

## 4) Crop performance weak spots (risk hotspots)

Based on captured test metrics in notebooks/comparison outputs:
- Rice and wheat are consistently lower than grape/tomato/potato.
  - Rice around high-80s in several runs.
  - Wheat around high-80s in several runs.

This is not a bug by itself, but it marks areas where current pipeline is comparatively weaker and sensitive to data/model choices.

## 5) Loophole summary by category

### Data pipeline loopholes
- Transform leakage bug in maize/rice/tomato (critical)
- Non-stratified per-crop split
- Preprocessing traceability not persisted

### Experiment management loopholes
- Fragmented artifacts across root/TRASH/tempstore/external folders
- Divergent duplicate notebooks
- Missing environment lock files

### Deployment/readiness loopholes
- Hardcoded absolute paths
- Notebook-only implementation pattern
- Inconsistent checkpoint loading policy across notebooks

---
This completes Part 2 only.
