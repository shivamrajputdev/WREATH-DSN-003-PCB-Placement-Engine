# WREATH — E. Resolution and Public API Decisions (E1–E4)

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Scope:** Definition-resolution request, response shape, snapshot granularity, and resolution failure behavior.  
**Status:** E1–E4 DECIDED. E5–E8 remain open.  
**Repository status:** No repository files were modified. This document is a separate decision record in the conversation workspace.

## Decision Summary

| Decision | Selected option | Status |
|---|---|---|
| E1 — Definition-resolution request | Option 2: ComponentId + version | DECIDED |
| E2 — Resolution response | Option 3: Typed result containing a full definition or typed error | DECIDED |
| E3 — Full snapshot vs. granular lookup | Option 1: Full definition snapshot | DECIDED |
| E4 — Missing, ambiguous, and conflicting lookup behavior | Option 1: Explicit typed errors | DECIDED |

## E1 — Definition-Resolution Request

- **Status:** DECIDED
- **Selected option:** Option 2 — `ComponentId + version`.
- **Decision:** The resolution request identifies a component and the requested definition revision/version. The shared contract must support resolving a specific revision.
- **Rationale:** This is consistent with the prior decision to pin each schematic instance to a specific definition revision. Resolution must not silently substitute the latest revision when the requested revision is unavailable.
- **Impact on DSN-001:** Send the component identity and the revision pinned by the schematic instance when resolving an existing instance.
- **Impact on DSN-002:** Support lookup by component identity and exact revision.
- **Impact on shared contracts:** Define a resolution request carrying the stable component identity and revision identifier.
- **Evidence/tests required:** Resolve an existing component revision successfully; verify that requesting an unavailable revision does not fall back to latest.
- **Follow-up:** Define the concrete revision identifier type and the request's serialized representation.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E1 unresolved details

- Whether the revision field is always required or can be optional for a separate, explicitly defined “resolve current/latest” workflow. For existing pinned schematic instances, the pinned revision is required.
- Whether the version identifier is a monotonic revision counter, immutable version key, or a contract that combines these concepts. Prior C5 chose a stable definition identity with a revision counter, while C6 chose pinning an exact revision; the exact type and lifecycle semantics remain open.
- How a component identity is represented in the request, including the agreed library-scoped identity and any optional UUID/hash metadata.
- How revisions are retained, queried, and resolved when a library is offline or a revision has been removed.
- Whether creation/search workflows need a separate query API later. Search-by-name/attributes is not part of this decision.

## E2 — Resolution Response

- **Status:** DECIDED
- **Selected option:** Option 3 — `Result<DefinitionSnapshot, ResolutionError>` in conceptual terms.
- **Decision:** Resolution returns a typed result. Success contains the full resolved definition snapshot; failure contains a typed resolution error.
- **Rationale:** This provides the consumer with the required data in one response while making resolution failures machine-readable and testable.
- **Impact on DSN-001:** Consume the full snapshot on success and handle typed failures without silently rebinding the instance.
- **Impact on DSN-002:** Produce the resolved snapshot or an explicit typed error.
- **Impact on shared contracts:** Define the response envelope, definition snapshot, and resolution error types. The exact Rust type names and fields remain to be designed.
- **Evidence/tests required:** Successful resolution; missing component; missing pinned revision; ambiguous lookup where applicable; conflicting identity/revision data.
- **Follow-up:** Define the typed response and error payloads in the shared contract.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E2 unresolved details

- Exact snapshot fields and which metadata are required for consumers.
- Whether the returned snapshot must echo the canonical component identity and exact resolved revision. Recommended for validation and traceability, but field-level contract details are not finalized.
- Error payload structure, including stable error codes, human-readable diagnostics, and contextual fields.
- Whether errors include source/library context and recovery hints.
- Serialization format and forward/backward compatibility policy for the contract.
- Whether the API uses Rust's concrete `Result` type directly or a transport-neutral equivalent if a future process boundary is introduced. The current direction is a Rust-native contract for the in-process MVP.

## E3 — Full Definition/Snapshot vs. Granular Lookup

- **Status:** DECIDED
- **Selected option:** Option 1 — Full definition snapshot.
- **Decision:** The MVP resolution API returns the complete definition snapshot needed by the consumer. Granular lookup is deferred.
- **Rationale:** A single complete result reduces API surface and avoids splitting one logical resolution across multiple queries.
- **Impact on DSN-001:** Receive the data required to use the resolved component definition without making follow-up calls for pins or symbol data.
- **Impact on DSN-002:** Assemble and return the complete definition snapshot for the requested revision.
- **Impact on shared contracts:** Define one snapshot response shape for the supported definition content.
- **Evidence/tests required:** Verify that the snapshot contains the data required by the supported DSN-001 slice and survives serialization/round-trip tests if serialization is used.
- **Follow-up:** Consider granular lookup only after profiling demonstrates a real performance or memory need.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E3 unresolved details

- The exact fields considered necessary for a complete snapshot in the current implementation slice.
- Whether snapshot values are deeply owned copies or use another in-process representation. An opaque lazy handle is not selected for the MVP.
- Whether unsupported/deferred content is omitted, represented explicitly, or causes a typed error.
- Snapshot immutability expectations after resolution and whether the consumer may retain the snapshot for the lifetime of a schematic operation.
- Performance thresholds that would justify a future granular API.

## E4 — Missing, Ambiguous, and Conflicting Lookup Behavior

- **Status:** DECIDED
- **Selected option:** Option 1 — Explicit typed errors.
- **Decision:** Resolution failures must be distinguishable through typed error variants. At minimum, the contract must distinguish a missing component (`NotFound`), an ambiguous lookup (`Ambiguous` where a query can produce multiple candidates), and a missing requested revision (`RevisionNotFound`). Conflicting identity/revision data must also fail explicitly rather than silently selecting or merging a result.
- **Rationale:** This aligns with revision pinning, explicit unresolved-reference diagnostics, and the prior rule that identity collisions are hard errors rather than last-write-wins or silent merges.
- **Impact on DSN-001:** Handle each error deliberately and preserve unresolved references when safe. Do not fall back to the latest revision or bind a different component automatically.
- **Impact on DSN-002:** Return stable, typed error categories that do not require callers to parse message text.
- **Impact on shared contracts:** Define typed error variants and any structured context needed for diagnosis.
- **Evidence/tests required:** Tests for missing component, missing pinned revision, ambiguous query if/when query lookup exists, and conflicting identity/revision data. Verify there is no implicit latest-version fallback.
- **Follow-up:** Agree on error variant names, payload fields, and the boundary between invalid request, not found, ambiguity, and integrity/conflict errors.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E4 unresolved details

- Exact Rust enum names and payload schemas.
- Whether `Ambiguous` is included in the MVP error enum even though E1 selects direct identity-plus-revision lookup rather than query/search. Recommendation: reserve a stable typed category only if it fits the shared contract's error model; it need not be emitted by the initial direct-lookup path.
- Exact categories for malformed IDs, invalid revision identifiers, storage/I/O failures, corrupt definitions, and internal failures.
- Which failures are recoverable and how DSN-001 represents them in the UI/document model.
- How conflicts are detected when the same logical identity appears with inconsistent metadata.

## Consistency Review Against Prior WREATH Decisions

### Identity and revision decisions

- **B1–B2:** E1 uses the stable component identity; library-scoped identity remains the primary identity choice. Optional UUID and hash semantics remain as previously decided and are not replaced by the revision field.
- **B7–B10:** Stable instance identity remains separate from mutable reference designators and definition revision. Duplicate/collision behavior remains explicit; this decision introduces no silent merge or identity replacement.
- **C5:** Stable definition identity plus revision counter is consistent with exact-version resolution. The precise revision identifier and lifecycle semantics remain open.
- **C6:** Pin exact revision per schematic instance. E1 directly implements this resolution requirement.
- **C7:** Existing instances retain their pinned revision and are not automatically updated. E1/E4 require a missing pinned revision to fail explicitly, not fall back to latest.
- **C8:** Missing component or missing pinned revision produces explicit diagnostics. E2/E4 provide typed error semantics that support this behavior.
- **C1–C4:** The snapshot's content must respect current scope: component-scoped symbols are supported; multi-unit support, footprint association, and pin-to-pad mapping remain deferred. E3 does not implicitly bring those deferred features into scope.

### Architecture and shared-contract decisions

- **A1/A2:** The decisions fit the Rust workspace and in-process crate architecture. No transport or process boundary is introduced.
- **A4–A6:** The resolution request, snapshot, and typed errors belong in the shared-contract crate. Neither engine should depend on the other's private concrete implementation types.
- **A7:** Direct Rust calls remain the MVP direction. This record does not define gRPC, protobuf, or a remote service contract.
- **Ownership boundary:** DSN-002 provides library definition resolution; DSN-001 consumes definitions. E6 remains open and will formalize read/write ownership.
- **D1–D10:** Geometry values in a snapshot must use the agreed WREATH geometry conventions. These API decisions do not settle the outstanding geometry details such as transformed-coordinate quantization, text behavior, or primitive field semantics.

### Consistency risks to resolve before implementation

1. Define a single revision identifier model consistent with C5/C6 and use it in the request, snapshot, and diagnostics.
2. Ensure an unavailable pinned revision never becomes a latest-version lookup.
3. Keep the full snapshot scoped to features approved for the MVP; do not accidentally imply multi-unit or footprint support.
4. Keep error categories stable and typed, while distinguishing resolution errors from storage, validation, and internal failures.
5. Define the snapshot's required identity/revision metadata so DSN-001 can verify what was resolved.
6. Confirm the authoritative ticket/acceptance criteria and approved implementation slice before coding.

## E5–E8 Status

The following decisions were not made in this session and remain open:

- **E5 — Caching policy:** OPEN.
- **E6 — Read/write API ownership:** OPEN.
- **E7 — Batch-operation requirements:** OPEN.
- **E8 — Concurrency expectations:** OPEN.

## Change-Control Note

This document records the Project Lead's E1–E4 selections and their consistency implications. It does not itself authorize implementation or repository changes. E5–E8 remain open. No repository files were modified while creating this decision document.


---

# Project Lead Decision Record: E5–E8

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Status:** E1–E8 DECIDED.  
**Repository status:** This is a separate updated decision document in the conversation workspace. No repository files were modified.

## Decision Summary

| Decision | Selected option | Status |
|---|---|---|
| E5 — Caching policy | Option 1: Defer caching | DECIDED |
| E6 — Read/write API ownership | Option 1: DSN-002 owns library writes | DECIDED |
| E7 — Batch-operation requirements | Option 1: Single-item resolution only | DECIDED |
| E8 — Concurrency expectations | Option 1: Single-threaded MVP with explicit boundaries | DECIDED |

## E5 — Caching Policy

- **Status:** DECIDED
- **Selected option:** Option 1 — Defer caching.
- **Decision:** The MVP will not introduce a dedicated resolved-definition cache. Resolution occurs on demand. Caching may be reconsidered after profiling demonstrates a need.
- **Rationale:** Avoid cache invalidation and lifecycle complexity before there is performance evidence. This does not change revision-pinning semantics.
- **Impact on DSN-001:** Do not depend on a cache API or cache invalidation behavior in the MVP.
- **Impact on DSN-002:** Implement correct resolution without requiring a dedicated cache layer.
- **Impact on shared contracts:** No cache-specific contract is required for the MVP.
- **Evidence/tests required:** Correct repeated resolution of the same component and exact revision; confirm that repeated resolution does not substitute another revision.
- **Follow-up:** Profile realistic workloads before proposing caching.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E5 unresolved details

- Whether DSN-002's underlying storage or runtime naturally caches data internally is an implementation detail not settled by this decision, provided observable behavior remains correct.
- If caching is later added, cache keys must distinguish component identity and exact revision.
- Future invalidation, eviction, memory limits, and cache ownership remain undecided.
- “Re-resolve as needed” does not mean a pinned instance may float to the latest revision. The requested revision remains authoritative.

## E6 — Read/Write API Ownership

- **Status:** DECIDED
- **Selected option:** Option 1 — DSN-002 owns library writes.
- **Decision:** DSN-002 owns creation, update, and deletion operations for reusable library data and its revision lifecycle. DSN-001 is a read-only consumer of resolved library definitions and owns schematic-document/instance behavior within its approved scope.
- **Rationale:** One authoritative owner prevents competing mutation paths and keeps library lifecycle rules with the engine that owns reusable definitions.
- **Impact on DSN-001:** Use the resolution/read interface; do not mutate DSN-002's library storage directly.
- **Impact on DSN-002:** Own library mutations and enforce identity, collision, and revision rules.
- **Impact on shared contracts:** The shared contract may define public read/resolution types. This decision does not require exposing library write operations through the shared contract.
- **Evidence/tests required:** Architectural review or API-level tests demonstrating DSN-001 has no direct library mutation path; verify DSN-002 enforces identity and revision invariants.
- **Follow-up:** Document the public read API and DSN-002-owned mutation boundary.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E6 unresolved details

- Exact public methods for library creation, update, deletion, and revision creation.
- Whether deletion means logical removal, archival, or physical deletion. Existing pinned revisions must not silently change meaning or become a different revision; retention policy remains open.
- Permissions and authorization are outside this ownership decision and remain unspecified.
- Whether any future UI or external API exposes DSN-002 write operations, and through what adapter, remains open.
- The precise DSN-001 schematic-document write API is separate from DSN-002 library write ownership.

## E7 — Batch-Operation Requirements

- **Status:** DECIDED
- **Selected option:** Option 1 — Single-item resolution only.
- **Decision:** The MVP resolution API supports one component identity and requested revision per call. Batch resolution is deferred until measured performance or a concrete workflow requires it.
- **Rationale:** A single-item request keeps error semantics and integration testing straightforward for the initial slice.
- **Impact on DSN-001:** Resolve definitions through individual calls.
- **Impact on DSN-002:** Implement and validate single-item resolution; batch-specific partial-success semantics are not required for the MVP.
- **Impact on shared contracts:** Define a single-item request and response. No batch request/response types are required now.
- **Evidence/tests required:** Single-item success and failure tests, including missing pinned revision and no-fallback behavior.
- **Follow-up:** Profile representative schematic loads before designing batch resolution.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E7 unresolved details

- Performance thresholds that justify batching.
- Whether a future batch API returns per-item results, fails the whole batch, or supports both modes.
- Ordering, duplicate requests, cancellation, and resource limits for any future batch API.
- Whether parallel resolution is useful after concurrency requirements are revisited.

## E8 — Concurrency Expectations

- **Status:** DECIDED
- **Selected option:** Option 1 — Single-threaded MVP with explicit boundaries.
- **Decision:** The MVP does not promise concurrent access or thread-safe behavior as a public contract. Implement the current workflow without unnecessary synchronization, while keeping engine and contract boundaries clear enough to revisit concurrency when requirements justify it.
- **Rationale:** Avoid speculative synchronization and trait bounds while not making concurrency an implicit architectural impossibility.
- **Impact on DSN-001:** The current resolution integration may be sequential. It must not rely on undocumented concurrent safety.
- **Impact on DSN-002:** Implement the current single-threaded workflow without a public concurrent-access guarantee.
- **Impact on shared contracts:** No `Send`/`Sync` requirement is introduced solely by this decision. Future bounds must follow an explicit concurrency requirement.
- **Evidence/tests required:** Tests for the supported sequential workflow and API/architecture review confirming no concurrency guarantee is accidentally claimed.
- **Follow-up:** Revisit concurrency when UI responsiveness, background operations, batching, or parallel workloads have concrete requirements.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

### E8 unresolved details

- Whether individual internal types happen to be thread-safe is not a public guarantee and is not settled here.
- Future concurrent-read/serialized-write behavior, synchronization strategy, cancellation, and lifecycle rules remain open.
- The Rust compiler's `Send`/`Sync` bounds should not be added or removed mechanically; evaluate them against the eventual API and execution model.
- Build and test commands may execute project code or build scripts. This decision does not authorize running untrusted code or change the existing repository execution-safety rules.

## Consistency Review — E1–E8 Against Prior WREATH Decisions

### Identity and revision pinning

- **B1–B2:** E1's component identity remains library-scoped; optional UUID/hash metadata do not replace the primary identity choice.
- **B7–B10:** Instance identity, mutable reference designator, duplicate handling, and collision behavior remain unchanged by caching, ownership, batching, or concurrency decisions.
- **C5:** Stable definition identity and a distinct revision counter remain consistent with exact-revision requests. The concrete revision identifier and lifecycle semantics are still open.
- **C6–C7:** Existing instances request and retain their pinned revision. Deferring caching and batching does not permit fallback to latest or automatic update.
- **C8:** Missing component or pinned revision still produces explicit diagnostics. E2/E4's typed result and error choices remain authoritative.
- **E5:** Any future cache must include the exact revision in its logical key and must not alter resolution semantics.
- **E6:** DSN-002's ownership of library mutations supports consistent identity/revision enforcement in one place.
- **E7:** Single-item calls preserve per-request revision specificity without defining batch partial-failure behavior.
- **E8:** Single-threaded expectations do not change the identity or revision rules.

### Shared contract and architecture

- **A1/A2:** All four decisions preserve the Rust workspace and in-process engine API direction.
- **A4–A6:** Request, snapshot, and typed error types remain shared-contract concerns; library storage and mutation implementation remain owned by DSN-002.
- **A7:** No service transport or gRPC commitment is introduced.
- **C1–C4 and D1–D10:** These API decisions do not bring deferred multi-unit, footprint, pin-to-pad, or detailed compatibility features into scope. Returned snapshots must use the agreed WREATH geometry contract for the supported slice.
- **Ownership boundary:** DSN-002 owns reusable library mutations; DSN-001 consumes definitions and manages schematic behavior. The shared contract defines the interface rather than becoming a third data owner.

### Implementation gates and remaining risks

1. Define the exact revision identifier and its retention/lookup semantics before implementing E1.
2. Define the minimum full snapshot fields and stable typed error payloads.
3. Ensure library deletion or archival cannot silently rebind existing instances.
4. Keep DSN-001 from bypassing DSN-002 to mutate library data.
5. Do not introduce a cache, batch API, or concurrency guarantee without a separately reviewed requirement.
6. Confirm authoritative ticket revisions and approved acceptance criteria before implementation.

## Final E Decision Status

| Decision | Status | Selected direction |
|---|---|---|
| E1 | DECIDED | Component identity + exact version/revision |
| E2 | DECIDED | Typed result containing full snapshot or typed error |
| E3 | DECIDED | Full definition snapshot |
| E4 | DECIDED | Explicit typed errors |
| E5 | DECIDED | Defer caching |
| E6 | DECIDED | DSN-002 owns library writes; DSN-001 reads |
| E7 | DECIDED | Single-item resolution only |
| E8 | DECIDED | Single-threaded MVP; no public concurrency promise |

## Change-Control Note

This document records the Project Lead's E1–E8 selections and consistency review. It does not itself authorize implementation or repository changes. Exact type definitions, revision lifecycle, snapshot fields, error payloads, deletion/retention policy, and future performance/concurrency triggers remain explicit follow-up items. No repository files were modified when creating this document.
