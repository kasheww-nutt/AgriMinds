# Model Artifacts

Model binaries are intentionally excluded from normal Git history.

Every approved champion should eventually be distributed with a model card containing:

- crop, supported classes, architecture, and preprocessing version;
- dataset/split manifest identity and training seed;
- validation selection metric and sealed test report;
- calibration and uncertainty policy;
- checkpoint SHA-256 hash and export format;
- latency, RAM, storage, and target-device benchmarks;
- known failure modes and intended/non-intended uses.

Legacy `.pt` files may remain in local working folders, but they are ignored by Git and are not production artifacts.

