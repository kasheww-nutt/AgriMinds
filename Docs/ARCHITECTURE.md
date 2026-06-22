# AgriMinds Architecture

## System boundary

AgriMinds is the diagnostic engine intended for the broader FarmWise product. Crop diagnosis is performed by project-owned models. The mobile application selects a crop, preprocesses evidence using the model specification, runs the corresponding champion, and returns either an accepted prediction or an uncertainty state.

```text
Guided image capture
        ↓
Image quality and evidence checks
        ↓
Selected crop champion model
        ↓
Calibration and uncertainty gate
        ↓
Accepted diagnosis ── or ── targeted retake / expert escalation
        ↓
Explainability and validated severity
        ↓
Grounded care plan and scheduled follow-up
```

## Model strategy

Five crops currently use ResNet18; Potato uses EfficientNet-B0. The production direction is one champion per crop, loaded only when selected. Heavy live model ensembles are excluded from the default Android path. Ensemble and TTA measurements are research-only unless later device benchmarks justify them.

## Training contract

Corrected crop training must use deterministic execution, persisted stratified manifests, independent transforms, macro-F1 checkpointing, global best-state retention across phases, calibrated confidence, and a sealed single-pass test evaluation.

## Inference contract

Inference returns a structured status instead of forcing a disease label:

```text
ok | uncertain | invalid_image | unsupported_input
```

An accepted result includes crop, disease, calibrated confidence, top alternatives, model identity, preprocessing version, and class-map identity. Treatment guidance is unavailable for rejected diagnoses.

## Advisory boundary

A future Gemini/API layer may translate and personalize expert-reviewed care guidance after a model diagnosis is accepted. It is not the diagnosis authority and may not invent pesticides, concentrations, intervals, or conflicting disease labels. API credentials must never be embedded in the Android application.

## Severity boundary

Grad-CAM++ is model attention, not lesion segmentation. Numerical severity remains experimental until an expert-labelled subset validates the evidence-fusion method. The severity interface must support `cannot_estimate`.

