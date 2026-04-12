# 🛡️Section 1: Software Supply Chain Integrity (SLSA Level 2)

**Immutable Identification:** All deployments are tracked via **SHA-256 Digests** rather than mutable Docker Tags. This eliminates "Tag-Switching" attacks.

**Cryptographic Attestation:** Every container is digitally signed using **Cosign and OIDC-based** (Keyless) authentication. This ensures only code built in our "Golden Pipeline" can run in production.

**Transparency (SBOM):** Every build generates a **CycloneDX SBOM (Software Bill of Materials)** via **Syft**. This allows for instantaneous vulnerability impact analysis across all 5 microservices.

---

# 🛡️Section 2: Automated Governance (Policy-as-Code)

**Security Gates:** The pipeline is configured with **Grype** to automatically block any build containing **CRITICAL** vulnerabilities.

**Architecture Parity:** Multi-arch builds **(AMD64/ARM64)** ensure the platform is hardware-agnostic and ready for high-efficiency cloud instances (e.g., AWS Graviton).

**Centralized Orchestration:** Using **GitHub Reusable Workflows**, we enforce a single security standard across polyglot services (Node.js, Go, etc.), reducing configuration drift. 