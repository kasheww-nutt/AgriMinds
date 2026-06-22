# Project Architecture

## How the pieces fit together

AgriMinds is the disease-detection part of the wider FarmWise application. The farmer selects a crop and takes a photograph. The app prepares the image, runs the model for that crop, and either shows a result or asks for better evidence.

```text
Take photographs
      ?
Check image quality
      ?
Run the selected crop model
      ?
Check confidence and disagreement
      ?
Show a result, request another image, or suggest expert help
      ?
Explain the result and begin follow-up care
```

## Models

The current plan uses ResNet18 for Grape, Maize, Rice, Tomato, and Wheat. Potato uses EfficientNet-B0. The app will load only the model for the crop selected by the farmer.

The ensemble notebook is useful for comparison, but running several large models for every photograph would make the Android app heavier and slower. It is not part of the default mobile plan.

## Training rules

Every corrected crop notebook should follow the same rules: repeatable seeds, saved stratified splits, separate transforms, macro-F1 checkpoint selection, one best checkpoint across all training phases, confidence calibration, and a final single-pass test.

## Prediction results

The app should not always force a disease name. A prediction can return one of four states:

```text
ok | uncertain | invalid_image | unsupported_input
```

An accepted result records the crop, disease, calibrated confidence, top alternatives, model version, preprocessing version, and class-map version. Treatment guidance should not be shown for an uncertain result.

## Language and care guidance

A future online service may turn an accepted result into simple, localized care instructions. The crop model remains responsible for the diagnosis. The language service cannot replace the disease label or invent products, dosages, or schedules.

API credentials belong on a protected server, not inside the Android application.

## Severity

Grad-CAM shows the parts of an image that influenced the model. It does not measure infected area by itself. The severity feature will be tested separately and must be able to say that an estimate is not reliable.
