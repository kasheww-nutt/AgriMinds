# AgriMinds

AgriMinds is a plant disease detection project built for farmers who may not have access to expensive phones or reliable internet. The project uses a separate model for each supported crop so diagnosis can eventually run directly on an Android device.

The project currently supports Grape, Maize, Potato, Rice, Tomato, and Wheat.

> AgriMinds is still being tested and improved. It should not be treated as a replacement for advice from an agricultural expert.

## What the project is trying to solve

A farmer should be able to select a crop, photograph an affected leaf, and receive a useful result without needing to understand machine learning. The long-term flow is simple:

1. Take clear photographs of the affected plant.
2. Check whether the photographs are usable and show different views.
3. Run the correct crop model.
4. Show the likely disease, confidence, and an explanation of the result.
5. Ask for another photograph when the result is uncertain.
6. Help the farmer follow treatment steps and record whether the plant improves.

The disease prediction will come from models trained and evaluated in this project. A future language service may explain an accepted result in the farmer's language, but it will not be allowed to replace the model's diagnosis or invent chemical dosages.

## Crops and current models

| Crop | Classes | Current model | Result saved in the old notebook* |
|---|---:|---|---:|
| Grape | 4 | ResNet18 | ~100% |
| Maize | 4 | ResNet18 | ~94% |
| Potato | 3 | EfficientNet-B0 | ~95% |
| Rice | 4 | ResNet18 | ~88% |
| Tomato | 4 | ResNet18 | ~98% |
| Wheat | 4 | ResNet18 | ~88% |

\*These numbers are included only as a record of earlier experiments. The old notebooks were created before the current audit, so the numbers are not being presented as final or production-ready results.

## Current work

The existing notebooks are being reviewed and rebuilt one crop at a time. The corrected training process will use fixed stratified splits, repeatable seeds, separate transforms, macro-F1 checkpoint selection, confidence calibration, and a final single-pass test that matches mobile inference.

The first corrected notebook is [`Notebooks/crop approach/potato_corrected.ipynb`](Notebooks/crop%20approach/potato_corrected.ipynb). The original notebooks under `crops/` are kept as a record of the earlier work.

The Android plan is to keep one final model for each crop and load only the model selected by the farmer. Running several large models for every photograph is not part of the current production plan.

## Repository layout

```text
AgriMinds/
??? Backend/          # Notes for future online services
??? Data/             # Dataset instructions; image data is not stored here
??? Deployment/       # Export and Android testing plans
??? Docs/             # Maintained project documentation
??? Frontend/         # Notes for the FarmWise application
??? Models/           # Model artifact rules and future model cards
??? Notebooks/        # Corrected and reusable notebooks
??? crops/            # Original crop experiments and class maps
??? md files/         # Older project notes waiting to be reviewed
??? SML LOGIC/        # Earlier ensemble experiment
```

## Running the notebooks

Python 3.12 is currently used for the research environment.

```bash
python -m venv .venv

# Windows PowerShell
.venv\Scripts\Activate.ps1

pip install -r requirements.txt
jupyter lab
```

The dataset is not included in this repository. Set `AGRIMINDS_DATA_ROOT` to the folder containing the crop directories:

```powershell
$env:AGRIMINDS_DATA_ROOT = "D:\path\to\prepro_pbl_dataset"
```

```text
prepro_pbl_dataset/
??? grape/
??? maize/
??? potato/
??? rice/
??? tomato/
??? wheat/
```

## Files that are not committed

The repository does not commit datasets, generated outputs, API credentials, local environment files, or model checkpoints. Approved model files will be published separately after their results, file hashes, and usage rights have been checked.

The original data sources also need a proper licensing and attribution review before any public release.

## Planned application features

After the six crop models are corrected, the project will move toward Android testing, guided multi-image diagnosis, uncertain-result handling, treatment reminders, follow-up photographs, and disease progress tracking. The order of work is recorded in [`Docs/ROADMAP.md`](Docs/ROADMAP.md).

## Important limitations

The models can be wrong. Results may change with poor lighting, complex field backgrounds, unsupported crops, unfamiliar diseases, nutrient deficiencies, pests, or damaged leaves. Low-confidence and contradictory results should be rejected instead of turned into confident treatment advice.

Grad-CAM images show which regions influenced a prediction. They do not directly measure infected area. Severity estimates will remain experimental until they are compared against expert-labelled examples.

Treatment information must come from reviewed agricultural sources. The application should never invent pesticide names, concentrations, or schedules.

## Repository status

This repository is private while the project reviews its research results and possible intellectual-property work. No open-source license has been selected. Please do not redistribute the code, models, or unpublished project ideas without permission.
