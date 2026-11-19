# Sygnit — MVP Features

## 1. Objective

Define the minimum features required to deliver the first functional version of Sygnit (v0.1.0).

The initial release focuses on cryptographic key generation, file signing, repository snapshot signing, and integrity verification.

## 2. MVP Scope

| ID | Feature | Description |
|---|---|---|
| MVP-01 | Key Generation | Generate Ed25519 public-private key pairs. |
| MVP-02 | File Hashing | Calculate SHA-256 hashes of files. |
| MVP-03 | File Signing | Sign individual files using a private key. |
| MVP-04 | File Verification | Verify file signatures using a public key. |
| MVP-05 | Repository Scanning | Identify files within a selected directory. |
| MVP-06 | Manifest Generation | Generate a manifest containing file paths and hashes. |
| MVP-07 | Manifest Signing | Cryptographically sign the repository manifest. |
| MVP-08 | Integrity Verification | Verify the manifest and detect modified or missing files. |
| MVP-09 | CLI Interface | Provide basic commands for key generation, signing, and verification. |
| MVP-10 | Error Handling | Handle invalid keys, missing files, and failed verification. |

## 3. Security Requirements

- Use Ed25519 for digital signatures.
- Use SHA-256 for file hashing.
- Never expose or include private keys in generated artifacts.
- Verify the manifest signature before trusting its contents.
- Do not modify original files during signing or verification.
- Clearly report failed verification.

## 4. Out of Scope

The following are deferred to future releases:

- Batch file signing.
- Advanced configuration and exclusion rules.
- JSON reports.
- Key rotation and trust management.
- Git and GitHub Actions integration.
- Graphical user interface.
- Additional cryptographic algorithms.

## 5. Completion Criteria

Sygnit v0.1.0 is considered complete when:

1. Users can generate an Ed25519 key pair.
2. Users can sign and verify individual files.
3. Users can generate and sign a repository manifest.
4. Users can verify repository integrity.
5. Modified and missing files are detected.
6. Core CLI operations work reliably.
7. Basic automated tests and usage documentation are available.

---

**Project:** Sygnit  
**Target Release:** v0.1.0  
**Document Version:** 1.0  
**Status:** Planned  
**Last Updated:** 19th November 2026
