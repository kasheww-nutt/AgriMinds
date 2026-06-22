# AgriMinds Decisions

Last verified: 23 June 2026

This file contains accepted project decisions. Proposed features and unresolved choices are listed separately so future chats do not silently convert discussion into scope.

## Accepted decisions

| ID | Decision | Consequence |
|---|---|---|
| D-001 | Keep the GitHub repository private for now. | Patent-sensitive details and unfinished research are not intentionally published. |
| D-002 | Preserve original crop notebooks as legacy evidence. | Corrected runners are created separately; old metrics remain historical. |
| D-003 | Build toward shared pipeline logic with thin crop notebooks. | Crop-specific work must not create six incompatible training implementations. |
| D-004 | Keep current architecture choices unless controlled evidence justifies reopening them. | ResNet18 remains the direction for five crops; Potato remains EfficientNet-B0. |
| D-005 | Retrain all six crops under the corrected pipeline. | Existing `.pt` files are baselines, not final production artifacts. |
| D-006 | Use validation macro-F1 as the primary checkpoint-selection metric. | Accuracy alone cannot hide weak minority-class performance. |
| D-007 | Official production evaluation is single-pass without TTA. | Research TTA numbers cannot be presented as Android performance. |
| D-008 | Do not use a heavy three-model ensemble as the default on-device path. | Ensemble work remains research-only unless device evidence reopens the decision. |
| D-009 | Target one champion model per crop and load only the selected crop model. | Storage and Android packaging still require later benchmarking. |
| D-010 | Keep diagnosis under project-owned crop models. | Gemini/LLMs may explain grounded results but cannot diagnose or override the model. |
| D-011 | Treat Grad-CAM as explanation, not severity ground truth. | Severity must support refusal and requires expert-labelled validation. |
| D-012 | Do not audit dataset contents until explicitly requested. | Crop-folder/class mapping is allowed; image scanning is currently out of scope. |
| D-013 | Use runtime-balanced sampling for corrected Potato imbalance handling. | Repeated minority draws receive fresh augmentation; loss remains unweighted. |
| D-014 | Freeze BatchNorm during corrected Potato fine-tuning and preserve the global phase-one champion. | Phase two is allowed to lose; it replaces phase one only after a real macro-F1 improvement. |
| D-015 | Keep generated artifacts and model binaries out of normal Git history. | Reports and checkpoints remain local until an artifact-release policy is approved. |

## Proposed directions, not accepted implementation scope

| Proposal | Current status |
|---|---|
| Guided two-to-three-image diagnosis | Discussed; aggregation and UX not locked |
| Diversity-aware evidence weighting | Discussed; not implemented |
| Failure-specific retake controller | Candidate differentiator; not implemented |
| Patch-versus-whole disagreement gate | Discussed; not implemented |
| Background-reliance acceptance gate | Discussed; not implemented |
| Same-plant healthy-reference capture | Discussed; not implemented |
| Five-panel diagnostic/severity report | Desired presentation; safe logic not implemented |
| Treatment plans and reminders | Discussed; knowledge source and safety policy not locked |
| Follow-up progression tracking | Discussed; comparison method not implemented |
| RAG/LLM care-advisory pipeline | Discussed; diagnosis boundary accepted, implementation not started |

## Open decisions

| Question | What must be known first |
|---|---|
| Exact Android deployment format | Export compatibility and device benchmarks |
| Final model compression/precision | Accuracy, size, and latency measurements |
| Whether phase two remains in every crop pipeline | Multi-seed validation evidence per crop |
| Final calibration/rejection method | A clean calibration protocol and untouched evaluation data |
| Multi-image aggregation rule | Validation experiments using realistic multi-view cases |
| Severity implementation | Expert-labelled masks/grades and reliability targets |
| Treatment knowledge source | Expert review, regional rules, and source versioning |
| Public-release timing and license | IP review, dataset licensing, and model-artifact review |

## Decision-change rule

Do not overwrite an accepted decision silently. Add a new dated decision stating what changed, why it changed, and which artifacts or measurements support the change.

