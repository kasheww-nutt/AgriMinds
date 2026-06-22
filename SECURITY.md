# Security Policy

AgriMinds is currently a private research project.

Do not commit API keys, service-account files, private certificates, personal datasets, precise farm locations, or identifiable farmer images. Use local environment variables for development secrets and protected server-side secret storage for deployed services.

Model checkpoints are treated as supply-chain artifacts. Production candidates require an expected architecture, class-map identity, preprocessing specification, split identity, and SHA-256 hash. Load PyTorch weights using the safest supported weights-only mechanism and never load untrusted serialized checkpoints.

Security issues should be reported privately to the repository owner rather than opened as public issues while the repository remains private.

