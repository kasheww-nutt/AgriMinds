# Security

AgriMinds is currently a private research repository.

Do not commit API keys, certificates, service-account files, private datasets, precise farm locations, or identifiable farmer images. Development secrets should use local environment variables. Deployed secrets should use protected server-side storage.

Final model files should be checked against their expected architecture, class map, preprocessing settings, split version, and SHA-256 hash. Do not load model files from an unknown source.

Report security problems privately to the repository owner while the project remains private.
