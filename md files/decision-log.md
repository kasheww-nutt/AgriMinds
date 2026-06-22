# AgriMinds Decision Log

## Purpose and Usage

This file is a working internal note for documenting project decisions and the reasoning around them.
It is meant to help the team remember why a path was chosen, why other paths were not preferred, and which topics are still open.
Confirmed decisions should stay short and stable.
Discussion points that are useful but not fully locked should stay in the working-notes section instead of being written as final conclusions.

## Confirmed Decisions

### Plan the pipeline and audit logic before touching dataset work

**Decision:**  
Define the end-to-end pipeline and perform a rigorous logic review of the existing project before starting dataset-level work.

**Status:**  
Accepted

**Why we chose it:**  
The current priority is to expose structural issues first, from minor notebook mistakes up to larger deployment and validation loopholes. That gives a cleaner base for any later model comparison or ensemble experiment.

**Why we did not choose alternatives:**  
Jumping into dataset work too early can hide pipeline flaws and mix root-cause analysis with data issues, which makes debugging slower and less reliable.

**Constraints considered:**  
Time, debugging clarity, model comparability, and implementation discipline.

**Impact on app / training / deployment:**  
The project should first lock pipeline stages, review assumptions, and identify failure points before deeper training or data expansion work.

**Revisit when:**  
Reopen only after the base pipeline audit is complete and the remaining blocker is clearly dataset-related.

### Keep the existing crop-notebook pipeline as the base path

**Decision:**  
Use the existing crop folder notebooks and pipeline as the main base path for the project.

**Status:**  
Accepted

**Why we chose it:**  
The current crop notebooks already represent the main working path. Building from them keeps effort focused on improving the existing system instead of restarting the project around a new training strategy.

**Why we did not choose alternatives:**  
A full ensemble-first rebuild would add rework, delay validation, and increase project complexity before the current baseline is properly fixed and hardened.

**Constraints considered:**  
Project time, implementation stability, validation effort, and development clarity.

**Impact on app / training / deployment:**  
Model and pipeline improvements should be layered onto the current crop workflow first. New strategies should fit into this base unless there is strong evidence that the base path is not viable.

**Revisit when:**  
Reopen only if the current crop-model pipeline cannot meet required quality or deployment constraints after proper fixes and validation.

### Avoid heavy live multi-model ensemble inference on-device

**Decision:**  
Do not make heavy live multi-model ensemble inference the default on-device production path.

**Status:**  
Accepted

**Why we chose it:**  
The app is intended for farmers, so device limitations, runtime cost, and package size have direct product impact. A lighter inference path is more practical.

**Why we did not choose alternatives:**  
Running several models together on-device would increase compute load, packaging pressure, and implementation overhead without first solving the more basic issues in the pipeline.

**Constraints considered:**  
Farmer device capability, APK size, inference speed, battery use, and maintainability.

**Impact on app / training / deployment:**  
Deployment choices should prefer a compact inference setup. Any ensemble-style idea must prove that it stays small enough to be practical.

**Revisit when:**  
Reopen only if benchmarking shows that a more complex inference path remains acceptable on target devices.

### Use project-owned models instead of Gemini-based diagnosis

**Decision:**  
Keep diagnosis centered on project-owned crop models rather than Gemini-based diagnosis.

**Status:**  
Accepted

**Why we chose it:**  
The project direction is to rely on models that the team trains, evaluates, and controls directly.

**Why we did not choose alternatives:**  
Gemini-based diagnosis was not chosen because it moves the core diagnosis path away from the project’s own validated model stack and weakens direct control over behavior and evaluation boundaries.

**Constraints considered:**  
System ownership, controllability, scope alignment, and model validation discipline.

**Impact on app / training / deployment:**  
Architecture decisions should support internally selected and validated crop models rather than external diagnosis logic.

**Revisit when:**  
Reopen only if the product scope intentionally changes toward external model-service dependence.

## Working Notes From Side Discussion

### Why ensemble alone will not fix the project

Switching from simple deep learning notebooks to ensemble methods does not automatically solve the real project risks.
Core issues such as data quality, model validation, consistency across crop pipelines, error analysis, and deployment fit still have to be solved directly.
Ensemble logic can improve model performance in some cases, but it is not a substitute for a rigorous pipeline review.

### Lightweight inference constraint

The app constraint is not secondary; it is a primary system constraint.
If the target users are farmers with limited device power, then the chosen inference path must remain operationally small and predictable.
Any model strategy that looks strong in notebooks but becomes heavy in the app is a poor production fit.

### Boosting / bagging position

Lighter ensemble-style methods such as boosting or bagging may still be worth exploring if the final inference path remains compact.
This was discussed as a possible direction because it may preserve some ensemble benefits without forcing a heavy multi-model live inference setup.
This is not yet a final decision and should be treated as an evaluation path, not a chosen architecture.

### Backend vs on-device reasoning

Backend-hosted models reduce APK weight and make updates easier, but they introduce dependence on connectivity, backend cost, latency, and operational maintenance.
Keeping models inside the app improves offline simplicity and user independence, but it increases packaging pressure and device-side performance demands.
The right split depends on what the final model size, runtime behavior, and user connectivity assumptions look like after benchmarking.

### Current pipeline as the go-to path

The current crop-model pipeline is still the main route to finish the project.
Project effort should first harden, audit, and fix that pipeline from basic mistakes up to larger structural loopholes before adding extra complexity.
If the base system is weak, adding ensemble logic on top only hides problems rather than solving them.

### Multi-image diagnosis idea

One proposed product feature is to accept 2 to 3 images of the same crop or plant from different angles and combine predictions through majority voting, confidence weighting, or severity-aware scoring.
This can improve robustness against single-image noise, but it also adds UX, latency, and scoring-design questions.
This is not yet a confirmed architecture decision.

### Farmer follow-up and disease tracking idea

Another proposed feature is post-diagnosis follow-up inside the app.
The idea is to let the farmer track the disease state over time and receive reminders for treatment steps such as fertilizer application or disease-management actions on specific days.
This is a product-layer feature idea and should be evaluated separately from the core diagnosis model quality.

## Open Questions

### Exact deployment split

Should the final system be primarily on-device, backend-managed, or hybrid?
This remains open because the final tradeoff depends on model size, latency tolerance, connectivity expectations, and maintenance cost.

### Lightweight boosting or bagging after benchmarking

Should lightweight boosting, bagging, or a similar compact ensemble-style method be allowed if device benchmarks stay within acceptable limits?
This remains open because the idea was discussed positively, but no acceptance threshold was defined.

### Final per-crop model count and packaging approach

How many models should be retained per crop in the final system, and how should they be packaged or served?
This remains open because it depends on evaluation results and the final deployment split.

### Validation and audit checklist for production readiness

What exact checklist must be passed before the system can be described as production-ready or validated?
This remains open because the project still needs a more rigorous definition of validation, testing, model comparison, and deployment-readiness criteria.

### Multi-image decision rule

If multi-image diagnosis is added, what should be the final aggregation rule: majority vote, confidence-weighted vote, severity-aware scoring, or a hybrid?
This remains open because the product idea exists, but the inference and UX rule has not been fixed.

### Disease tracking feature scope

Should disease tracking be limited to reminders and follow-up logging, or should it include treatment plans, progress history, and severity trend estimation?
This remains open because the product feature is promising, but the scope boundary is not yet defined.
