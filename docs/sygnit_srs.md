# Sygnit --- Software Requirements Specification (SRS)

**Document status:** Initial draft\
**Version:** 0.1\
**Product:** Sygnit\
**Product type:** Open-source cryptographic toolkit and command-line
interface\
**Intended audience:** Developers, maintainers, contributors, testers,
and reviewers

------------------------------------------------------------------------

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

-   Generating and managing local signing key pairs.
-   Hashing individual files using an approved cryptographic hash
    algorithm.
-   Creating a manifest representing a defined repository snapshot.
-   Digitally signing file metadata or repository manifests.
-   Verifying signatures using a supplied or configured public key.
-   Recalculating file hashes and identifying content changes.
-   Reporting modified, missing, and unexpected files according to the
    selected verification policy.
-   Supporting automation through predictable exit codes and
    machine-readable output.

**Initial product boundary**

The initial release focuses on local signing and verification of files
and repository snapshots. It is not intended to:

-   Prove the real-world identity of a signer without a separate trusted
    identity mechanism.
-   Prove that signed code is safe, benign, or free from
    vulnerabilities.
-   Replace Git commit or tag signing, software provenance systems, or
    complete software supply-chain security platforms.
-   Guarantee that a published binary was built from a particular source
    tree.
-   Provide a hosted key-management or identity service.
-   Execute, compile, or modify the source files being signed.

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

### 1.3 Acceptance Criteria

The initial release will be considered acceptable when:

1.  A user can generate an Ed25519 signing key pair using the supported
    key-generation workflow.
2.  Private key material is not printed in normal command output or
    included in generated repository manifests.
3.  A user can sign an individual supported file.
4.  A user can create a deterministic manifest for a repository snapshot
    under documented file-selection and path-normalization rules.
5.  A user can sign the repository manifest.
6.  A user can verify a signature using the corresponding public key.
7.  Verification detects modified files.
8.  Verification detects missing files included in the manifest.
9.  Verification reports unexpected files according to the selected
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

------------------------------------------------------------------------

## 2. Functional Requirements (SMART List)

Requirements use unique identifiers so they can be linked to design
decisions, implementation tasks, and test cases. Priorities are **P0**
(MVP), **P1** (important enhancement), and **P2** (future capability).

### 2.1 Key Generation and Management

  -----------------------------------------------------------------------
  ID                Requirement       Priority          Acceptance
                                                        measure
  ----------------- ----------------- ----------------- -----------------
  FR-KEY-01         Sygnit shall      P0                A generated key
                    generate an                         pair passes a
                    Ed25519                             signing and
                    public/private                      verification
                    key pair through                    test.
                    a CLI command.                      

  FR-KEY-02         Sygnit shall save P0                Expected key
                    private and                         files are created
                    public keys to                      and their
                    user-selected or                    locations
                    documented                          reported.
                    default                             
                    locations.                          

  FR-KEY-03         Sygnit shall      P0                Public-key export
                    support exporting                   succeeds;
                    or displaying the                   private-key
                    public key                          contents are
                    without exposing                    absent from
                    the private key.                    output.

  FR-KEY-04         Sygnit shall      P0                Invalid key
                    reject                              fixtures produce
                    unreadable,                         a non-zero exit
                    malformed, or                       code and
                    unsupported key                     explanatory
                    files with a                        message.
                    clear error.                        

  FR-KEY-05         Sygnit should     P1                Users can
                    support key                         introduce a new
                    rotation and                        public key and
                    document the                        stop trusting an
                    process for                         old key.
                    replacing a key.                    

  FR-KEY-06         Sygnit may        P2                A future
                    support                             specification
                    key-revocation                      defines how
                    metadata or a                       revoked keys are
                    local trust                         represented and
                    store.                              checked.
  -----------------------------------------------------------------------

### 2.2 Individual File Signing

  -----------------------------------------------------------------------
  ID                Requirement       Priority          Acceptance
                                                        measure
  ----------------- ----------------- ----------------- -----------------
  FR-FILE-01        Sygnit shall      P0                Output matches
                    calculate a                         known test
                    cryptographic                       vectors.
                    hash for a                          
                    selected file                       
                    using an approved                   
                    hash algorithm.                     

  FR-FILE-02        Sygnit shall sign P0                A valid signature
                    the file's                          is produced and
                    defined signing                     verified with the
                    payload using the                   matching public
                    user's private                      key.
                    key.                                

  FR-FILE-03        Sygnit shall      P0                Signing does not
                    store signature                     modify the
                    metadata                            original file.
                    separately from                     
                    the original                        
                    file.                               

  FR-FILE-04        Sygnit shall      P0                Unmodified
                    verify a signed                     content and valid
                    file using a                        metadata pass
                    supplied public                     verification.
                    key.                                

  FR-FILE-05        Sygnit shall      P0                A one-byte
                    report                              modification
                    verification                        causes
                    failure when file                   verification to
                    content changes                     fail.
                    after signing.                      

  FR-FILE-06        Sygnit should     P1                A batch operation
                    support signing                     reports a result
                    multiple selected                   for each selected
                    files in one                        file.
                    operation.                          
  -----------------------------------------------------------------------

### 2.3 Repository Snapshot and Manifest

  -------------------------------------------------------------------------
  ID                Requirement         Priority          Acceptance
                                                          measure
  ----------------- ------------------- ----------------- -----------------
  FR-REPO-01        Sygnit shall        P0                Test repositories
                    traverse a selected                   produce the
                    directory and                         expected file
                    identify files                        list.
                    included in the                       
                    snapshot according                    
                    to documented                         
                    rules.                                

  FR-REPO-02        Sygnit shall        P0                Excluded paths
                    support exclusion                     are absent from
                    rules for files and                   the manifest.
                    directories.                          

  FR-REPO-03        Sygnit shall        P0                Every included
                    calculate a                           file has a
                    cryptographic hash                    corresponding
                    for every included                    hash.
                    regular file.                         

  FR-REPO-04        Sygnit shall        P0                The manifest
                    generate a                            validates against
                    versioned manifest                    the published
                    containing relative                   schema.
                    paths, hashes,                        
                    algorithm                             
                    identifiers, and                      
                    required metadata.                    

  FR-REPO-05        Sygnit shall        P0                Repeated
                    serialize manifests                   generation for an
                    deterministically                     unchanged
                    under documented                      snapshot produces
                    canonicalization                      identical
                    rules.                                canonical signing
                                                          bytes.

  FR-REPO-06        Sygnit shall sign   P0                The signature
                    the canonical                         verifies using
                    manifest using the                    the corresponding
                    selected private                      public key.
                    key.                                  

  FR-REPO-07        Sygnit shall store  P0                Generated output
                    the manifest and                      contains no
                    signature in a                        private key.
                    documented location                   
                    without private key                   
                    material.                             

  FR-REPO-08        Sygnit should       P1                The selected
                    provide options for                   policy is
                    handling hidden                       reflected
                    files, with                           consistently in
                    documented                            the manifest.
                    defaults.                             

  FR-REPO-09        Sygnit should       P1                Symbolic links
                    define and report                     follow an
                    how symbolic links                    explicit, tested
                    are handled.                          policy.
  -------------------------------------------------------------------------

### 2.4 Verification and Integrity Reporting

  -----------------------------------------------------------------------
  ID                Requirement       Priority          Acceptance
                                                        measure
  ----------------- ----------------- ----------------- -----------------
  FR-VERIFY-01      Sygnit shall      P0                Invalid manifest
                    verify the                          signatures
                    manifest                            prevent
                    signature before                    successful
                    treating its                        verification.
                    contents as an                      
                    authenticated                       
                    reference.                          

  FR-VERIFY-02      Sygnit shall      P0                Recalculated
                    recalculate                         hashes are
                    hashes for files                    compared with
                    listed in the                       authenticated
                    manifest.                           manifest values.

  FR-VERIFY-03      Sygnit shall      P0                A changed file is
                    identify modified                   reported with its
                    files.                              relative path.

  FR-VERIFY-04      Sygnit shall      P0                Missing files are
                    identify files                      reported with
                    listed in the                       their relative
                    manifest that are                   paths.
                    missing from the                    
                    verification                        
                    directory.                          

  FR-VERIFY-05      Sygnit shall      P0                Unlisted files
                    identify                            are reported when
                    unexpected files                    strict detection
                    according to the                    is enabled.
                    selected                            
                    verification                        
                    policy.                             

  FR-VERIFY-06      Sygnit shall      P0                Output does not
                    distinguish                         label a key
                    signature                           trusted solely
                    validity from                       because a
                    public-key trust.                   signature is
                                                        mathematically
                                                        valid.

  FR-VERIFY-07      Sygnit shall      P0                The report
                    provide an                          includes a
                    overall result                      summary and
                    and per-file                        relevant file
                    verification                        statuses.
                    details.                            

  FR-VERIFY-08      Sygnit should     P1                Output conforms
                    support JSON                        to a documented
                    output for                          JSON schema.
                    automated                           
                    processing.                         

  FR-VERIFY-09      Sygnit should     P1                Tests demonstrate
                    provide strict                      the different
                    and non-strict                      handling of
                    verification                        unexpected files
                    modes with                          and policy
                    documented                          violations.
                    behavior.                           
  -----------------------------------------------------------------------

### 2.5 CLI and User Experience

  -----------------------------------------------------------------------
  ID                Requirement       Priority          Acceptance
                                                        measure
  ----------------- ----------------- ----------------- -----------------
  FR-CLI-01         Sygnit shall      P0                Commands run
                    provide a CLI                       through the
                    entry point for                     installed
                    supported                           package.
                    operations.                         

  FR-CLI-02         Sygnit shall      P0                Help is available
                    provide help                        through a
                    output describing                   documented
                    commands,                           command or
                    options, and                        option.
                    examples.                           

  FR-CLI-03         Sygnit shall      P0                Automated tests
                    return zero for                     assert exit codes
                    successful                          for success and
                    operations and                      failure cases.
                    non-zero for                        
                    failed                              
                    operations.                         

  FR-CLI-04         Sygnit shall      P0                Error tests
                    provide                             confirm useful
                    actionable error                    messages and no
                    messages without                    private-key
                    exposing secrets.                   leakage.

  FR-CLI-05         Sygnit should     P1                The command
                    provide a status                    displays
                    or inspection                       documented
                    command for                         metadata without
                    signature and                       exposing secrets.
                    manifest                            
                    metadata.                           
  -----------------------------------------------------------------------

### 2.6 Automation and Integration

  -----------------------------------------------------------------------
  ID                Requirement       Priority          Acceptance
                                                        measure
  ----------------- ----------------- ----------------- -----------------
  FR-AUTO-01        Sygnit shall      P0                Core commands run
                    support                             in a
                    non-interactive                     non-interactive
                    execution for                       test environment.
                    core signing and                    
                    verification when                   
                    required inputs                     
                    are supplied.                       

  FR-AUTO-02        Sygnit shall      P0                Exit codes are
                    provide stable                      documented and
                    exit-code                           tested.
                    behavior suitable                   
                    for scripts.                        

  FR-AUTO-03        Sygnit should     P1                A documented
                    provide examples                    example runs
                    for verification                    verification in
                    in CI workflows.                    CI.

  FR-AUTO-04        Sygnit may        P2                A future release
                    provide                             defines and tests
                    maintained GitHub                   the integration
                    Actions                             contract.
                    integration.                        
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 3. Non-Functional Requirements and Safety

### 3.1 Performance NFRs

The following are initial engineering targets. They must be measured on
documented reference hardware and adjusted using test results.

  -----------------------------------------------------------------------
  ID                      Requirement             Initial target
  ----------------------- ----------------------- -----------------------
  NFR-PERF-01             Sygnit shall generate   Local operation;
                          keys without            normally complete
                          unnecessary network     within 2 seconds on
                          access.                 reference hardware.

  NFR-PERF-02             Sygnit shall process    Memory use remains
                          files in a streaming    bounded when hashing
                          manner where practical. large files.

  NFR-PERF-03             Sygnit shall provide    Progress output does
                          progress information    not corrupt
                          for operations that     machine-readable
                          take noticeable time.   output.

  NFR-PERF-04             Sygnit shall support    Test report records
                          repositories containing elapsed time and peak
                          at least 10,000 regular memory.
                          files in performance    
                          tests.                  

  NFR-PERF-05             Sygnit shall avoid      Performance tests
                          unnecessary repeated    identify and prevent
                          hashing within a single avoidable duplicate
                          operation.              reads.

  NFR-PERF-06             Sygnit shall not        Resource-limit failures
                          silently omit files due are explicit and
                          to resource limits.     non-successful.
  -----------------------------------------------------------------------

### 3.2 Safety Invariants and Constraints --- High

1.  **No false success:** Sygnit must never report successful integrity
    verification if signature verification fails or a required file
    check fails.
2.  **Verify before trusting:** Manifest contents must not be treated as
    authenticated until the manifest signature has been successfully
    verified.
3.  **Private-key confidentiality:** Private keys must not be included
    in manifests, signatures, logs, diagnostic reports, or normal
    terminal output.
4.  **No silent file modification:** Signing and verification must not
    modify source files.
5.  **Explicit file-selection policy:** Sygnit must document which files
    are included, excluded, followed, or ignored.
6.  **Safe path handling:** Manifest paths must be validated to prevent
    path traversal outside the selected verification root.
7.  **Safe symbolic-link handling:** Symbolic links must follow a
    documented policy and must not permit unintended access outside the
    selected root.
8.  **No execution of repository content:** Verification must not
    execute scripts, build commands, hooks, or repository-provided code.
9.  **Fail closed on ambiguous security state:** Malformed manifests,
    unsupported algorithms, invalid signatures, and ambiguous trust
    information must not produce successful verification.
10. **Clear trust communication:** A valid signature must not be
    described as proof of real-world identity unless a separate trust
    mechanism establishes that identity.

### 3.3 Security NFRs --- High

  -----------------------------------------------------------------------
  ID                      Requirement             Priority
  ----------------------- ----------------------- -----------------------
  NFR-SEC-01              Sygnit shall use        High
                          established             
                          cryptographic           
                          implementations and     
                          shall not implement     
                          custom signature        
                          algorithms.             

  NFR-SEC-02              The initial signing     High
                          algorithm shall be      
                          Ed25519, subject to     
                          compatibility and       
                          security review.        

  NFR-SEC-03              The initial             High
                          file-hashing algorithm  
                          shall be SHA-256 or     
                          another explicitly      
                          approved secure hash    
                          algorithm.              

  NFR-SEC-04              The signed payload      High
                          shall use a             
                          deterministic,          
                          documented              
                          representation.         

  NFR-SEC-05              Signature metadata      High
                          shall identify format   
                          version and algorithm   
                          identifiers needed for  
                          safe verification.      

  NFR-SEC-06              Sygnit shall validate   High
                          manifest structure,     
                          path values, file       
                          counts, and input sizes 
                          before processing.      

  NFR-SEC-07              Sygnit shall use        High
                          restrictive file        
                          permissions for newly   
                          generated private keys  
                          where the operating     
                          system supports them.   

  NFR-SEC-08              Sygnit shall warn users High
                          when private-key        
                          storage permissions are 
                          broader than            
                          recommended, where      
                          detectable.             

  NFR-SEC-09              Sygnit shall not        High
                          transmit file contents, 
                          private keys, or        
                          signing metadata to a   
                          remote service during   
                          local signing and       
                          verification.           

  NFR-SEC-10              Sygnit shall document   High
                          the trust model and     
                          explain that users must 
                          obtain public keys      
                          through a trusted       
                          channel.                

  NFR-SEC-11              Sygnit shall provide a  High
                          process for reporting   
                          security                
                          vulnerabilities.        

  NFR-SEC-12              Sygnit shall be tested  High
                          against malformed       
                          manifests, invalid      
                          signatures, path        
                          traversal attempts, and 
                          adversarial file-system 
                          cases.                  
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 4. External Interfaces, Data Schemas and Protocols

### 4.1 User Interface

The primary user interface shall be a command-line interface.

Proposed command structure:

  -----------------------------------------------------------------------
  Command                             Purpose
  ----------------------------------- -----------------------------------
  `sygnit init`                       Initialize configuration or
                                      generate initial keys, according to
                                      finalized command design.

  `sygnit keygen`                     Generate a signing key pair.

  `sygnit sign <file>`                Sign an individual file.

  `sygnit sign <directory>`           Generate and sign a repository
                                      snapshot manifest.

  `sygnit verify <file>`              Verify an individual file
                                      signature.

  `sygnit verify <directory>`         Verify a repository snapshot.

  `sygnit status <directory>`         Inspect available Sygnit metadata
                                      and report status.

  `sygnit --help`                     Display command help.

  `sygnit --version`                  Display the installed version.
  -----------------------------------------------------------------------

The final command names and options shall be confirmed during CLI
design. These examples are proposed interfaces, not a finalized public
API.

### 4.2 Software Interfaces

-   **Runtime:** Node.js, with supported versions documented by the
    project.
-   **Cryptography:** Node.js built-in cryptographic APIs or another
    reviewed cryptographic library.
-   **Package distribution:** npm.
-   **Source hosting and collaboration:** Git and GitHub.
-   **Automation:** CI systems such as GitHub Actions, initially through
    documented CLI usage.
-   **Operating systems:** Windows, Linux, and macOS, subject to
    compatibility testing.

### 4.3 File and Directory Interfaces

Sygnit shall accept user-selected file and directory paths. It shall:

-   Resolve paths relative to a clearly defined working directory or
    explicit root.
-   Store repository paths as normalized relative paths.
-   Reject paths that escape the selected root.
-   Define how hidden and ignored files are treated.
-   Define how symbolic links and special files are handled.
-   Avoid including Sygnit's own generated metadata in the snapshot
    unless explicitly required by the format.

### 4.4 Proposed Manifest Schema

The following is illustrative, not a finalized format. The final schema
must define canonicalization, field validation, path rules, and version
compatibility.

``` json
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

-   Manifest format and version.
-   Signature algorithm.
-   Public-key identifier or fingerprint.
-   Signature encoding.
-   Any required domain-separation or context identifier.

The signature file must not contain private key material.

### 4.6 Protocols and Cryptographic Operations

-   File content shall be hashed using the configured approved hash
    algorithm.
-   The repository manifest shall be serialized using a deterministic
    canonicalization procedure.
-   The canonical manifest bytes shall be signed using the selected
    private key.
-   Verification shall validate the signature using the corresponding
    public key.
-   File integrity verification shall compare recalculated hashes with
    values in the authenticated manifest.
-   Unsupported algorithm identifiers and format versions shall be
    rejected or handled through explicitly documented compatibility
    behavior.

------------------------------------------------------------------------

## 5. Testing, Simulation and Evaluation Plan

### 5.1 Testing Objectives

Testing shall establish that Sygnit:

-   Produces valid signatures for supported inputs.
-   Correctly verifies valid signatures.
-   Detects content modifications.
-   Detects missing and unexpected files according to policy.
-   Rejects invalid signatures and malformed metadata.
-   Protects private-key material.
-   Handles file-system edge cases safely.
-   Behaves consistently on supported operating systems.

### 5.2 Unit Testing

Unit tests shall cover:

-   Key generation and key parsing.
-   File hashing.
-   Manifest creation.
-   Manifest canonicalization.
-   Signature creation and verification.
-   Path normalization.
-   Exclusion rules.
-   Error and exit-code mapping.

### 5.3 Integration Testing

Integration tests shall cover these complete workflows:

1.  Generate a key pair.
2.  Sign a file.
3.  Verify the unchanged file.
4.  Modify the file and confirm verification fails.
5.  Generate a repository manifest.
6.  Sign the manifest.
7.  Verify an unchanged repository.
8.  Modify, delete, and add files.
9.  Confirm the report identifies each relevant difference.

### 5.4 Negative and Security Testing

Test cases shall include:

-   Invalid public and private keys.
-   Corrupted signature files.
-   Modified manifests.
-   Unsupported manifest versions and algorithms.
-   Duplicate manifest paths.
-   Absolute paths and path traversal sequences.
-   Symbolic links pointing outside the repository.
-   Unreadable files and permission errors.
-   Very large files and manifests.
-   Files changing during hashing.
-   Private-key data accidentally appearing in logs or reports.

### 5.5 Compatibility Testing

The test matrix shall include:

-   Supported Node.js versions.
-   Windows, Linux, and macOS.
-   npm installation from a clean environment.
-   Paths containing spaces and Unicode characters.
-   Different file-system permission behaviors.

### 5.6 Performance Evaluation

Performance testing shall measure:

-   Key-generation time.
-   File-hashing throughput.
-   Repository traversal time.
-   Manifest-generation time.
-   Signing and verification time.
-   Peak memory usage.
-   Performance across different file counts and file sizes.

The test environment, hardware, operating system, Node.js version, and
repository characteristics shall be recorded so results can be
reproduced.

### 5.7 Evaluation Metrics

  -----------------------------------------------------------------------
  Metric                              Evaluation method
  ----------------------------------- -----------------------------------
  Signature correctness               Valid signatures pass; invalid
                                      signatures fail.

  Tamper detection                    Modified file content is detected.

  Missing-file detection              Files in the manifest but absent
                                      from the snapshot are reported.

  Unexpected-file detection           Unlisted files are reported
                                      according to policy.

  Key confidentiality                 No private-key material appears in
                                      generated metadata or logs.

  Manifest determinism                Equivalent snapshots produce
                                      identical canonical manifest bytes.

  Compatibility                       Core test suite passes on supported
                                      environments.

  Performance                         Time and memory use are measured
                                      against documented test
                                      repositories.

  Usability                           New users can complete the
                                      documented quick-start workflow.
  -----------------------------------------------------------------------

### 5.8 Release Testing

Before each release:

-   Run all automated tests.
-   Review security-sensitive changes.
-   Validate package contents.
-   Test installation from npm.
-   Verify documented examples.
-   Review known defects and limitations.
-   Record the release acceptance result.

------------------------------------------------------------------------

## 6. Operations Runbook, Rollout and Traceability

### 6.1 Installation and Initial Setup

The user shall be able to install Sygnit from npm using the documented
installation method.

Setup documentation shall explain:

-   Supported Node.js and npm versions.
-   Key-generation procedure.
-   Recommended private-key storage and backup practices.
-   Public-key distribution and trust establishment.
-   Basic file-signing and repository-signing workflows.
-   Verification procedures.

### 6.2 Normal Operation

A typical user workflow shall be:

1.  Install Sygnit.
2.  Generate or configure a signing key pair.
3.  Select a file or repository snapshot.
4.  Generate a signature.
5.  Distribute the signed content, manifest, signature, and public key
    through appropriate channels.
6.  Obtain and trust the public key independently.
7.  Verify the signature.
8.  Review the integrity report.

### 6.3 Failure Handling and Recovery

The operations documentation shall explain how to respond to:

-   Invalid signatures.
-   Modified or missing files.
-   Unexpected files.
-   Missing or unreadable keys.
-   Corrupted manifests.
-   Unsupported formats.
-   Permission errors.
-   Incomplete signing or verification operations.

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

  -----------------------------------------------------------------------
  Release stage           Scope                   Exit condition
  ----------------------- ----------------------- -----------------------
  Internal prototype      Validate key            Core workflows pass
                          generation, hashing,    local tests.
                          manifest creation,      
                          signing, and            
                          verification.           

  MVP / 0.1.x             Release essential file  MVP acceptance criteria
                          and repository signing  are met.
                          and verification.       

  Feature releases /      Improve reports,        Each milestone's
  0.2.x--0.3.x            configuration,          acceptance criteria are
                          automation, and         met.
                          integrations.           

  Stable release / 1.0.0  Complete security       Stable-release review
                          review, documentation,  is approved.
                          compatibility policy,   
                          and release process.    
  -----------------------------------------------------------------------

### 6.6 Maintenance and Support

The project shall:

-   Track bugs and feature requests through GitHub Issues.
-   Publish release notes and maintain a changelog.
-   Review dependencies and security advisories.
-   Provide a security vulnerability reporting channel.
-   Maintain supported Node.js and operating-system information.
-   Document breaking changes and migration steps.
-   Review community contributions according to the contribution policy.

### 6.7 Requirements Traceability

Each requirement shall be linked to at least one design element,
implementation task, and test case before the corresponding release.

Example traceability structure:

  -----------------------------------------------------------------------
  Requirement ID    Design reference  Implementation    Test reference
                                      task              
  ----------------- ----------------- ----------------- -----------------
  FR-KEY-01         Key management    Key-pair          TEST-KEY-001
                    design            generation        

  FR-FILE-02        File signing      File signature    TEST-FILE-002
                    design            creation          

  FR-REPO-04        Manifest          Manifest          TEST-REPO-004
                    specification     generation        

  FR-REPO-06        Signature format  Manifest signing  TEST-REPO-006
                    design                              

  FR-VERIFY-03      Verification      Modified-file     TEST-VERIFY-003
                    report design     detection         

  FR-VERIFY-04      Verification      Missing-file      TEST-VERIFY-004
                    report design     detection         

  NFR-SEC-01        Cryptographic     Approved crypto   TEST-SEC-001
                    design review     implementation    

  NFR-SEC-06        Input validation  Manifest and path TEST-SEC-006
                    design            validation        
  -----------------------------------------------------------------------

The traceability table shall be expanded as requirements, design
documents, implementation issues, and test cases are created. Changes to
a requirement shall trigger review of its linked design, implementation,
tests, and documentation.

------------------------------------------------------------------------

## Appendix A --- Glossary

  -----------------------------------------------------------------------
  Term                                Definition
  ----------------------------------- -----------------------------------
  Digital signature                   A cryptographic value created with
                                      a private key and verified with the
                                      corresponding public key.

  Private key                         Secret key material used to create
                                      digital signatures.

  Public key                          Key material used to verify
                                      signatures and identify the
                                      corresponding key pair.

  Hash                                A fixed-length digest calculated
                                      from input data using a
                                      cryptographic hash function.

  Manifest                            A structured document describing
                                      the files and metadata included in
                                      a repository snapshot.

  Repository snapshot                 A defined collection of files and
                                      paths at a particular point in
                                      time, independent of whether it is
                                      stored in Git.

  Integrity                           The property that data has not
                                      changed relative to a trusted
                                      reference.

  Authenticity                        Confidence that data or a signature
                                      is associated with the expected key
                                      or identity under a defined trust
                                      model.

  Provenance                          Information about the origin and
                                      history of software or an artifact.

  CI/CD                               Continuous Integration and
                                      Continuous Delivery or Deployment.

  Canonicalization                    Conversion of structured data into
                                      a deterministic representation
                                      before signing or hashing.

  Ed25519                             A public-key digital signature
                                      algorithm based on Edwards-curve
                                      Digital Signature Algorithm.

  SHA-256                             A cryptographic hash function that
                                      produces a 256-bit digest.
  -----------------------------------------------------------------------

## Appendix B --- Open Decisions

The following decisions must be finalized during architecture and
security design:

1.  Exact manifest and signature file names and directory layout.
2.  Canonical serialization format and specification.
3.  Whether file signatures use detached signature files or a shared
    manifest format.
4.  Default file inclusion and exclusion behavior.
5.  Symbolic-link and special-file handling.
6.  Whether unexpected files cause failure by default or are reported
    according to a configurable policy.
7.  Public-key fingerprint format and trust configuration.
8.  Private-key encryption and storage options.
9.  Key rotation and revocation model.
10. Supported Node.js versions and operating systems.
11. Stable CLI command names and exit-code definitions.
12. Whether a public programmatic API is included in the initial
    release.

These decisions should be documented before the related features are
declared stable.

------------------------------------------------------------------------

**End of document**
