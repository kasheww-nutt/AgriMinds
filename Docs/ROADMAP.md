# AgriMinds Roadmap

## Phase 1 — Repository and evidence hygiene

Prepare a private, reviewable repository; separate maintained documentation from historical notes; exclude datasets, secrets, generated artifacts, and model binaries from normal Git history; record decisions and known limitations honestly.

## Phase 2 — Correct the six crop pipelines

Preserve legacy notebooks, build shared training logic, retrain all existing architectures under persisted stratified splits, select checkpoints by validation macro-F1, calibrate confidence, and publish single-pass test reports and model cards.

Potato is the first corrected runner. Grape, Maize, Rice, Tomato, and Wheat follow after the Potato pipeline passes a clean-kernel execution review.

## Phase 3 — Dataset audit

After explicit approval, inspect corrupt files, exact and near duplicates, source leakage, class imbalance, label consistency, acquisition groups, background bias, and external-field coverage. Rebuild manifests if the audit changes dataset membership.

## Phase 4 — Android model delivery

Export six champions, verify Python/Android preprocessing parity using golden images, benchmark package size, RAM, cold start, latency, battery, and thermal behaviour on representative low-cost devices, then choose validated precision/compression formats.

## Phase 5 — Evidence-aware diagnosis

Add guided multi-image capture, blur/exposure/coverage checks, near-duplicate rejection, calibrated confidence-weighted consensus, contradiction handling, and targeted reacquisition instructions. Keep the interaction simple even when internal evidence logic is complex.

## Phase 6 — Care and progression loop

Ground treatment protocols in expert-reviewed agricultural sources, create local care tasks and reminders, collect comparable follow-up evidence, report improving/stable/worsening states, and escalate unreliable or worsening cases.

## Phase 7 — Validation and release

Run external field evaluation, expert review, safety testing, accessibility/localization checks, privacy review, and staged pilot deployment. “Production validated” remains prohibited until these gates are passed.

