# Known Limitations

AgriMinds is still a research project. These issues need to be addressed before it can be trusted in production.

## Older results

The results saved in the original notebooks were produced before the current audit. Some notebooks used different splitting, checkpoint, and test-time augmentation choices, so their numbers should not be compared directly with the corrected models.

## Field conditions

Many training images are cleaner than photographs taken on a farm. Different phones, lighting, soil backgrounds, growth stages, pests, nutrient problems, and mixed diseases may reduce accuracy.

## Unknown inputs

The old classifiers always choose one of their known labels. The application needs a safe way to reject the wrong crop, non-leaf photographs, unknown diseases, poor images, and weak predictions.

## Confidence

A large softmax score does not automatically mean that a prediction is reliable. Confidence needs to be calibrated and checked again when the models are tested outside the training environment.

## Grad-CAM and severity

A Grad-CAM heatmap is an explanation of model attention. It is not proof of disease or a direct measurement of damaged leaf area. Severity requires separate labels and expert review.

## Treatment guidance

Wrong chemical or fertilizer advice can damage crops and create safety risks. Treatment information needs reviewed sources, version history, regional checks, and clear escalation to an expert when the diagnosis is uncertain.

## Android performance

The models have not yet been benchmarked on the low-cost phones the project is designed for. Final decisions about model format and packaging will be made after measuring size, speed, memory, battery use, and heat.
