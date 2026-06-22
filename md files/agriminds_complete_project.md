# AgriMinds — Complete Project Documentation

> **Plant Disease Detection & Severity Estimation System using Deep Learning**
> Project Type: College PBL (Project Based Learning) | Author: Kashif, Pune, Maharashtra

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Problem Statement](#2-problem-statement)
3. [System Environment & Setup](#3-system-environment--setup)
4. [Dataset](#4-dataset)
5. [Data Preprocessing Pipeline](#5-data-preprocessing-pipeline)
6. [Architecture Decisions](#6-architecture-decisions)
7. [Model Training — Per Crop Pipeline](#7-model-training--per-crop-pipeline)
8. [Model Results](#8-model-results)
9. [Grad-CAM Explainability](#9-grad-cam-explainability)
10. [Severity Estimation](#10-severity-estimation)
11. [Prediction Pipeline](#11-prediction-pipeline)
12. [Advanced Patent Features](#12-advanced-patent-features)
13. [Errors Encountered & Fixes](#13-errors-encountered--fixes)
14. [File Structure](#14-file-structure)
15. [Deliverables](#15-deliverables)
16. [Viva Preparation](#16-viva-preparation)
17. [Future Scope](#17-future-scope)

---

## 1. Project Overview

**AgriMinds** is an AI-powered plant disease detection and severity estimation system that allows farmers and agronomists to photograph a crop leaf and instantly receive a disease diagnosis with confidence score, Grad-CAM heatmap visualization, and severity level.

| Attribute | Detail |
|-----------|--------|
| **Project Name** | AgriMinds |
| **Domain** | Deep Learning / Precision Agriculture |
| **Student** | Kashif, Pune, Maharashtra |
| **Laptop** | ASUS TUF F15 — RTX 4060, i7 13th Gen H, 16GB DDR5 |
| **OS** | Windows 11 + WSL2 (Ubuntu via Miniconda) |
| **Environment** | `conda activate ml-env` (Python 3.12) |
| **IDE** | VS Code with Jupyter Notebooks |
| **Framework** | PyTorch (switched from TensorFlow due to cuDNN issues) |

---

## 2. Problem Statement

Farmers in India rely on manual, visual inspection for plant disease diagnosis — a process that is slow, error-prone, and dependent on expert availability. Misidentification leads to wrong pesticide use, crop loss, and economic damage.

**AgriMinds solves this by:**
- Providing instant, AI-based disease identification from a single leaf photo
- Giving a confidence score with tier classification (High / Moderate / Low)
- Visualizing exactly where on the leaf the disease was detected (Grad-CAM heatmap)
- Showing severity level based on infected area spread percentage

---

## 3. System Environment & Setup

### Hardware
- **GPU:** NVIDIA RTX 4060 Laptop GPU (8GB VRAM)
- **CPU:** Intel i7-13th Gen H (16 threads)
- **RAM:** 16GB DDR5

### Software Stack

```
OS           : Windows 11 + WSL2 Ubuntu
Shell        : bash via WSL2
Package mgr  : Miniconda (conda activate ml-env)
Python       : 3.12
Framework    : PyTorch 2.x + torchvision
CUDA         : 12.1
cuDNN        : 9.1.0
IDE          : VS Code + Jupyter Notebooks
```

### Installation Commands

```bash
conda activate ml-env
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
pip install matplotlib seaborn scikit-learn jupyter opencv-python pillow
```

### Why PyTorch (Not TensorFlow)?

TensorFlow was initially used but caused a hard crash:
```
Loaded runtime CuDNN library: 9.1.0 but source was compiled with 9.3.0
No DNN in stream executor — training fails
```
PyTorch handles cuDNN natively and worked without any version conflicts.

### WSL2 Path Convention

```
Windows path : C:\processeddataset\Potato
WSL2 path    : /mnt/c/processeddataset/Potato
```

---

## 4. Dataset

### Source
Mixed dataset — PlantVillage (public benchmark) + supplementary real-world leaf images.

### Location
- **Raw:** `C:\dataset`
- **Processed/Clean:** `C:\processeddataset`

### Statistics

| Crop | Classes / Diseases | Total Images (approx.) |
|------|-------------------|----------------------|
| Potato | 3 (Healthy, Early Blight, Late Blight) | ~3,000 |
| Rice | 4 (Healthy, Bacterial Blight, Brown Spot, Leaf Blast) | ~5,500 |
| Maize | 4 (Healthy, Blight, Common Rust, Gray Leaf Spot) | ~3,800 |
| Wheat | 5 (Healthy, Yellow Rust, Brown Rust, Powdery Mildew, Septoria) | ~3,200 |
| Grape | 4 (Healthy, Black Rot, Downy Mildew, Powdery Mildew) | ~3,600 |
| Tomato | Multiple (Healthy + various diseases) | ~9,600 |
| **Total** | — | **~28,728** |

### Known Dataset Limitations

1. **PlantVillage background bias** — many images shot on white/black background, not field conditions
2. **Class imbalance** — some disease classes have significantly fewer samples
3. **Intra-class diversity** — same disease looks different across growth stages and weather

---

## 5. Data Preprocessing Pipeline

All preprocessing was done before training to create a clean, consistent dataset.

### Steps Applied

1. **Image Resizing** — All images resized to 224×224 pixels (MobileNetV2/ResNet18 input size)
2. **RGB Conversion** — All images converted to 3-channel RGB (removes grayscale inconsistencies)
3. **Aspect Ratio Preservation** — Center-crop approach using LANCZOS resampling
4. **Corrupted File Handling** — Unreadable/corrupt images automatically skipped
5. **Clean Dataset Creation** — New folder `C:\processeddataset` created; original left untouched

### Preprocessing Code Summary

```python
from PIL import Image, ImageOps
import os

ORIGINAL_PATH = r"C:\dataset"
PROCESSED_PATH = r"C:\processeddataset"
IMG_SIZE = (224, 224)
count = 0

for root, dirs, files in os.walk(ORIGINAL_PATH):
    for file in files:
        if file.lower().endswith(('.jpg','.jpeg','.png','.bmp','.tiff','.webp')):
            try:
                with Image.open(os.path.join(root, file)) as img:
                    img = img.convert("RGB")
                    img = ImageOps.fit(img, IMG_SIZE, Image.Resampling.LANCZOS)
                    img.save(new_img_path, quality=95)
                    count += 1
            except Exception as e:
                print("Skipped:", file)

print("Total images processed:", count)
# Output: Total images processed: 28,728
```

### Data Splits (Applied per crop at training time)

| Split | Percentage | Purpose |
|-------|-----------|---------|
| Train | 70% | Model learning |
| Validation | 15% | Hyperparameter tuning, early stopping |
| Test | 15% | Final unbiased evaluation |

### Augmentation (Training Set Only)

```python
train_transform = T.Compose([
    T.Resize((224, 224)),
    T.RandomHorizontalFlip(),
    T.RandomRotation(15),
    T.ColorJitter(brightness=0.2, contrast=0.2),
    T.ToTensor(),
    T.Normalize(mean=[0.485, 0.456, 0.406],
                std=[0.229, 0.224, 0.225])
])
```

Validation and test sets use only resize + normalize (no augmentation).

---

## 6. Architecture Decisions

### Final Architecture Choices

| Decision | Choice | Reason |
|----------|--------|--------|
| Framework | **PyTorch** | TF had cuDNN 9.1 vs 9.3 crash |
| Base Model | **MobileNetV2 + ResNet18** | Lightweight, fast, pretrained on ImageNet |
| Strategy | **Per-crop models (×5)** | Less inter-crop confusion, higher accuracy per crop |
| Training | **2-Phase transfer learning** | Best practice for small datasets |
| Loss Function | **CrossEntropyLoss with class weights** | Handles class imbalance |
| Severity | **Grad-CAM spread % → slider** | Replaced broken OpenCV color heuristics |
| Output Format | **Disease + Confidence % + Grad-CAM + Severity** | Full explainable AI output |

### Why Per-Crop Models (Not One Universal Model)?

- A single model trained on all 6 crops showed inter-crop confusion (e.g., Rice Blast confused with Wheat Rust)
- Per-crop models are more accurate, easier to update, and modular
- Trade-off: 5 separate `.pt` model files (accepted)

### Why OpenCV Severity Was Dropped

Originally planned to measure infected leaf area using HSV color thresholding:
```
Green → Healthy leaf area
Dark/discolored → Diseased area
Ratio = Diseased Pixels / Total Leaf Pixels
```

**Problem found:**
- Wheat at harvest = naturally brown → algorithm falsely reports severe infection
- Powdery Mildew = white lesions → not detected by HSV dark-region threshold
- Background color contamination in field-collected images

**Replacement:** Grad-CAM heatmap spread percentage as severity proxy — color-agnostic, model-derived.

### Why ResNet18 for Rice Specifically?

| Model | Rice Accuracy |
|-------|--------------|
| ResNet18 | **88.60%** ✅ |
| EfficientNetB0 | 84.46% |
| MobileNetV2 | 81.35% |

Rice diseases (Blast, Brown Spot, Bacterial Blight) have extremely subtle texture differences. ResNet18's standard 3×3 convolutions with direct residual shortcuts preserve fine-grained spatial patterns better than EfficientNetB0's depthwise separable MBConv blocks.

---

## 7. Model Training — Per Crop Pipeline

Every crop follows the same 12-cell Jupyter notebook structure.

### Cell Structure (`agriminds_{crop}_pytorch.ipynb`)

| Cell | Purpose |
|------|---------|
| Cell 1 | Setup & GPU verify |
| Cell 2 | Transforms (train + val) |
| Cell 3 | ImageFolder load, 70/15/15 split, class names saved |
| Cell 4 | Class weights → CrossEntropyLoss |
| Cell 5 | MobileNetV2/ResNet18 build, frozen base |
| Cell 6 | `train_epoch()` + `eval_epoch()` functions |
| Cell 7 | Phase 1 training (10 epochs, EarlyStopping patience=3) |
| Cell 8 | Phase 2 fine-tune (5 epochs, lr=1e-5) |
| Cell 9 | Training accuracy/loss plot |
| Cell 10 | Test evaluation + confusion matrix |
| Cell 11 | Grad-CAM function |
| Cell 12 | Final `predict()` function |

### Phase 1 — Frozen Base Training

```python
# Freeze all base layers
for param in model.features.parameters():
    param.requires_grad = False

# Only train the classifier head
model.classifier[1] = nn.Linear(model.classifier[1].in_features, NUM_CLASSES)
optimizer = optim.Adam(filter(lambda p: p.requires_grad, model.parameters()), lr=0.001)
```

Trains for up to 10 epochs with EarlyStopping (patience=3). Best model saved as `{crop}_phase1.pt`.

### Phase 2 — Fine-Tuning

```python
# Unfreeze last 3 convolutional blocks
for layer in model.features[-3:]:
    for param in layer.parameters():
        param.requires_grad = True

optimizer2 = optim.Adam(
    filter(lambda p: p.requires_grad, model.parameters()),
    lr=1e-5  # Very small learning rate
)
```

Runs for 5 epochs. Best model saved as `{crop}_final_model.pt`.

### Class Weight Calculation

```python
from collections import Counter
from sklearn.utils.class_weight import compute_class_weight

train_labels = [full_dataset.targets[i] for i in train_ds.indices]
label_counts = Counter(train_labels)
total = len(train_labels)

class_weights = torch.tensor(
    [total / (NUM_CLASSES * label_counts[i]) for i in range(NUM_CLASSES)],
    dtype=torch.float
).to(device)

criterion = nn.CrossEntropyLoss(weight=class_weights)
```

### Training Loop

```python
def train_epoch(model, loader, criterion, optimizer, device):
    model.train()
    running_loss, correct, total = 0, 0, 0
    for images, labels in loader:
        images, labels = images.to(device), labels.to(device)
        optimizer.zero_grad()
        outputs = model(images)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()
        running_loss += loss.item() * images.size(0)
        _, preds = torch.max(outputs, 1)
        correct += (preds == labels).sum().item()
        total += labels.size(0)
    return running_loss / total, correct / total
```

---

## 8. Model Results

### Final Accuracy Table (Test Set)

| Crop | Best Model | Test Accuracy | Notes |
|------|-----------|---------------|-------|
| **Grape** | ResNet18 | **99.33%** | Cleanest dataset, clear visual differences |
| **Potato** | EfficientNetB0 | **98.76%** | Bold lesion patterns, well-balanced classes |
| **Tomato** | ResNet18 | **96.82%** | Large dataset, good diversity |
| **Maize** | ResNet18 | **92.68%** | Some Rust vs Blight confusion |
| **Wheat** | ResNet18 | **88.04%** | Subtle disease progression patterns |
| **Rice** | ResNet18 | **88.60%** | Hardest crop — high inter-class similarity |

### Model Architecture Comparison (All Crops)

| Crop | ResNet18 | EfficientNetB0 | MobileNetV2 | Chosen |
|------|---------|----------------|-------------|--------|
| Grape | 99.33% | 99.33% | 99.06% | ResNet18/EffNet |
| Maize | 92.68% | 91.88% | 91.24% | ResNet18 |
| Potato | 96.90% | **98.76%** | 97.52% | EfficientNetB0 |
| Rice | **88.60%** | 84.46% | 81.35% | ResNet18 |
| Tomato | **96.82%** | 96.21% | 96.33% | ResNet18 |
| Wheat | **88.04%** | 87.54% | 87.05% | ResNet18 |

### Why Rice is Hard (Technical Explanation)

Rice disease features include Leaf Blast, Brown Spot, and Bacterial Blight — all presenting as small dark/water-soaked lesions with extreme inter-class visual similarity. Additionally:
- High intra-class diversity across growth stages
- Subtle elongated lesion geometry that depthwise separable convolutions handle poorly
- EfficientNetB0's Squeeze-and-Excitation blocks channel-reweight features in a way that disadvantages elongated spatial patterns

ResNet18's standard 3×3 convolutions with direct residual shortcuts preserve fine-grained texture gradients, making it the better choice for rice specifically.

---

## 9. Grad-CAM Explainability

Gradient-weighted Class Activation Mapping (Grad-CAM) is used to highlight which regions of the leaf image the model focused on when making its prediction.

### How It Works

1. **Register forward hook** on the last convolutional layer (`model.features[-1]`)
2. **Register backward hook** to capture gradients
3. **Forward pass** — get model prediction
4. **Backward pass** — compute gradients of predicted class w.r.t. feature maps
5. **Weigh feature maps** by their global average gradient
6. **Apply ReLU** to remove negative contributions
7. **Resize and overlay** on original image using JET colormap

### Grad-CAM Implementation

```python
def gradcam_pytorch(model, img_path, class_names, device):
    model.eval()
    gradients, activations = [], []

    def fwd_hook(m, i, o): activations.append(o)
    def bwd_hook(m, i, o): gradients.append(o[0])

    target_layer = model.features[-1]
    target_layer.register_forward_hook(fwd_hook)
    target_layer.register_full_backward_hook(bwd_hook)

    img = T.Compose([
        T.Resize((224,224)), T.ToTensor(),
        T.Normalize(MEAN, STD)
    ])(Image.open(img_path).convert("RGB"))
    img_tensor = img.unsqueeze(0).to(device).requires_grad_(True)

    output = model(img_tensor)
    pred_idx = output.argmax(1).item()
    confidence = torch.softmax(output, dim=1)[0][pred_idx].item() * 100

    model.zero_grad()
    output[0, pred_idx].backward()

    grads  = gradients[0].cpu().detach().numpy()[0]
    acts   = activations[0].cpu().detach().numpy()[0]
    weights = grads.mean(axis=(1, 2))
    heatmap = np.sum(weights[:, np.newaxis, np.newaxis] * acts, axis=0)
    heatmap = np.maximum(heatmap, 0)
    heatmap = (heatmap - heatmap.min()) / (heatmap.max() - heatmap.min() + 1e-8)

    spread = round(float(np.sum(heatmap > 0.5) / heatmap.size * 100), 1)
    return class_names[pred_idx], confidence, spread
```

---

## 10. Severity Estimation

Severity is derived from the **Grad-CAM heatmap spread percentage** — the proportion of the leaf area where the heatmap activation exceeds the 0.5 threshold.

### Severity Mapping

| Spread % (Grad-CAM) | Severity Level |
|---------------------|---------------|
| < 25% | 🟢 Mild |
| 25% – 60% | 🟡 Moderate |
| > 60% | 🔴 Severe |

### Why This Approach (vs OpenCV Color Thresholding)

| Method | Works for Wheat Rust? | Works for Powdery Mildew? | Background Sensitive? |
|--------|-----------------------|--------------------------|----------------------|
| OpenCV HSV Thresholding | ❌ (wheat = naturally brown) | ❌ (white lesions not dark) | ❌ Yes |
| **Grad-CAM Spread %** | ✅ | ✅ | ✅ No |

---

## 11. Prediction Pipeline

### Full Predict Function

```python
def predict(img_path, class_names=class_names):
    disease, confidence, spread = gradcam_pytorch(model, img_path, class_names, device)

    tier = ("✅ High"      if confidence >= 85 else
            "🟡 Moderate"  if confidence >= 70 else
            "⚠️ Low — Retake Image")

    severity = ("🟢 Mild"     if spread < 25  else
                "🟡 Moderate" if spread <= 60  else
                "🔴 Severe")

    print(f"\n🌿 Crop      : {crop_name}")
    print(f"🦠 Disease   : {disease}")
    print(f"📊 Confidence: {confidence:.1f}% — {tier}")
    print(f"🔥 Spread    : {spread}% — {severity}")
    return disease, confidence, spread
```

### Sample Output

```
🌿 Crop      : Potato
🦠 Disease   : Early_Blight
📊 Confidence: 94.2% — ✅ High
🔥 Spread    : 38.5% — 🟡 Moderate
```

### Confidence Tier System

| Tier | Range | Action |
|------|-------|--------|
| ✅ High | ≥ 85% | Accept diagnosis |
| 🟡 Moderate | 70–84% | Accept with caution |
| ⚠️ Low | < 70% | Request retake |

---

## 12. Advanced Patent Features

Six features were planned and a prior-art novelty scan was performed. Below is the summary.

### Feature 1 — Failure-Specific Adaptive Reacquisition Controller ⭐ STRONGEST

After each image, compute a multi-dimensional **failure vector**:
- `background_bias_risk` — is the model relying on background?
- `blur_risk` — is the image too blurry?
- `leaf_coverage_low` — is the leaf taking up enough frame area?
- `patch_vs_full_disagreement` — do lesion patch and full leaf predict the same class?
- `view_redundancy` — is this image a near-duplicate of a previous one?
- `ambiguous_alternative_class` — is there a close-second predicted class?

Map each failure to a specific next-capture instruction and withhold acceptance until evidence-completion state is satisfied.

**Novelty:** No plant-disease paper or patent found that defines a multi-dimensional failure vector with a deterministic mapping to prescribed next capture instructions and an evidence-completion gate.

### Feature 2 — Patch-vs-Whole Contradiction Gate

Use Grad-CAM to extract most-lesion-dense patches. Run classifier on both:
- Full leaf → prediction A
- Lesion patch crop → prediction B

If A ≠ B, trigger reacquisition request for specific resolving view.

**Novelty:** Patch-level CAM models are known, but using patch–whole disagreement as a workflow control signal for plant disease diagnosis is relatively uncovered.

### Feature 3 — Background-Reliance Invariance Audit

Generate test variants at runtime:
- Original image → prediction
- Leaf-only (segmented) → prediction
- Background-suppressed → prediction

If class changes or confidence swings too much across variants → trigger "plain-background retake" request.

**Novelty:** Background bias is documented in PlantVillage, but turning it into a deployment-time acceptance gate is new.

### Feature 4 — Diversity-Aware Evidence Gain Filter

For multi-image sessions, score each new image for evidence gain:
- Change in viewpoint, leaf instance, lesion region, focus quality
- Near-duplicates discounted or ignored
- Only update plant diagnosis state if diversity threshold met

### Feature 5 — Same-Plant Healthy-Reference Control Capture

When ambiguity remains, ask user for one healthy leaf from the same plant. Use Siamese-style comparison to cancel lighting, background, and crop-specific confounds.

### Feature 6 — Longitudinal Progression Consistency Memory

Store per-plant diagnosis fingerprints. For new images of the same plant, check temporal compatibility with expected disease progression. Trigger reacquisition if inconsistent.

### Patent Novelty Assessment Summary

| Feature | Crowding Level | Strongest Claim Angle |
|---------|---------------|----------------------|
| Reacquisition Controller | **Low** | State-machine + failure vector + view prompts |
| Patch–Whole Gate | Low–Moderate | Disagreement as acquisition trigger |
| Background Audit | Moderate | Operational acceptance gate (not just analysis) |
| Diversity Filter | **Low** | Evidence-gain scoring for multi-image workflow |
| Healthy Reference Capture | Moderate | Runtime control capture protocol |
| Longitudinal Memory | Low–Moderate | Progression consistency as deployment sanity check |

---

## 13. Errors Encountered & Fixes

| Error | Root Cause | Fix Applied |
|-------|-----------|------------|
| `No DNN in stream executor` | TF compiled with cuDNN 9.3, runtime has 9.1 | Switched to PyTorch |
| `InvalidArgumentError` class_weight shape mismatch | TF expects different format | Switched to PyTorch `CrossEntropyLoss(weight=...)` |
| `Loaded runtime CuDNN 9.1.0 but compiled with 9.3.0` | Version mismatch warning | Warning only in PyTorch — not a crash |
| `No module named pip` during nvidia-pyindex install | Conda env pip issue | Resolved via conda pip reinstall |
| `TF 2.11+ Windows GPU not supported` | TF dropped Windows GPU support | WSL2 + PyTorch instead |

---

## 14. File Structure

```
~/agriminds/
├── notebooks/
│   ├── agriminds_potato_pytorch.ipynb     ✅ Complete
│   ├── agriminds_rice_pytorch.ipynb       ✅ Complete
│   ├── agriminds_maize_pytorch.ipynb      ✅ Complete
│   ├── agriminds_wheat_pytorch.ipynb      ✅ Complete
│   ├── agriminds_grape_pytorch.ipynb      ✅ Complete
│   └── agriminds_predict.ipynb            ✅ Complete
│
├── models/
│   ├── potato_final_model.pt
│   ├── potato_classes.json
│   ├── rice_final_model.pt
│   ├── rice_classes.json
│   ├── maize_final_model.pt
│   ├── maize_classes.json
│   ├── wheat_final_model.pt
│   ├── wheat_classes.json
│   ├── grape_final_model.pt
│   └── grape_classes.json
│
├── outputs/
│   ├── potato_confusion_matrix.png
│   ├── potato_training_plot.png
│   ├── rice_confusion_matrix.png
│   ├── rice_training_plot.png
│   ├── maize_confusion_matrix.png
│   ├── maize_training_plot.png
│   ├── wheat_confusion_matrix.png
│   ├── wheat_training_plot.png
│   ├── grape_confusion_matrix.png
│   └── grape_training_plot.png
│
└── docs/
    ├── agriminds_complete_project.md   ← this file
    └── agriminds_patent_features.md
```

---

## 15. Deliverables

| Deliverable | Status | Notes |
|-------------|--------|-------|
| 5 trained models (.pt) | ✅ Complete | Per-crop, 2-phase transfer learning |
| 5 class JSON files | ✅ Complete | Required for inference |
| 5 confusion matrix PNGs | ✅ Complete | Per-crop test evaluation |
| 5 training plot PNGs | ✅ Complete | Loss + accuracy curves |
| `agriminds_predict.ipynb` | ✅ Complete | Full predict() with Grad-CAM |
| Severity slider | ✅ Complete | Grad-CAM spread → Mild/Moderate/Severe |
| Patent novelty scan | ✅ Complete | 6 features analyzed |
| Project documentation (this file) | ✅ Complete | — |

---

## 16. Viva Preparation

### Slide 1 — Problem Statement
> "Farmers rely on visual inspection for disease diagnosis — slow and error-prone. We built AgriMinds: an AI-powered instant diagnosis system for 5 major Indian crops."

### Slide 2 — Why Per-Crop Models?
> "A single universal model showed inter-crop confusion — rice blast was confused with wheat rust. Per-crop models are more accurate, modular, and easier to update. Each model is trained only on its crop's diseases."

### Slide 3 — Why We Dropped OpenCV Severity
> "Rule-based HSV thresholding fails for Powdery Mildew (white lesions) and wheat at harvest (naturally brown). We replaced it with Grad-CAM spread percentage — which is model-derived and color-agnostic."

### Slide 4 — Why ResNet18 for Rice?
> "Rice diseases have extremely subtle inter-class visual differences. EfficientNetB0's depthwise separable convolutions trade off fine-grained texture discrimination for efficiency. ResNet18's standard convolutions preserved spatial gradients better, giving 88.60% vs EfficientNetB0's 84.46%."

### Slide 5 — What is Grad-CAM?
> "Gradient-weighted Class Activation Mapping — we compute gradients of the predicted class with respect to the last convolutional layer's feature maps, weighted by global average pooling. This produces a heatmap showing exactly which leaf regions drove the model's decision. It requires no additional training."

### Slide 6 — Results
> "Grape: 99.33% | Potato: 98.76% | Tomato: 96.82% | Maize: 92.68% | Rice: 88.60% | Wheat: 88.04%"

### Slide 7 — Demo
> Live leaf photo upload → disease prediction + confidence tier + Grad-CAM heatmap + severity slider in under 2 seconds.

### Common Viva Questions

| Question | Answer |
|----------|--------|
| Why MobileNetV2 and not ResNet50? | MobileNetV2 is lightweight for mobile deployment; ResNet50 would be overkill and slower for inference on limited hardware |
| What is transfer learning? | Using a model pretrained on ImageNet (1.2M images, 1000 classes) as a starting point and fine-tuning on our domain-specific data — much better than training from scratch |
| What is class imbalance and how did you fix it? | Some diseases have fewer samples; CrossEntropyLoss(weight=) penalizes the model more for misclassifying minority classes |
| Why 70/15/15 split? | 70% for training, 15% for hyperparameter tuning (val), 15% for unbiased final evaluation (test). Using test set for tuning would be data leakage |
| What are the limitations? | PlantVillage background bias; performance may drop on field images with complex backgrounds; no multilingual support; no real-time video |

---

## 17. Future Scope

1. **Fertilizer Recommendation Engine** — Map disease + crop to specific treatment (e.g., "Early Blight → Mancozeb 0.25% spray")
2. **Market Price Integration** — Live Agmarknet API for ₹/kg crop prices with sell/hold suggestion
3. **Failure-Specific Reacquisition Controller** — AI-guided multi-shot workflow (see Section 12)
4. **Mobile App Deployment** — Convert models to ONNX/TFLite for Android field use
5. **Multilingual Output** — Hindi, Marathi disease names and treatment instructions
6. **Longitudinal Plant Health Tracking** — Store plant history and check disease progression over time
7. **Field Dataset Collection** — Reduce PlantVillage bias by collecting local field images

---

*Last updated: June 2026 | Project: AgriMinds PBL | Kashif, Pune*
