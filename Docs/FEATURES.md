# AgriMinds Feature Registry

Last verified: 23 June 2026

This registry prevents planned ideas from being described as working features. ?Potential invention direction? is a brainstorming label, not a patentability conclusion.

## Implemented or evidenced today

| Feature | Status | Category | Notes |
|---|---|---|---|
| Six crop-specific legacy classifiers | Historical implementation | Standard ML | Original notebooks and local checkpoints exist; metrics remain historical. |
| Transfer learning with ResNet18/EfficientNet-B0 | Historical + corrected Potato | Standard ML | Common computer-vision approach. |
| Corrected Potato stratified runner | Implemented | Strong engineering | Persisted split, class contract, deterministic execution. |
| Runtime-balanced Potato sampling | Implemented | Strong engineering | Minority samples receive repeated randomized views; not new biological data. |
| Global phase checkpoint retention | Implemented in corrected Potato | Strong engineering | Phase two cannot replace a superior phase-one checkpoint. |
| BatchNorm-safe Potato fine-tuning | Implemented | Strong engineering | Prevents small resampled data from shifting pretrained running statistics. |
| Macro-F1 champion selection | Implemented in corrected Potato | Strong engineering | Protects minority-class visibility. |
| Temperature calibration | Implemented in corrected Potato | Strong safety feature | Current protocol still reuses validation data and is not final. |
| Uncertainty rejection | Implemented in corrected Potato | Strong safety feature | Returns `uncertain`; validation-fitted threshold needs a stronger final protocol. |
| Single-pass test report | Implemented in corrected Potato | Required production alignment | No TTA in the corrected report. |
| Legacy Grad-CAM/spread visualizations | Historical prototype | Standard/unsafe as written | Old logic can output severity after low confidence or segmentation failure. |
| Historical three-model ensemble comparison | Historical research | Standard ML research | Hard/soft voting and stacking exist; excluded from the default mobile path. |

## Planned strong product features

| Feature | Status | Why it matters |
|---|---|---|
| One champion model per crop on-device | Direction accepted; not deployed | Offline use and predictable device cost. |
| Guided two-to-three-image capture | Proposed | Helps farmers provide useful views. |
| Image-quality gate | Proposed | Rejects blur, bad exposure, poor framing, or insufficient leaf coverage. |
| Near-duplicate view rejection | Proposed | Prevents repeated photographs from creating fake consensus. |
| Calibrated multi-view consensus | Proposed | Combines evidence more safely than one-image confidence. |
| Safe leaf/symptom segmentation report | Priority 0 | Five-panel visual evidence report: original, leaf region, attention, symptom pixels, and possible affected overlay. |
| Care-plan tasks and local reminders | Proposed | Turns diagnosis into clear follow-up actions. |
| Follow-up image comparison | Proposed | Tracks whether the plant appears to improve or worsen. |
| Expert escalation | Proposed | Gives uncertain, contradictory, or worsening cases a safe path. |
| RAG-based multilingual guidance | Proposed | Retrieves reviewed information before an LLM explains it; RAG itself is not novel. |

## Potential invention directions

These require prior-art searching, implementation, comparative experiments, and professional legal review.

| Direction | Status | Potentially distinctive element |
|---|---|---|
| Failure-specific reacquisition controller | Proposed | Maps a measured failure vector to a specific next photograph instead of a generic retake. |
| Evidence-completion state machine | Proposed | Withholds diagnosis until required evidence conditions are satisfied. |
| Diversity-aware evidence gain | Proposed | Accepts a new image only when it adds a useful view, lesion region, or quality improvement. |
| Patch-versus-whole contradiction gate | Proposed | Uses disagreement between full-leaf and lesion-patch predictions to control capture. |
| Background-reliance acceptance gate | Proposed | Turns background sensitivity into a live rejection and retake decision. |
| Same-plant healthy-reference capture | Proposed | Uses a healthy leaf from the same plant as a runtime control. |
| Progression-consistency gate | Proposed | Checks whether new evidence is compatible with earlier evidence and expected progression. |
| Closed diagnosis-care-verification loop | Proposed umbrella concept | Diagnosis controls follow-up; follow-up evidence controls continuation, change, or escalation. |

## Standard supporting features

Useful but not technical novelty: crop selection, confidence display, confusion matrices, login, user roles, marketplace/cart, weather, government schemes, language selection, generic notifications, basic majority voting, a generic chatbot, and Grad-CAM alone.

## Safety boundaries

- The crop model owns diagnosis.
- An LLM may explain an accepted diagnosis but cannot replace it.
- Treatment guidance must come from reviewed sources.
- No severity percentage is trusted after segmentation or evidence failure.
- Low-confidence or contradictory evidence must not trigger chemical advice.
- Patent language remains provisional until reviewed.

## Priority 0 implementation TODO

- Potato first, then generalize.
- Use the existing classifier output only after it is accepted by confidence/rejection gates.
- Use leaf segmentation to remove background before any symptom-pixel logic.
- Treat Grad-CAM/Grad-CAM++ as attention support only.
- Use colour/texture symptom pixels as weak visual evidence, not diagnosis.
- Combine evidence only inside the trusted leaf region.
- Output `cannot_estimate` instead of fake precision when gates fail.
- Keep wording honest: `possible affected area estimate`, not `exact disease area`.