# Sygnit — Release Acceptance Criteria

## 1. General Acceptance Criteria

A release can be published only when:

- [ ] All planned release features are implemented.
- [ ] Relevant automated tests pass.
- [ ] No unresolved critical or high-severity bugs remain.
- [ ] Security requirements are satisfied.
- [ ] Documentation is updated.
- [ ] Installation and basic usage are verified.
- [ ] Version number and release notes are prepared.

## 2. Release-Specific Criteria

### v0.1.0 — Initial MVP
- [ ] Ed25519 key generation works correctly.
- [ ] Individual file signing and verification work.
- [ ] Repository manifests can be generated and signed.
- [ ] Repository integrity verification detects modified and missing files.
- [ ] Invalid signatures are rejected.
- [ ] Original files remain unchanged.

### v0.2.0 — Developer Experience
- [ ] Improved CLI functionality works as expected.
- [ ] JSON reports produce valid output.
- [ ] File exclusion and batch signing work correctly.
- [ ] Expanded tests pass.

### v0.3.0 — Integrations
- [ ] Git integration works correctly.
- [ ] GitHub Actions workflows execute successfully.
- [ ] Key rotation and trust management behave as documented.
- [ ] Automation documentation is available.

### v1.0.0 — Stable Release
- [ ] Security review is completed.
- [ ] Manifest and signature formats are finalized.
- [ ] Supported platforms are tested.
- [ ] Documentation is complete.
- [ ] Dependencies are reviewed for known vulnerabilities.
- [ ] Compatibility and versioning policies are documented.

## 3. Release Approval

A release is approved when all applicable acceptance criteria are satisfied, required tests pass, and release documentation is ready.

Any deferred criteria must be explicitly documented before publication.

---

**Project:** Sygnit  
**Document Version:** 1.0  
**Status:** Planned  
**Last Updated:** 19th November 2026
