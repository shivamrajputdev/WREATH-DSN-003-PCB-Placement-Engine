# WREATH-DSN-003 — Phase 1 Native Data Contract Proposal

**Status:** DRAFT / PROPOSED — NOT APPROVED

**Scope:** Manual-placement MVP only. Automatic placement is a separate increment and is not defined by this proposal.

**Sources:** `docs/project/WREATH-DSN-003_Phase0_Audit.md`, `docs/project/WREATH-DSN-003_Manual_Placement_MVP.md`, `docs/project/WREATH-DSN-003_Acceptance_Test_Matrix.md`, `docs/project/WREATH-DSN-003_Dependencies_Blockers.md`, `docs/reference/WREATH-DSN-003_Ticket.md`.

## 1. Purpose and authority

This document is a **draft / proposed** Phase 1 artifact. It is **NOT APPROVED** and does not constitute owner approval, an approved API contract, or authorization to implement. It deliberately avoids finalizing concrete Rust types or external API signatures.

## 2. Manual-placement MVP request and result concepts

- Request concepts (ticket sections 3.3, 3.5): placement target (board identity + footprint-instance identities), operation/mode, constraints, objective settings, deterministic seed, cancellation policy, expected board revision.
- Result concepts: final position and orientation, changed footprint-instance IDs, constraint violations, execution status, commit flag, infeasible diagnostics.
- Labels are **conceptual** per ticket section 3.5, not finalized type names.

## 3. Board and footprint-instance references

- Placement target: board identity + one or more footprint-instance identities.
- Footprint instance reference: stable instance identity; authoritative library-footprint reference/version; pad identities supplied by board/library contracts.
- Board revision: a revision or concurrency token **where supported** (unresolved; ticket section 3.5).

## 4. Position, rotation, board side and fixed-footprint state

- The ticket requires reuse of **approved WREATH coordinate and unit conventions** and forbids introducing a conflicting convention (ticket section 7.1).
- **Boundary correction:** the geometry decisions D1-D10 are recorded at policy level, and **D3 specifically addresses schematic Y-axis direction; it does not decide PCB front/back (board side) placement or PCB coordinate conventions.**
- Consequently, the **PCB position unit, axis direction, rotation sign, transform order, and board-side (front/back) representation remain UNRESOLVED** and must not be assumed here. They are prerequisites to be confirmed before implementation.
- Fixed-footprint state: a fixed/locked flag with allowed orientations and permitted region, treated as a **mandatory constraint** (ticket section 7.2).

## 5. Hard constraints versus soft optimization objectives

- Hard constraints define whether a placement is permitted: fixed footprint cannot move; placement within permitted region; no mandatory keepout intersection; only permitted orientations; mandatory mechanical clearances.
- If a hard constraint cannot be satisfied, the engine must **report infeasibility rather than quietly violating it** (ticket section 7.2).
- Soft objectives (connection-length reduction, grouping, spacing, distribution, congestion, preferred orientation/regions) rank otherwise-feasible placements and **must not override hard constraints** (ticket section 7.3).

## 6. Candidate evaluation, validation, commit and failure handling

- Lifecycle (ticket section 7.5): Current -> Requested -> Candidate -> Evaluated-feasible -> Evaluated-infeasible -> Committed -> Rejected/Cancelled/Failed.
- Candidate generation, scoring and evaluation are **non-mutating** and are kept **separate from committing changes**; only a feasible candidate may be committed. Hard constraints must never be overridden, even if an override is approved elsewhere. An infeasible candidate must be rejected and must never be committed through the normal placement workflow.
- Commit must verify the board revision or equivalent token where supported; a stale candidate must be rejected or re-evaluated, never silently applied.
- Cancellation and errors must leave no partial transforms; a committed result reports changed footprint IDs and the resulting revision when available.

## 7. Structured success, infeasibility and error outcomes

- Success: a structured success outcome.
- Infeasibility: structured diagnostics identifying the failed mandatory constraint or the unavailable required input.
- Errors: shared error taxonomy (F1), typed resolution errors (E4), validation ownership (F2), basic write-then-rename atomicity (F4), unsupported-feature policy (F6), and the MVP diagnostics/reporting approach (F5/F7). No final error variant names or payloads are assumed here.

## 8. Required geometry, connectivity and shared-contract dependencies

- Geometry: board containment, footprint overlap, spacing, keepout and cutout queries must come from the **approved geometry API**; if a required capability is unavailable, record it as a **blocking dependency** rather than building a parallel geometry kernel (ticket sections 3.3, 3.4).
- Connectivity: an authoritative net/pad input contract, including how missing or stale connectivity is reported; the engine must not own schematic connectivity or derive electrical truth from visual proximity.
- Shared contract: resolution decisions E1-E8 and validation/error decisions F1-F7 are recorded at policy level; implementation-level details remain open.

## 9. Deterministic test requirements and acceptance traceability

- Deterministic tests are required for transforms, constraints, persistence integration and failure behavior (MVP step 10); the 30 mandatory scenarios (ticket section 9) and the acceptance matrix (`docs/project/WREATH-DSN-003_Acceptance_Test_Matrix.md`) provide the traceability target.

## 10. Acceptance test matrix review — gaps versus the 30 mandatory scenarios

- The existing matrix covers the 30 mandatory scenarios at a summary level.
- Gaps identified (no matrix changes were made in this task):
  - No per-scenario fixture / expected-result / tolerance mapping.
  - No explicit mapping of lifecycle scenarios (candidate non-mutation, commit rollback, stale revision, cancellation, infeasible commit).
  - No approval-status dimension distinguishing proposed versus approved criteria.
  - No decision-record traceability columns.
  - MVP step-to-test traceability not fully formalized.

## 11. Exclusions and safety preserved

- Ticket exclusions preserved: PCB routing/autorouting, copper zones, full board-outline/cutout editing, complete geometry kernel, standalone DRC, ERC, integrity/thermal simulation, manufacturing output, 3D collision simulation, RL infrastructure, full PCB editor UI, AI-NL generation.
- Workflow safety preserved: no invented approvals, contracts or external APIs; no engine implementation started.

## 12. Unresolved dependencies / open items

- Ticket status Proposed (implementation not approved).
- Open architecture decisions (board placement-state ownership, connectivity input contract, transaction boundary, UI/API boundary, optimization baseline).
- Missing/unverified dependencies (approved board geometry, WHIR/shared contract, feasibility API, footprint data).
- Unresolved PCB coordinate/transform/board-side details (see section 4).
- Proposed performance/quality targets.

## 13. Next step

- Owner approval of this draft proposal is required before Phase 1 implementation proceeds.
