# Project Roadmap

## 1. Clean up the project

Keep the repository private, separate current documentation from old notes, and make sure datasets, secrets, generated files, and model weights are not added to normal Git history.

## 2. Correct all six crop notebooks

The old notebooks stay as a record of the original experiments. Each crop will receive a corrected runner that follows the same split, training, checkpoint, calibration, and evaluation rules.

Potato is the first corrected notebook. Grape, Maize, Rice, Tomato, and Wheat will follow after Potato runs successfully from a clean kernel.

## 3. Review the dataset

Dataset work starts only after the model pipeline is stable. The review will check corrupt images, duplicate images, labels, class balance, source leakage, background bias, and field-image coverage.

## 4. Test models on Android

The six final models will be exported and checked against Python using the same test images. Package size, startup time, inference speed, RAM, battery use, and heat will be measured on realistic low-cost phones.

## 5. Add guided multi-image diagnosis

The app will guide the farmer through useful views, reject blurry or repeated images, combine reliable predictions, and ask for another photograph when the evidence conflicts.

## 6. Add care plans and progress tracking

Accepted diagnoses can start a care plan based on reviewed agricultural information. The app will schedule reminders, collect follow-up photographs, and show whether the plant appears to be improving, stable, or worsening.

## 7. Field testing

Before calling the system production-ready, it needs field images, agricultural expert review, safety checks, language and accessibility testing, privacy review, and a limited pilot with real users.
