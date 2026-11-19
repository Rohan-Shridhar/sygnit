# Sygnit — Product Roadmap

**Project:** Sygnit   
**Status:** Planned  
**License:** Open Source

## 1. Overview

Sygnit is an open-source cryptographic toolkit designed to help developers sign individual source-code files and repository snapshots, verify their authenticity against trusted public keys, and detect unauthorized modifications.

This roadmap outlines the planned development phases, release milestones, and long-term direction of Sygnit. It provides contributors with a shared understanding of development priorities while keeping the project scope manageable.

## 2. Product Vision

To provide developers with a simple, reliable, and accessible way to establish and verify the integrity of source code and software repositories through cryptographic signing.

## 3. Release Roadmap

### Version 0.1.0 — Foundation

**Objective:** Establish the core cryptographic signing and verification functionality.

Planned features:

- Ed25519 public/private key-pair generation.
- SHA-256 file hashing.
- Individual file signing.
- Repository snapshot traversal.
- Repository manifest generation.
- Deterministic manifest serialization.
- Manifest digital signing.
- Signature verification using public keys.
- Basic repository integrity verification.
- Detection of modified and missing files.
- Basic CLI commands and verification results.

**Milestone outcome:** Users can generate keys, sign files and repository snapshots, and verify their integrity.

### Version 0.2.0 — Developer Experience

**Objective:** Improve usability, reporting, and configuration.

Planned features:

- Improved CLI help and documentation.
- Clearer error messages.
- Detailed verification reports.
- Detection and reporting of unexpected files.
- File and directory exclusion rules.
- Configuration file support.
- JSON verification output.
- Strict and non-strict verification modes.
- Batch file-signing support.
- Expanded unit and integration test coverage.

**Milestone outcome:** Sygnit becomes easier to use, configure, troubleshoot, and integrate into development workflows.

### Version 0.3.0 — Ecosystem Integration

**Objective:** Extend Sygnit's usefulness across existing development workflows.

Planned features:

- Git workflow integration.
- GitHub Actions examples or integration.
- Trusted public-key management improvements.
- Key rotation support.
- Non-interactive automation improvements.
- CI/CD verification workflows.
- Additional integration documentation.

**Milestone outcome:** Developers can incorporate Sygnit into repository management and automated verification workflows.

### Version 1.0.0 — Stable Release

**Objective:** Establish Sygnit as a stable, documented, and security-reviewed open-source product.

Planned features:

- Security review of the implementation and cryptographic design.
- Stable CLI interface and documented command contracts.
- Stable manifest and signature format.
- Public API documentation, if included.
- Cross-platform compatibility testing.
- Dependency and vulnerability review.
- Complete user and contributor documentation.
- Defined compatibility and versioning policies.
- Release and maintenance procedures.
- Security vulnerability reporting process.

**Milestone outcome:** Sygnit reaches its first stable release with reliable functionality, documented limitations, and a clearly defined maintenance process.

## 4. Development Dependencies

The development sequence will follow these dependencies:

1. Key generation must be established before signing operations.
2. File hashing must be implemented before file and repository integrity verification.
3. Manifest structure and canonicalization must be finalized before manifest signing.
4. Manifest signing must be completed before authenticated repository verification.
5. Core verification must be reliable before detailed reporting and automation integrations.
6. Public-key trust management must be defined before making stronger authenticity claims.
7. Security review and compatibility testing must be completed before the stable release.

## 5. Prioritization Strategy

Sygnit will prioritize development using three priority levels:

| Priority | Meaning | Development approach |
|---|---|---|
| P0 | Essential functionality | Required for the MVP and core security guarantees. |
| P1 | Important enhancements | Planned after the foundational functionality is reliable. |
| P2 | Future improvements | Considered based on user feedback, feasibility, and project needs. |

Security, correctness, and integrity verification will take precedence over convenience features and ecosystem integrations.

## 6. Roadmap Maintenance

This roadmap is a planning document rather than a guarantee that every feature will be delivered in a particular release.

- Priorities may change based on implementation findings and community feedback.
- Security issues may require changes to the development sequence.
- New features must be evaluated against the project's core objectives.
- Features not included in an agreed release scope will remain planned rather than committed.
- Each release must satisfy its documented acceptance criteria before publication.

Roadmap changes should be recorded through GitHub issues and pull requests to maintain transparency.

## 7. Long-Term Direction

Beyond version 1.0.0, Sygnit may explore:

- Additional cryptographic algorithm support.
- More advanced key-management capabilities.
- Repository signing integrations with popular developer platforms.
- Expanded software supply-chain verification workflows.
- Additional automation and developer tooling.

These possibilities are exploratory and are not commitments for the initial releases.

---

**Maintained by:** Sygnit Contributors  
**Document Version:** 1.0  
**Last Updated:** 19th November 2026
