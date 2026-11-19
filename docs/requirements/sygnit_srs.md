# Sygnit --- Software Requirements Specification (SRS)

- **Document status:** Final
- **Version:** 1.0
- **Product:** Sygnit
- **Product type:** Open-source cryptographic toolkit and command-line interface
- **Intended audience:** Developers, maintainers, contributors, testers, and reviewers

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Specification (SRS) defines the functional,
non-functional, security, interface, testing, and operational
requirements for Sygnit.

Sygnit is an open-source developer tool intended to let users create
cryptographic signatures for individual source-code files and repository
snapshots, then verify those signatures and detect changes to the signed
content.

This document establishes the initial requirements for architecture,
implementation, testing, release planning, and future requirement
changes. Requirements in this draft are proposed and should be validated
during product discovery and technical design.

### 1.2 Scope and Expected Outcome

**Product scope**

Sygnit will provide a command-line interface (CLI) and reusable core
functionality for:

- Generating and managing local signing key pairs.
- Hashing individual files using an approved cryptographic hash
  algorithm.
- Creating a manifest representing a defined repository snapshot.
- Digitally signing file metadata or repository manifests.
- Verifying signatures using a supplied or configured public key.
- Recalculating file hashes and identifying content changes.
- Reporting modified, missing, and unexpected files according to the
  selected verification policy.
- Supporting automation through predictable exit codes and
  machine-readable output.

**Initial product boundary**

The initial release focuses on local signing and verification of files
and repository snapshots. It is not intended to:

- Prove the real-world identity of a signer without a separate trusted
  identity mechanism.
- Prove that signed code is safe, benign, or free from
  vulnerabilities.
- Replace Git commit or tag signing, software provenance systems, or
  complete software supply-chain security platforms.
- Guarantee that a published binary was built from a particular source
  tree.
- Provide a hosted key-management or identity service.
- Execute, compile, or modify the source files being signed.

**Expected outcome**

A user should be able to create or obtain a signing key, sign a file or
repository snapshot, distribute the signed content and verification
metadata, and independently verify the signature and file integrity
using Sygnit.

A successful verification means that the signature is valid for the
supplied manifest and that the checked files match the manifest under
the documented verification rules. It is not proof of the signer's
real-world identity unless the public key has been trusted through an
independent process.

> [!note]  
> A valid signature confirms integrity relative to the supplied
> manifest; it does *not* prove the signer's real-world identity without
> an independently trusted public key.

### 1.3 Acceptance Criteria

The initial release will be considered acceptable when:

1. A user can generate an Ed25519 signing key pair using the supported
   key-generation workflow.
2. Private key material is not printed in normal command output or
   included in generated repository manifests.
3. A user can sign an individual supported file.
4. A user can create a deterministic manifest for a repository snapshot
   under documented file-selection and path-normalization rules.
5. A user can sign the repository manifest.
6. A user can verify a signature using the corresponding public key.
7. Verification detects modified files.
8. Verification detects missing files included in the manifest.
9. Verification reports unexpected files according to the selected
   policy.
10. Invalid signatures, malformed manifests, missing keys, and
    unreadable files result in clear failure reports and non-zero exit
    codes.
11. The CLI provides help information and documents supported commands
    and options.
12. Automated unit and integration tests cover core signing and
    verification workflows.
13. The package can be installed and executed from npm in a clean
    supported environment.
14. The README and technical documentation accurately describe the trust
    model, limitations, supported environments, and commands.

---

## 2. Functional Requirements (SMART List)

Requirements use unique identifiers so they can be linked to design
decisions, implementation tasks, and test cases. Priorities are **P0**
(MVP), **P1** (important enhancement), and **P2** (future capability).

### 2.1 Key Generation and Management

| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-KEY-01 | Sygnit shall generate an Ed25519 public/private key pair through a CLI command. | P0 | Generated key pair successfully passes a signing and verification test. |
| FR-KEY-02 | Sygnit shall save private and public keys to user-selected or documented default locations. | P0 | Expected key files are created and their locations are reported. |
| FR-KEY-03 | Sygnit shall allow users to export or display their public key without exposing the private key. | P0 | Public key is displayed or exported successfully without private-key leakage. |
| FR-KEY-04 | Sygnit shall reject unreadable, malformed, or unsupported key files with a clear error. | P0 | Invalid key files produce an explanatory error and non-zero exit code. |
| FR-KEY-05 | Sygnit should support key rotation and document the process for replacing a key. | P1 | Users can introduce a new public key and stop trusting an old key. |
| FR-KEY-06 | Sygnit may support key-revocation metadata or a local trust store. | P2 | Revoked keys are represented and checked according to a documented specification. |

### 2.2 Individual File Signing
| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-FILE-01 | Sygnit shall calculate a cryptographic hash for a selected file using an approved hash algorithm. | P0 | Generated hash matches known test vectors. |
| FR-FILE-02 | Sygnit shall sign the file's defined signing payload using the user's private key. | P0 | Valid signature is produced and verified using the matching public key. |
| FR-FILE-03 | Sygnit shall store signature metadata separately from the original file. | P0 | Signing does not modify the original file. |
| FR-FILE-04 | Sygnit shall verify a signed file using a supplied public key. | P0 | Unmodified content with valid metadata passes verification. |
| FR-FILE-05 | Sygnit shall report verification failure when file content changes after signing. | P0 | A one-byte modification causes verification to fail. |
| FR-FILE-06 | Sygnit should support signing multiple selected files in one operation. | P1 | Batch operation reports an individual result for each selected file. |

### 2.3 Repository Snapshot and Manifest
| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-REPO-01 | Sygnit shall traverse a selected directory and identify files included in the repository snapshot according to documented rules. | P0 | Test repositories produce the expected file list. |
| FR-REPO-02 | Sygnit shall support exclusion rules for files and directories. | P0 | Excluded paths are absent from the generated manifest. |
| FR-REPO-03 | Sygnit shall calculate a cryptographic hash for every included regular file. | P0 | Every included file has a corresponding hash in the manifest. |
| FR-REPO-04 | Sygnit shall generate a versioned repository manifest containing relative paths, hashes, algorithm identifiers, and required metadata. | P0 | Generated manifest validates against the published schema. |
| FR-REPO-05 | Sygnit shall serialize manifests deterministically using documented canonicalization rules. | P0 | Repeated generation for an unchanged snapshot produces identical canonical signing bytes. |
| FR-REPO-06 | Sygnit shall sign the canonical repository manifest using the selected private key. | P0 | Manifest signature successfully verifies using the corresponding public key. |
| FR-REPO-07 | Sygnit shall store the manifest and signature in a documented location without including private-key material. | P0 | Generated output contains the manifest and signature but no private key. |
| FR-REPO-08 | Sygnit should provide options for handling hidden files, with documented defaults. | P1 | Selected file-inclusion policy is consistently reflected in the manifest. |
| FR-REPO-09 | Sygnit should define and report how symbolic links are handled. | P1 | Symbolic links follow an explicit, tested policy. |

### 2.4 Verification and Integrity Reporting
| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-VERIFY-01 | Sygnit shall verify the manifest signature before treating its contents as an authenticated reference. | P0 | Invalid manifest signatures prevent successful verification. |
| FR-VERIFY-02 | Sygnit shall recalculate hashes for files listed in the manifest. | P0 | Recalculated hashes are compared with authenticated manifest values. |
| FR-VERIFY-03 | Sygnit shall identify modified files during repository verification. | P0 | Modified files are reported with their relative paths. |
| FR-VERIFY-04 | Sygnit shall identify files listed in the manifest that are missing from the verification directory. | P0 | Missing files are reported with their relative paths. |
| FR-VERIFY-05 | Sygnit shall identify unexpected files according to the selected verification policy. | P0 | Unlisted files are reported when strict detection is enabled. |
| FR-VERIFY-06 | Sygnit shall distinguish signature validity from public-key trust. | P0 | Output does not label a key as trusted solely because its signature is mathematically valid. |
| FR-VERIFY-07 | Sygnit shall provide an overall verification result and per-file details. | P0 | Report includes a summary and relevant file statuses. |
| FR-VERIFY-08 | Sygnit should support JSON output for automated processing. | P1 | Output conforms to a documented JSON schema. |
| FR-VERIFY-09 | Sygnit should provide strict and non-strict verification modes with documented behavior. | P1 | Tests demonstrate different handling of unexpected files and policy violations. |

### 2.5 CLI and User Experience
| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-CLI-01 | Sygnit shall provide a CLI entry point for supported operations. | P0 | Commands execute successfully through the installed npm package. |
| FR-CLI-02 | Sygnit shall provide help output describing commands, options, and examples. | P0 | Help is accessible through the documented command or option. |
| FR-CLI-03 | Sygnit shall return zero for successful operations and non-zero for failed operations. | P0 | Automated tests verify exit codes for success and failure cases. |
| FR-CLI-04 | Sygnit shall provide actionable error messages without exposing secrets. | P0 | Error tests confirm useful messages and no private-key leakage. |
| FR-CLI-05 | Sygnit should provide a status or inspection command for signature and manifest metadata. | P1 | Command displays documented metadata without exposing secrets. |

### 2.6 Automation and Integration
| ID | Requirement | Priority | Acceptance measure |
| --- | --- | --- | --- |
| FR-AUTO-01 | Sygnit shall support non-interactive execution for core signing and verification operations when required inputs are supplied. | P0 | Core commands execute in a non-interactive test environment. |
| FR-AUTO-02 | Sygnit shall provide stable exit-code behavior suitable for scripts. | P0 | Exit codes are documented and covered by automated tests. |
| FR-AUTO-03 | Sygnit should provide examples for verification in CI workflows. | P1 | A documented example successfully runs verification in a CI environment. |
| FR-AUTO-04 | Sygnit may provide maintained GitHub Actions integration. | P2 | A future release defines and tests the integration contract. |

---

## 3. Non-Functional Requirements and Safety

### 3.1 Performance NFRs

The following are initial engineering targets. They must be measured on
documented reference hardware and adjusted using test results.

| ID | Requirement | Initial target |
| --- | --- | --- |
| NFR-PERF-01 | Sygnit shall generate keys without unnecessary network access. | Local operation; normally complete within 2 seconds on reference hardware. |
| NFR-PERF-02 | Sygnit shall process files in a streaming manner where practical. | Memory use remains bounded when hashing large files. |
| NFR-PERF-03 | Sygnit shall provide progress information for operations that take noticeable time. | Progress output does not corrupt machine-readable output. |
| NFR-PERF-04 | Sygnit shall support repositories containing at least 10,000 regular files in performance tests. | Test report records elapsed time and peak memory. |
| NFR-PERF-05 | Sygnit shall avoid unnecessary repeated hashing within a single operation. | Performance tests identify and prevent avoidable duplicate reads. |
| NFR-PERF-06 | Sygnit shall not silently omit files due to resource limits. | Resource-limit failures are explicit and non-successful. |

### 3.2 Safety Invariants and Constraints --- High

1. **No false success:** Sygnit must never report successful integrity
   verification if signature verification fails or a required file
   check fails.
2. **Verify before trusting:** Manifest contents must not be treated as
   authenticated until the manifest signature has been successfully
   verified.
3. **Private-key confidentiality:** Private keys must not be included
   in manifests, signatures, logs, diagnostic reports, or normal
   terminal output.
4. **No silent file modification:** Signing and verification must not
   modify source files.
5. **Explicit file-selection policy:** Sygnit must document which files
   are included, excluded, followed, or ignored.
6. **Safe path handling:** Manifest paths must be validated to prevent
   path traversal outside the selected verification root.
7. **Safe symbolic-link handling:** Symbolic links must follow a
   documented policy and must not permit unintended access outside the
   selected root.
8. **No execution of repository content:** Verification must not
   execute scripts, build commands, hooks, or repository-provided code.
9. **Fail closed on ambiguous security state:** Malformed manifests,
   unsupported algorithms, invalid signatures, and ambiguous trust
   information must not produce successful verification.
10. **Clear trust communication:** A valid signature must not be
    described as proof of real-world identity unless a separate trust
    mechanism establishes that identity.

> [!IMPORTANT]  
> Never include private-key material in generated metadata, logs,
> diagnostic reports, or normal terminal output.

### 3.3 Security NFRs --- High

| ID | Requirement | Priority |
| --- | --- | --- |
| NFR-SEC-01 | Sygnit shall use established cryptographic implementations and shall not implement custom signature algorithms. | High |
| NFR-SEC-02 | The initial signing algorithm shall be Ed25519, subject to compatibility and security review. | High |
| NFR-SEC-03 | The initial file-hashing algorithm shall be SHA-256 or another explicitly approved secure hash algorithm. | High |
| NFR-SEC-04 | The signed payload shall use a deterministic, documented representation. | High |
| NFR-SEC-05 | Signature metadata shall identify format version and algorithm identifiers needed for safe verification. | High |
| NFR-SEC-06 | Sygnit shall validate manifest structure, path values, file counts, and input sizes before processing. | High |
| NFR-SEC-07 | Sygnit shall use restrictive file permissions for newly generated private keys where the operating system supports them. | High |
| NFR-SEC-08 | Sygnit shall warn users when private-key storage permissions are broader than recommended, where detectable. | High |
| NFR-SEC-09 | Sygnit shall not transmit file contents, private keys, or signing metadata to a remote service during local signing and verification. | High |
| NFR-SEC-10 | Sygnit shall document the trust model and explain that users must obtain public keys through a trusted channel. | High |
| NFR-SEC-11 | Sygnit shall provide a process for reporting security vulnerabilities. | High |
| NFR-SEC-12 | Sygnit shall be tested against malformed manifests, invalid signatures, path traversal attempts, and adversarial file-system cases. | High |

---

## 4. External Interfaces, Data Schemas and Protocols

### 4.1 User Interface

The primary user interface shall be a command-line interface.

Proposed command structure:

| Command | Purpose |
| --- | --- |
| `sygnit init` | Initialize configuration or generate initial keys, according to finalized command design. |
| `sygnit keygen` | Generate a signing key pair. |
| `sygnit sign <file>` | Sign an individual file. |
| `sygnit sign <directory>` | Generate and sign a repository snapshot manifest. |
| `sygnit verify <file>` | Verify an individual file signature. |
| `sygnit verify <directory>` | Verify a repository snapshot. |
| `sygnit status <directory>` | Inspect available Sygnit metadata and report status. |
| `sygnit --help` | Display command help. |
| `sygnit --version` | Display the installed version. |

The final command names and options shall be confirmed during CLI
design. These examples are proposed interfaces, not a finalized public
API.

### 4.2 Software Interfaces

- **Runtime:** Node.js, with supported versions documented by the
  project.
- **Cryptography:** Node.js built-in cryptographic APIs or another
  reviewed cryptographic library.
- **Package distribution:** npm.
- **Source hosting and collaboration:** Git and GitHub.
- **Automation:** CI systems such as GitHub Actions, initially through
  documented CLI usage.
- **Operating systems:** Windows, Linux, and macOS, subject to
  compatibility testing.

### 4.3 File and Directory Interfaces

Sygnit shall accept user-selected file and directory paths. It shall:

- Resolve paths relative to a clearly defined working directory or
  explicit root.
- Store repository paths as normalized relative paths.
- Reject paths that escape the selected root.
- Define how hidden and ignored files are treated.
- Define how symbolic links and special files are handled.
- Avoid including Sygnit's own generated metadata in the snapshot
  unless explicitly required by the format.

### 4.4 Proposed Manifest Schema

The following is illustrative, not a finalized format. The final schema
must define canonicalization, field validation, path rules, and version
compatibility.

```json
{
  "format": "sygnit-manifest",
  "formatVersion": 1,
  "hashAlgorithm": "sha256",
  "signatureAlgorithm": "ed25519",
  "createdAt": "2026-01-01T00:00:00.000Z",
  "files": [
    {
      "path": "src/index.js",
      "hash": "<hex-encoded-sha256-digest>",
      "size": 1024
    },
    {
      "path": "package.json",
      "hash": "<hex-encoded-sha256-digest>",
      "size": 512
    }
  ]
}
```

The signature shall cover a precisely defined canonical representation
of the manifest. Fields not covered by the signature must not be
presented as authenticated claims.

### 4.5 Signature Metadata

Signature metadata should identify:

- Manifest format and version.
- Signature algorithm.
- Public-key identifier or fingerprint.
- Signature encoding.
- Any required domain-separation or context identifier.

The signature file must not contain private key material.

### 4.6 Protocols and Cryptographic Operations

- File content shall be hashed using the configured approved hash
  algorithm.
- The repository manifest shall be serialized using a deterministic
  canonicalization procedure.
- The canonical manifest bytes shall be signed using the selected
  private key.
- Verification shall validate the signature using the corresponding
  public key.
- File integrity verification shall compare recalculated hashes with
  values in the authenticated manifest.
- Unsupported algorithm identifiers and format versions shall be
  rejected or handled through explicitly documented compatibility
  behavior.

---

## 5. Testing, Simulation and Evaluation Plan

### 5.1 Testing Objectives

Testing shall establish that Sygnit:

- Produces valid signatures for supported inputs.
- Correctly verifies valid signatures.
- Detects content modifications.
- Detects missing and unexpected files according to policy.
- Rejects invalid signatures and malformed metadata.
- Protects private-key material.
- Handles file-system edge cases safely.
- Behaves consistently on supported operating systems.

### 5.2 Unit Testing

Unit tests shall cover:

- Key generation and key parsing.
- File hashing.
- Manifest creation.
- Manifest canonicalization.
- Signature creation and verification.
- Path normalization.
- Exclusion rules.
- Error and exit-code mapping.

### 5.3 Integration Testing

Integration tests shall cover these complete workflows:

1. Generate a key pair.
2. Sign a file.
3. Verify the unchanged file.
4. Modify the file and confirm verification fails.
5. Generate a repository manifest.
6. Sign the manifest.
7. Verify an unchanged repository.
8. Modify, delete, and add files.
9. Confirm the report identifies each relevant difference.

### 5.4 Negative and Security Testing

Test cases shall include:

- Invalid public and private keys.
- Corrupted signature files.
- Modified manifests.
- Unsupported manifest versions and algorithms.
- Duplicate manifest paths.
- Absolute paths and path traversal sequences.
- Symbolic links pointing outside the repository.
- Unreadable files and permission errors.
- Very large files and manifests.
- Files changing during hashing.
- Private-key data accidentally appearing in logs or reports.

### 5.5 Compatibility Testing

The test matrix shall include:

- Supported Node.js versions.
- Windows, Linux, and macOS.
- npm installation from a clean environment.
- Paths containing spaces and Unicode characters.
- Different file-system permission behaviors.

### 5.6 Performance Evaluation

Performance testing shall measure:

- Key-generation time.
- File-hashing throughput.
- Repository traversal time.
- Manifest-generation time.
- Signing and verification time.
- Peak memory usage.
- Performance across different file counts and file sizes.

The test environment, hardware, operating system, Node.js version, and
repository characteristics shall be recorded so results can be
reproduced.

### 5.7 Evaluation Metrics

| Metric | Evaluation method |
| --- | --- |
| Signature correctness | Valid signatures pass; invalid signatures fail. |
| Tamper detection | Modified file content is detected. |
| Missing-file detection | Files in the manifest but absent from the snapshot are reported. |
| Unexpected-file detection | Unlisted files are reported according to policy. |
| Key confidentiality | No private-key material appears in generated metadata or logs. |
| Manifest determinism | Equivalent snapshots produce identical canonical manifest bytes. |
| Compatibility | Core test suite passes on supported environments. |
| Performance | Time and memory use are measured against documented test repositories. |
| Usability | New users can complete the documented quick-start workflow. |

### 5.8 Release Testing

Before each release:

- Run all automated tests.
- Review security-sensitive changes.
- Validate package contents.
- Test installation from npm.
- Verify documented examples.
- Review known defects and limitations.
- Record the release acceptance result.

---

## 6. Operations Runbook, Rollout and Traceability

### 6.1 Installation and Initial Setup

The user shall be able to install Sygnit from npm using the documented
installation method.

Setup documentation shall explain:

- Supported Node.js and npm versions.
- Key-generation procedure.
- Recommended private-key storage and backup practices.
- Public-key distribution and trust establishment.
- Basic file-signing and repository-signing workflows.
- Verification procedures.

> [!TIP]  
> Obtain and trust the public key independently before verifying a signature.

### 6.2 Normal Operation

A typical user workflow shall be:

1. Install Sygnit.
2. Generate or configure a signing key pair.
3. Select a file or repository snapshot.
4. Generate a signature.
5. Distribute the signed content, manifest, signature, and public key
   through appropriate channels.
6. Obtain and trust the public key independently.
7. Verify the signature.
8. Review the integrity report.

### 6.3 Failure Handling and Recovery

The operations documentation shall explain how to respond to:

- Invalid signatures.
- Modified or missing files.
- Unexpected files.
- Missing or unreadable keys.
- Corrupted manifests.
- Unsupported formats.
- Permission errors.
- Incomplete signing or verification operations.

Sygnit must not silently treat a failed or incomplete operation as
successful.

### 6.4 Key Backup, Rotation and Compromise

The documentation shall recommend secure private-key backups and explain
the consequences of key loss.

A future key-rotation procedure shall explain how users introduce a new
public key and establish trust in it. A compromised key must no longer
be trusted for new verification decisions once its compromise is known.
Timestamps alone do not prove when a signature was created.

### 6.5 Rollout Plan

| Release stage | Scope | Exit condition |
| --- | --- | --- |
| Internal prototype | Validate key generation, hashing, manifest creation, signing, and verification. | Core workflows pass local tests. |
| MVP / 0.1.x | Release essential file and repository signing and verification. | MVP acceptance criteria are met. |
| Feature releases / 0.2.x--0.3.x | Improve reports, configuration, automation, and integrations. | Each milestone's acceptance criteria are met. |
| Stable release / 1.0.0 | Complete security review, documentation, compatibility policy, and release process. | Stable-release review is approved. |

### 6.6 Maintenance and Support

The project shall:

- Track bugs and feature requests through GitHub Issues.
- Publish release notes and maintain a changelog.
- Review dependencies and security advisories.
- Provide a security vulnerability reporting channel.
- Maintain supported Node.js and operating-system information.
- Document breaking changes and migration steps.
- Review community contributions according to the contribution policy.

### 6.7 Requirements Traceability

Each requirement shall be linked to at least one design element,
implementation task, and test case before the corresponding release.

Example traceability structure:

| Requirement ID | Design reference | Implementation task | Test reference |
| --- | --- | --- | --- |
| FR-KEY-01 | Key management design | Key-pair generation | TEST-KEY-001 |
| FR-FILE-02 | File signing design | File signature creation | TEST-FILE-002 |
| FR-REPO-04 | Manifest specification | Manifest generation | TEST-REPO-004 |
| FR-REPO-06 | Signature format design | Manifest signing | TEST-REPO-006 |
| FR-VERIFY-03 | Verification report design | Modified-file detection | TEST-VERIFY-003 |
| FR-VERIFY-04 | Verification report design | Missing-file detection | TEST-VERIFY-004 |
| NFR-SEC-01 | Cryptographic design review | Approved crypto implementation | TEST-SEC-001 |
| NFR-SEC-06 | Input validation design | Manifest and path validation | TEST-SEC-006 |

The traceability table shall be expanded as requirements, design
documents, implementation issues, and test cases are created. Changes to
a requirement shall trigger review of its linked design, implementation,
tests, and documentation.

---

## Appendix A --- Glossary

| Term | Definition |
| --- | --- |
| Digital signature | A cryptographic value created with a private key and verified with the corresponding public key. |
| Private key | Secret key material used to create digital signatures. |
| Public key | Key material used to verify signatures and identify the corresponding key pair. |
| Hash | A fixed-length digest calculated from input data using a cryptographic hash function. |
| Manifest | A structured document describing the files and metadata included in a repository snapshot. |
| Repository snapshot | A defined collection of files and paths at a particular point in time, independent of whether it is stored in Git. |
| Integrity | The property that data has not changed relative to a trusted reference. |
| Authenticity | Confidence that data or a signature is associated with the expected key or identity under a defined trust model. |
| Provenance | Information about the origin and history of software or an artifact. |
| CI/CD | Continuous Integration and Continuous Delivery or Deployment. |
| Canonicalization | Conversion of structured data into a deterministic representation before signing or hashing. |
| Ed25519 | A public-key digital signature algorithm based on Edwards-curve Digital Signature Algorithm. |
| SHA-256 | A cryptographic hash function that produces a 256-bit digest. |

## Appendix B --- Open Decisions

The following decisions must be finalized during architecture and
security design:

1. Exact manifest and signature file names and directory layout.
2. Canonical serialization format and specification.
3. Whether file signatures use detached signature files or a shared
   manifest format.
4. Default file inclusion and exclusion behavior.
5. Symbolic-link and special-file handling.
6. Whether unexpected files cause failure by default or are reported
   according to a configurable policy.
7. Public-key fingerprint format and trust configuration.
8. Private-key encryption and storage options.
9. Key rotation and revocation model.
10. Supported Node.js versions and operating systems.
11. Stable CLI command names and exit-code definitions.
12. Whether a public programmatic API is included in the initial
    release.

These decisions should be documented before the related features are
declared stable.

---

**End of document**
