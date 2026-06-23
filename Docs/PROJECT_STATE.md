# AgriMinds Project State

Last verified: 23 June 2026  
Git branch: `main`  
Latest context-pack commit at creation: `6669f2f`

## Status summary

| Area | Status | Evidence / next action |
|---|---|---|
| Private GitHub repository | Complete | `kasheww-nutt/AgriMinds` |
| Human-readable repository docs | Complete | README, architecture, roadmap, limitations, security |
| Durable project context | Complete | `Docs/PROJECT_CONTEXT.md` |
| Accepted-decision record | Complete | `Docs/DECISIONS.md` |
| Shared pipeline specification | Complete in documentation | `Docs/PIPELINE_SPEC.md`; shared Python package not built |
| Legacy crop notebooks | Preserved | `crops/` |
| Corrected Potato runner | Implemented and run | `Notebooks/crop approach/potato_corrected.ipynb` |
| Corrected Grape runner | Not started | Use shared specification after core extraction |
| Corrected Maize runner | Not started | Use shared specification after core extraction |
| Corrected Rice runner | Not started | Use shared specification after core extraction |
| Corrected Tomato runner | Not started | Use shared specification after core extraction |
| Corrected Wheat runner | Not started | Use shared specification after core extraction |
| Shared `agriminds_ml` package | Not started | Extract from corrected Potato reference |
| Synthetic pipeline tests | Not started | Required before copying crop runners |
| Dataset audit | Not authorized | Do not inspect without explicit request |
| Multi-seed Potato conclusion | Incomplete | Latest artifact is one run; seeds must be compared |
| Clean final holdout | Missing | Current Potato test has influenced development |
| Safe leaf/symptom segmentation report | Implemented in Potato notebook / execution pending | Five-panel report code and dataset demo cell added to `Notebooks/crop approach/potato_corrected.ipynb`; run in user notebook environment |
| Severity validation | Not started | Requires expert-labelled subset |
| Guided multi-image diagnosis | Proposed | No aggregation implementation or UX yet |
| Treatment tracking/reminders | Proposed | No app implementation yet |
| RAG/LLM care advisory | Proposed | Diagnosis boundary decided; no service exists |
| Android application integration | Not started in this repository | Export/parity/device benchmarks required |
| Ensemble production path | Excluded | Historical notebook remains research-only |

## Latest verified Potato evidence

| Item | Value |
|---|---|
| Seed in current corrected notebook | 2026 |
| Architecture | EfficientNet-B0 |
| Champion phase | Phase-one head |
| Test accuracy | 97.21% |
| Test macro-F1 | 96.20% |
| Test balanced accuracy | 96.77% |
| Healthy F1 | 93.62% on 23 images |
| Calibrated ECE | 2.20% |
| Rejection coverage | 96.59% |
| Accepted accuracy | 98.40% |
| Phase-two result | Stable, but below phase-one global best |

## Immediate technical risks

1. The Potato result is a single run with a small Healthy test support.
2. The current Potato test set has already guided development and is not a clean final holdout.
3. Validation currently serves checkpoint selection, temperature fitting, and rejection-policy selection.
4. The shared pipeline still lives inside one notebook rather than tested modules.
5. No dataset-source/duplicate audit or external field evaluation is available.
6. No Android latency, RAM, size, battery, or thermal benchmark exists.
7. Severity and treatment guidance are not validated.

## Ordered next work

0. **Build the safe leaf/symptom segmentation and five-panel visual evidence report.** This is now the highest-priority feature path because it makes the model output understandable while exposing unreliable cases instead of hiding them.
1. Define the visual-report contract: original image, leaf region, model-attention region, symptom-colour evidence, final possible-affected overlay, plus `ok` / `cannot_estimate` status.
2. Implement the Potato version first as a separate post-inference module/section; do not merge it into training or checkpoint selection.
3. Add refusal gates for low confidence, weak leaf mask, poor image quality, tiny leaf area, attention/evidence disagreement, and healthy predictions.
4. Save visual artifacts and a small JSON report with labels that say `possible affected area estimate`, never `exact disease area`.
5. Only after the visual-report path is stable, extract reusable Potato logic into a shared package without changing behaviour.
6. Add synthetic tests for split isolation, sampler behaviour, BatchNorm freezing, checkpoint retention, class mapping, inference status, and visual-report refusal gates.
7. Reduce the Potato notebook to a thin runner and reproduce the current result shape.
8. Create corrected runners for the remaining crops using configuration, not copied training code.
9. Revisit calibration/final-holdout design before making final model claims.
10. Perform dataset auditing only after the user explicitly authorizes it.

## Priority 0 TODO: safe segmentation and visual evidence report

- [x] Create a Potato-only post-inference report section/module.
- [x] Add a lightweight image-quality check: blur, exposure, minimum resolution, and leaf coverage.
- [x] Create a leaf-region mask using conservative colour priors plus GrabCut/fallback logic.
- [x] Generate Grad-CAM as `model attention`, not lesion area.
- [x] Detect symptom-coloured pixels only inside the trusted leaf region.
- [x] Combine leaf mask, attention support, and symptom evidence into a `possible affected area` overlay.
- [x] Return `cannot_estimate` when gates fail instead of forcing a severity number.
- [x] Save `original`, `leaf_region`, `attention_map`, `symptom_pixels`, `final_overlay`, and `report_summary.json`.
- [ ] Run the demo cell in the user notebook environment and visually inspect the generated five-panel report.
- [ ] Later, validate masks/severity against expert-labelled masks or grades before claiming production severity.

## Handoff rule

Every work chat must update this file when it changes project-wide status. Crop-only results belong primarily in `Docs/crops/<crop>.md`, with only a summary change here.

