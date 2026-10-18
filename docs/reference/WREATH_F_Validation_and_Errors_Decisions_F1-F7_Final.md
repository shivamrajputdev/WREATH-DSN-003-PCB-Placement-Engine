# WREATH — F. Validation and Errors Decisions (F1–F4)

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Scope:** Shared error taxonomy, validation ownership, invalid-reference behavior, and basic save atomicity.  
**Status:** F1–F4 DECIDED. F5–F7 remain open.  
**Repository status:** This is a separate decision record in the conversation workspace. No repository files were modified.

## Decision Summary

| Decision | Selected option | Status |
|---|---|---|
| F1 — Shared error taxonomy | Option 1: Shared error enum | DECIDED |
| F2 — Validation ownership | Option 1: Domain ownership with boundary validation | DECIDED |
| F3 — Invalid-reference policy | Option 1: Diagnostic and degraded loading | DECIDED |
| F4 — Partial failure and atomicity | Option 1: Basic write-then-rename | DECIDED |

## F1 — Shared Error Taxonomy

- **Status:** DECIDED
- **Selected option:** Option 1 — Shared error enum.
- **Decision:** Define cross-engine error categories in `shared-contracts`. Each engine may retain internal error types and convert them into the shared error type at its public boundary.
- **Rationale:** The E2/E4 decisions already require a typed resolution result and distinguishable resolution failures. A shared enum gives cross-engine callers stable machine-readable categories without forcing every internal implementation error into one global type.
- **Impact on DSN-001:** Handle the shared error variants exposed by the interfaces it consumes.
- **Impact on DSN-002:** Convert library-resolution and other cross-engine failures into the appropriate shared variants at the boundary.
- **Impact on shared contracts:** Define the shared error enum and its stable semantics.
- **Evidence/tests required:** Integration tests that exercise successful calls and representative shared-error paths; tests should verify categories rather than matching message strings.
- **Follow-up:** Define the enum variants, payloads, and conversion rules.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F1 unresolved details

- Exact variant names and payload fields.
- The boundary between resolution errors, validation errors, storage/I/O errors, serialization errors, and internal failures.
- Whether nested source errors are retained, summarized, or omitted from the shared type.
- Whether diagnostics such as severity and source location belong in a separate diagnostic model rather than the shared error enum.
- Stability and evolution policy for the public error taxonomy.
- Avoid turning the shared enum into an unbounded dumping ground for every engine-internal error; internal errors may remain engine-specific.

## F2 — Validation Ownership

- **Status:** DECIDED
- **Selected option:** Option 1 — Each engine validates its own domain, with consumer checks at boundaries.
- **Decision:** DSN-001 validates schematic integrity. DSN-002 validates library-definition integrity. DSN-001 also verifies that a resolved definition meets the minimum requirements for safe use at the consumer boundary.
- **Rationale:** Domain ownership avoids duplicating all validation rules, while boundary checks protect DSN-001 from malformed, incomplete, or incompatible data.
- **Impact on DSN-001:** Validate schematic structures and perform minimum-use checks on resolved snapshots before relying on them.
- **Impact on DSN-002:** Validate definitions and library invariants before exposing them through resolution.
- **Impact on shared contracts:** Document the minimum validity expectations for data crossing the engine boundary.
- **Evidence/tests required:** Engine-local validation tests plus integration tests with valid and deliberately invalid snapshots/definitions.
- **Follow-up:** Define the validation rules owned by each engine and the minimal DSN-001 boundary checks.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F2 unresolved details

- The exact validation rule set for each engine.
- Which checks are mandatory before persistence, before resolution, and before use.
- Whether validation returns the first error or collects multiple findings.
- How schema-version incompatibility and deferred features are reported.
- How to avoid divergent duplicate rules: DSN-001 boundary checks should verify minimum consumer preconditions, not reimplement DSN-002's entire library validator.
- Whether validation functions are exposed through the shared contract or remain engine-owned with shared input/output types.

## F3 — Invalid-Reference Policy

- **Status:** DECIDED
- **Selected option:** Option 1 — Explicit diagnostic and degraded loading.
- **Decision:** Missing or invalid references, including references to components, pins, or nets, must be reported explicitly. The engine must not crash solely because a reference is broken. Load the rest of the schematic in a degraded state when safe, preserve unresolved references where possible, and never silently substitute a different component, pin, net, or revision.
- **Rationale:** This is consistent with C8's graceful-degradation policy and E1–E4's exact-revision and typed-error semantics.
- **Impact on DSN-001:** Detect and represent broken schematic references and allow safe inspection/recovery of the rest of the document.
- **Impact on DSN-002:** Return clear typed resolution errors, including missing component and missing pinned revision where applicable.
- **Impact on shared contracts:** Provide error categories sufficient to distinguish resolution failures from other invalid-reference conditions.
- **Evidence/tests required:** Negative tests for missing component, missing pinned revision, missing pin, and missing net; verify no crash and no silent rebind.
- **Follow-up:** Add broken-reference tests and define which unresolved-reference data is preserved.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F3 unresolved details

- Whether unresolved references use explicit placeholder entities, a separate unresolved-reference record, or another representation.
- Which operations remain permitted on a degraded schematic, especially editing and saving.
- Whether saving a degraded schematic preserves unknown/unresolved data losslessly.
- Exact diagnostic severity and location/context fields; F5 remains open.
- How to distinguish a missing reference from a malformed identifier or a reference to an unsupported feature.
- What happens when the schematic itself is too malformed to load safely; degraded loading is not a requirement to accept arbitrary corrupt input.

## F4 — Partial Failure and Atomicity

- **Status:** DECIDED
- **Selected option:** Option 1 — Basic write-then-rename.
- **Decision:** For the MVP save path, write the new contents to a temporary file and rename it into place after the write succeeds. Defer a full atomic-save, crash-consistency, and recovery design.
- **Rationale:** This is a bounded MVP improvement over direct overwrite while avoiding a premature cross-platform persistence subsystem.
- **Impact on DSN-001:** Use the basic write-then-rename approach for schematic saves where applicable.
- **Impact on DSN-002:** Apply the same basic strategy to file-backed library saves where applicable, without changing revision and identity rules.
- **Impact on shared contracts:** No substantial shared-contract addition is required; persistence errors still need an appropriate shared boundary representation if exposed cross-engine.
- **Evidence/tests required:** Test successful save and representative write/rename failures; verify a failed write does not replace the target before the temporary write succeeds.
- **Follow-up:** Design and validate stronger atomicity/recovery behavior if requirements demand it.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F4 unresolved details and caveats

- Temporary-file naming, location, permissions, cleanup, and collision behavior.
- Replacement semantics of rename on supported operating systems and filesystems.
- Whether the temporary file and parent directory require explicit flush/sync for durability.
- Recovery from process crash, power loss, or a failure between write and rename.
- Backup/version retention and recovery UX.
- Behavior when the target is open, locked, or modified concurrently.
- Write-then-rename is a basic strategy, not a universal guarantee of crash durability or identical atomic replacement behavior across platforms.

## Consistency Review Against Prior WREATH Decisions

### Identity and revision pinning

- **B1–B2:** Validation and error reporting do not change the library-scoped component identity. Optional UUID/hash metadata remain separate from primary identity and revision semantics.
- **B7–B10:** Invalid references and conflicts must not silently replace stable instance identities or merge colliding entities.
- **C5–C7:** Exact definition revision remains part of resolution. If a pinned revision is unavailable, F3 requires an explicit diagnostic; F1 provides typed cross-engine errors. No latest-version fallback is permitted.
- **C8:** F3 directly implements graceful degradation with explicit diagnostics and preservation of unresolved references where safe.
- **E1–E4:** F1 complements `Result<DefinitionSnapshot, ResolutionError>` conceptually by defining a shared error taxonomy. F2 adds consumer boundary checks without replacing DSN-002's domain validation. F3 preserves explicit `NotFound`/`RevisionNotFound` behavior. F4 does not change resolution semantics.
- **E5–E8:** No caching, batching, or concurrency guarantee is introduced. Single-item resolution and single-threaded MVP assumptions remain intact.

### Shared contract and engine ownership

- **A1/A2:** These decisions fit the Rust workspace and in-process engine APIs.
- **A4–A6:** The shared-contract crate owns cross-engine error types and shared boundary semantics, not either engine's entire internal error model or private validation implementation.
- **A7:** No process transport or gRPC contract is introduced.
- **E6:** DSN-002 owns library mutations and library integrity; DSN-001 owns schematic integrity and remains a read-only consumer of library definitions. F2 reinforces rather than blurs that ownership.
- **C1–C4 and D1–D10:** Validation must respect the approved slice and geometry contract. Deferred multi-unit, footprint, pin-to-pad, and detailed compatibility work does not become mandatory because validation exists.

### Consistency risks to resolve before implementation

1. Define shared error variants and conversion rules so E4's resolution errors remain distinguishable.
2. Specify DSN-001's minimum boundary checks without duplicating all DSN-002 validation logic.
3. Define a safe representation and persistence policy for unresolved references before promising lossless degraded editing/saving.
4. Document write-then-rename behavior for supported platforms and test failure paths; do not claim full crash durability.
5. Confirm authoritative ticket revisions and acceptance criteria before implementation.

## F5–F7 Status

These decisions were not made in this session and remain open:

- **F5 — Diagnostic structure:** OPEN.
- **F6 — Unsupported-feature behavior:** OPEN.
- **F7 — Failure recovery and user-visible reporting:** OPEN.

## Change-Control Note

This document records the Project Lead's F1–F4 selections and their consistency implications. It does not itself authorize implementation or repository changes. F5–F7 remain open, and detailed error variants, validation rules, degraded-document representation, and persistence guarantees remain follow-up work. No repository files were modified while creating this document.


---

# Project Lead Decision Record: F5–F7

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Status:** F1–F7 DECIDED.  
**Repository status:** This is a separate final decision-document copy in the conversation workspace. No repository files were modified.

## Decision Summary

| Decision | Selected option | Status |
|---|---|---|
| F5 — Diagnostic structure | Option 1: Defer detailed diagnostic model | DECIDED |
| F6 — Unsupported-feature behavior | Option 2: Warn explicitly, preserve when possible, reject unsafe operations | DECIDED |
| F7 — Failure recovery and user-visible reporting | Option 1: Logs and test output for the MVP | DECIDED |

## F5 — Diagnostic Structure

- **Status:** DECIDED
- **Selected option:** Option 1 — Defer the detailed diagnostic model.
- **Decision:** For the MVP, use `Result<T, SharedError>` for operation success/failure. Defer a richer diagnostic framework, including severity levels, source locations, diagnostic collections, and recovery suggestions.
- **Rationale:** This matches the original decision options and keeps the current implementation slice narrow.
- **Impact on DSN-001:** Propagate and handle shared errors; do not depend on a full diagnostic panel or diagnostic aggregation framework.
- **Impact on DSN-002:** Return shared errors for cross-engine failures and retain engine-local details where useful internally.
- **Impact on shared contracts:** No full diagnostic type system is required for the MVP.
- **Evidence/tests required:** Test success and failure paths using stable error categories rather than relying on message strings.
- **Follow-up:** Design a structured diagnostic model when user-facing reporting or richer validation workflows are in scope.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F5 unresolved details and guardrails

- Exact shared error variants and payload fields remain open under F1.
- A non-fatal validation finding is not necessarily an operation failure. The MVP must not misrepresent a successful-but-degraded load as fully valid merely because the rich diagnostic model is deferred.
- How warnings are surfaced internally, accumulated, or associated with entities remains open.
- Severity levels, source locations, related entities, recovery suggestions, and diagnostic aggregation remain deferred.
- Deferring diagnostics does not cancel F3's requirement to report broken references or F6's requirement to report unsupported features. The exact reporting channel can be basic logs/test output for the MVP.

## F6 — Unsupported-Feature Behavior

- **Status:** DECIDED
- **Selected option:** Option 2 — Explicit warning, preserve when possible, reject unsafe operations.
- **Decision:** Unsupported features must not be silently discarded. The engine should continue loading or processing supported content when safe, preserve unsupported data when feasible, and reject operations that would knowingly destroy unsupported information that cannot be preserved.
- **Rationale:** This balances graceful degradation with data integrity. It is consistent with F3's degraded-loading policy without promising that every unsupported feature can be round-tripped losslessly.
- **Impact on DSN-001:** Identify unsupported schematic content, report it through the MVP's available error/logging path, and preserve it where feasible. Do not silently remove it during a load/edit/save cycle.
- **Impact on DSN-002:** Identify unsupported library features or fields, preserve them where feasible, and avoid destructive writes when preservation is impossible and the operation would lose data.
- **Impact on shared contracts:** No complete unknown-feature framework is required now. If unsupported content crosses an engine boundary, the contract must not silently reinterpret it as supported content.
- **Evidence/tests required:** Tests with supported content plus unsupported fields/features; verify safe loading where possible, explicit reporting, preservation where implemented, and rejection of destructive operations where preservation is impossible.
- **Follow-up:** Define unsupported-feature detection, preservation representation, and save/export behavior when those requirements enter the approved slice.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F6 unresolved details and guardrails

- Which features/fields count as unsupported in the initial slice.
- Whether unknown data can be retained as opaque serialized data, extension fields, or another representation.
- Whether preservation is lossless across load/edit/save, and what happens if a user changes adjacent supported data.
- The exact condition for rejecting save/export rather than allowing a warning-only operation.
- Whether a format adapter may pass unknown data through unchanged or must reject content it cannot safely interpret.
- Severity and user-facing presentation remain open under F5/F7.
- This decision does not require accepting malformed or unsafe input; the engine may reject data that cannot be safely parsed or preserved.

## F7 — Failure Recovery and User-Visible Reporting

- **Status:** DECIDED
- **Selected option:** Option 1 — Logs and test output only for the MVP.
- **Decision:** Defer UI-level error reporting and recovery workflows. During the MVP, failures must remain observable through returned errors, logs, and test output as appropriate. This decision does not authorize swallowing errors or treating failures as success.
- **Rationale:** It keeps UI and recovery experience outside the initial engine slice while retaining testability and debuggability.
- **Impact on DSN-001:** Propagate shared errors to callers and provide useful logs/test evidence. No diagnostic panel or recovery UI is required in the MVP.
- **Impact on DSN-002:** Return typed errors and provide appropriate logs/test evidence for failures.
- **Impact on shared contracts:** No UI-specific reporting model is required. Shared errors remain structured according to F1 and E2/E4.
- **Evidence/tests required:** Tests verify failure propagation and ensure important errors are not silently swallowed. Logs should not be the only programmatic error signal where the API returns a result.
- **Follow-up:** Design user-visible error presentation and recovery workflows when the relevant UI/API consumer is in scope.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### F7 unresolved details

- Logging framework, levels, output destinations, and structured fields.
- Which failures are logged at which layer to avoid duplicated or noisy logs.
- Redaction of sensitive paths or data in log messages.
- Whether any future headless API consumer needs structured diagnostics beyond returned shared errors.
- User-facing recovery actions, diagnostic navigation, and error-history retention remain deferred.

## Consistency Review — F1–F7 Against Prior WREATH Decisions

### Identity and revision pinning

- **B1–B2:** F1/F2 do not alter the library-scoped primary component identity or the distinct roles of optional UUID/hash metadata.
- **B7–B10:** Invalid references, unsupported data, and conflicts must not silently replace stable instance identity or merge colliding entities.
- **C5–C7:** Existing schematic instances remain pinned to their exact definition revisions. Missing pinned revisions must produce explicit errors/diagnostics; unsupported-feature handling must not trigger fallback to latest.
- **C8:** F3's graceful degradation remains active. F6 adds a data-preservation guardrail: load supported content when safe, preserve unresolved/unsupported information where possible, and avoid destructive saves when preservation is impossible.
- **E1–E4:** The resolution API continues to return a typed result with a full definition snapshot or typed error. Missing component/revision and other resolution failures remain machine-readable.
- **E5–E8:** No caching, batch API, or public concurrency guarantee is introduced. Error and validation behavior must work within the single-item, single-threaded MVP assumptions.

### Shared contract and architecture

- **A1/A2:** The decisions preserve the Rust workspace and in-process crate architecture.
- **A4–A6:** Cross-engine errors remain in `shared-contracts`; internal error details may remain engine-specific. Validation ownership stays with each engine's domain, with DSN-001 performing minimum consumer-boundary checks.
- **A7:** No remote transport or gRPC commitment is introduced.
- **E6:** DSN-002 owns library writes and library integrity; DSN-001 consumes library definitions and owns schematic integrity. F2 reinforces this boundary.
- **C1–C4 and D1–D10:** Unsupported-feature policy does not bring deferred multi-unit, footprint, pin-to-pad, or detailed compatibility features into scope. Geometry must continue to follow the approved WREATH conventions for supported content.
- **F4:** Basic write-then-rename remains the selected MVP save strategy. F6's preservation guardrail means the engine must not proceed with a save that it knows would destructively discard unsupported data that it cannot preserve. Write-then-rename alone does not guarantee universal crash durability.

### Consistency risks to resolve before implementation

1. Define F1's shared error variants so E4 resolution failures remain distinguishable.
2. Specify how F3/F6 represent unresolved and unsupported content when degraded loading succeeds.
3. Define the MVP's safe-save rejection conditions where unsupported data cannot be preserved.
4. Ensure F5/F7 deferral does not cause errors or unsupported-feature warnings to be silently discarded.
5. Keep DSN-001 boundary checks limited to consumer preconditions rather than duplicating DSN-002's full validator.
6. Confirm authoritative ticket revisions and acceptance criteria before implementation.

## Final F Decision Status

| Decision | Status | Selected direction |
|---|---|---|
| F1 | DECIDED | Shared error enum in `shared-contracts` |
| F2 | DECIDED | Domain-owned validation plus consumer boundary checks |
| F3 | DECIDED | Explicit broken-reference diagnostics; degraded loading when safe |
| F4 | DECIDED | Basic write-then-rename; full atomicity design deferred |
| F5 | DECIDED | Defer detailed diagnostic framework; use `Result<T, SharedError>` |
| F6 | DECIDED | Warn explicitly, preserve unsupported data where possible, reject unsafe destructive operations |
| F7 | DECIDED | Logs and test output for MVP; defer UI-level reporting/recovery |

## Change-Control Note

This document records the Project Lead's F1–F7 selections and their consistency implications. It does not itself authorize implementation or repository changes. Exact error variants, validation rules, unknown-data preservation, safe-save rejection criteria, logging details, and the future diagnostic/user-facing reporting model remain explicit follow-up items. No repository files were modified while creating this final decision document.
