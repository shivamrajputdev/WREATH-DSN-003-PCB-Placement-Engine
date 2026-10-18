# WREATH-DSN-003 — Acceptance Test Matrix

**Status:** DRAFT / PROPOSED — NOT APPROVED
**Source of scenarios:** `docs/reference/WREATH-DSN-003_Ticket.md` section 9 (30 mandatory scenarios).
**Related:** `docs/project/WREATH-DSN-003_Manual_Placement_MVP.md`, `docs/project/WREATH-DSN-003_Contract_Proposal.md`, `docs/project/WREATH-DSN-003_Dependencies_Blockers.md`.
**Evidence baseline:** branch `main` @ `654a1ae`; checkout contains `README.md` and `docs/` only. No test has been written or executed. No PASS is claimed anywhere in this document.

## How to read this matrix

- **Scenario IDs:** `S01`–`S30` are stable criterion IDs mapped 1:1 to ticket section 9 items 1–30. IDs are preserved; do not renumber.
- **Requirement approval status:** no acceptance requirement is currently approved; the ticket is status *Proposed*. Every row is therefore **Proposed (not approved)**.
- **Execution status:** every row is **NOT RUN**. No test has been executed and no PASS is claimed.
- **Fixture / test data:** `TBD` marks a fixture the ticket does not specify. Fixtures are not invented; the wording after `TBD` records only what the fixture must cover.
- **Tolerance:** `TBD` marks an unspecified tolerance/threshold. `N/A (binary outcome)` marks a test whose result is pass/fail and needs no numeric tolerance. Where a numeric comparison is expected but the tolerance is unspecified, the value stays `TBD`.
- **Related MVP step:** references the 10 manual-placement MVP steps (ticket section 3.3). Work outside the MVP (Phase 3 evaluation, Increment E/F automatic placement/optimization) is labelled explicitly and is recorded for traceability only.
- **Dependency IDs:** `DEP-nn` reference the dependency register in `docs/project/WREATH-DSN-003_Dependencies_Blockers.md`.
- **Decision-record traceability (DTR):** cites the policy-level decision records (A/B/C/D/E/F) that inform the requirement. Those records are scoped to DSN-001/DSN-002 (schematic + library); **PCB-placement applicability is unresolved** and is tracked as `DEP-12`. A citation does not mean a decision is approved for PCB placement.

## Scenario matrix (30 scenarios)

| ID | Scenario (ticket section 9) | Requirement / expected behavior | Verification method | Fixture / test data | Expected observable result | Tolerance | Related MVP step / increment | Required dependency or contract | Decision-record traceability (DTR) | Requirement approval status | Execution status | Notes / evidence |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| S01 | 1. Place one footprint at an absolute position | Footprint placed at the exact supplied absolute position | Deterministic unit test | TBD — single footprint at a known absolute board coordinate | Footprint at exact position; no unintended state change | TBD — PCB unit/precision unspecified | MVP 1, 3 | DEP-04, DEP-05, DEP-12 | D1, D2, D6, D8 (schematic scope); B7 | Proposed (not approved) | NOT RUN | Position unit/axis and transform order TBD |
| S02 | 2. Move a footprint relative to a reference point | Offset applied exactly relative to a reference; reference preserved | Unit test (relative move) | TBD — footprint plus an explicit reference point/instance | New position = reference + offset per approved convention | TBD — transform order/precision unspecified | MVP 4 | DEP-04, DEP-05, DEP-12 | D2, D6, D8 (schematic scope); B7 | Proposed (not approved) | NOT RUN | Transform order/quantization TBD |
| S03 | 3. Rotate a footprint through supported angles | Rotation matches approved convention for supported angles | Unit test (rotation) | TBD — asymmetric footprint rotated through the supported angle set | Correct rotated geometry; unsupported angles rejected | TBD — supported angle set and numeric tolerance unspecified | MVP 3, 4 | DEP-04, DEP-12 | D4, D8 (rotation convention is schematic scope) | Proposed (not approved) | NOT RUN | D4 does not decide PCB rotation sign/units; supported angle set TBD |
| S04 | 4. Flip a footprint to the opposite board side | Footprint moved to opposite side with consistent geometry | Side-change test (unit) | TBD — footprint with side-sensitive geometry | Footprint on target side; side semantics correct | TBD — side/mirror geometry tolerance unspecified | MVP 3, 4 | DEP-04, DEP-12 | D5 (mirror flags are schematic scope) | Proposed (not approved) | NOT RUN | D3/D5 do not decide PCB front/back; side semantics TBD |
| S05 | 5. Transform an asymmetric footprint and verify all geometry | All transformed geometry (outline/pins/pads) matches expected | Geometry fixture comparison | TBD — asymmetric footprint with known local outline/pad coordinates and an expected board-coordinate set | Geometry matches expected within tolerance | TBD — quantization/rounding rule unresolved (D8) | MVP 2, 3, 10 | DEP-05, DEP-06, DEP-12 | D2, D5, D6, D7, D8, D9, D10 (policy level) | Proposed (not approved) | NOT RUN | Fixture and tolerance TBD; pad geometry deferred (D7) |
| S06 | 6. Attempt to move a fixed footprint | Fixed footprints cannot move; attempt rejected | Negative test | TBD — footprint in fixed/locked state; move/rotate/flip request | Operation rejected/infeasible; fixed footprint unchanged | N/A (binary outcome) | MVP 5 | DEP-04 | Ticket 3.3, 7.2 | Proposed (not approved) | NOT RUN | Hard constraint; no override permitted (ticket 3.3, 7.2) |
| S07 | 7. Place a footprint fully within the board outline | Containment satisfied | Containment test | TBD — footprint placed fully inside the board outline | Success; no boundary violation | N/A (binary outcome); edge clearance handled by S10 | MVP 6 | DEP-04, DEP-06 | D8 (geometric tolerance kept separate from identity/connectivity) | Proposed (not approved) | NOT RUN | Geometry API dependency open (DEP-06) |
| S08 | 8. Attempt a placement outside the permitted board region | Out-of-region placement rejected | Negative test | TBD — footprint placed outside the permitted region/board outline | Infeasible result with diagnostics; not committed | N/A (binary outcome) | MVP 6, 9 | DEP-06, DEP-09 | F3, F6 (explicit diagnostics, no silent substitution) | Proposed (not approved) | NOT RUN | Permitted-region definition TBD |
| S09 | 9. Test overlapping footprints | Overlap detected and reported per policy | Overlap test | TBD — two footprints with a known geometric intersection | Overlap flagged; hard/soft policy applied | TBD — overlap severity/clearance policy unspecified | MVP 6 | DEP-06 | F1, F5 (structured error categories) | Proposed (not approved) | NOT RUN | Overlap severity policy TBD |
| S10 | 10. Test a footprint near a board edge | Edge-clearance rule applied | Edge-case test | TBD — footprint at a known distance from the board edge | Accepted or rejected per boundary rule | TBD — clearance value unspecified | MVP 6 | DEP-06 | F1 | Proposed (not approved) | NOT RUN | Clearance value/rule TBD |
| S11 | 11. Test board cutouts and nonrectangular boundaries where supported | Correct handling when the API supports it; explicit unsupported diagnostic otherwise | Conditional test | TBD — board with a cutout / nonrectangular boundary | Cutout respected or explicit unsupported result | TBD — cutout geometry tolerance unspecified | MVP 6 | DEP-06 (cutout support availability TBD) | D10, F6 (unsupported features warned, not silently discarded) | Proposed (not approved) | NOT RUN | Conditional on API support; ticket 3.3 step 6 requires a blocker report if unavailable |
| S12 | 12. Test placement keepouts | Keepout intersection rejected | Keepout test | TBD — board with a mandatory keepout region | Infeasible with diagnostics; not committed | N/A (binary outcome) | MVP 6 | DEP-06 | F3, F6 | Proposed (not approved) | NOT RUN | Keepout data ownership/dependency open |
| S13 | 13. Test allowed and forbidden orientations | Only permitted orientations accepted | Constraint test | TBD — footprint with an allowed/forbidden orientation set | Forbidden orientation rejected | N/A (binary outcome) | MVP 5, 6 | DEP-04 | D4 (rotation set policy); F6 | Proposed (not approved) | NOT RUN | Allowed orientation set TBD |
| S14 | 14. Place connected components using connectivity-aware objectives | Connection-length objective influences placement | Connected-components test | TBD — board with known net/pad connectivity and a related component group | Objective reflects connectivity; no electrical truth derived from proximity | TBD — objective term/threshold unspecified | Increment E/F (separate; not manual MVP) | DEP-07, DEP-11 | D8 (proximity is not connectivity); connectivity contract open | Proposed (not approved) | NOT RUN | Out of manual-placement MVP scope; recorded for traceability |
| S15 | 15. Run automatic placement with a subset of fixed components | Auto placement respects fixed components | Auto-placement test | TBD — board with a subset of fixed components and an eligible subset | Fixed parts unmoved; others placed feasibly | TBD — feasibility/quality thresholds unspecified | Increment E (separate) | DEP-07, DEP-11, DEP-14 | D4, B7 | Proposed (not approved) | NOT RUN | Separate increment |
| S16 | 16. Run automatic placement with an infeasible constraint set | Auto placement reports infeasibility | Negative auto test | TBD — infeasible constraint set (e.g. oversubscribed region) | Infeasibility reported; no infeasible commit | N/A (binary outcome) | Increment E (separate) | DEP-06, DEP-11 | F3, F5, F6 | Proposed (not approved) | NOT RUN | Diagnostic content TBD |
| S17 | 17. Run the optimizer twice with the same deterministic seed | Identical result for identical inputs and seed | Determinism test (two runs) | TBD — auto-placement configuration plus an explicit seed | Identical outputs | N/A (identity expected) | Increment F (separate) | DEP-11 | Deterministic seed policy proposed (ticket 3.3, 7.4) — not approved | Proposed (not approved) | NOT RUN | Seed policy TBD |
| S18 | 18. Run the optimizer with different permitted seeds and compare quality | Quality compared across permitted seeds | Multi-seed comparison | TBD — auto-placement configuration plus a permitted seed set | Quality reported per seed | TBD — quality target/baseline unapproved | Increment F (separate) | DEP-11, DEP-14 | Optimization baseline proposed (ticket 8) — not approved | Proposed (not approved) | NOT RUN | Ticket proposes a 10% connection-length reduction target; it is unapproved and not a gate |
| S19 | 19. Test an empty board | No-op or defined result without corruption | Edge-case test | TBD — board with zero footprints | Success or defined empty result; state intact | N/A (binary outcome) | MVP 1, 9 | DEP-04 | B10 (no silent mutation), F5 | Proposed (not approved) | NOT RUN | Expected empty-board behavior TBD |
| S20 | 20. Test a large board with many footprints and connections | Correct operation at scale (correctness plus measured runtime/memory) | Scale test | TBD — large board with documented footprint/net counts | Correct results; runtime/memory recorded | TBD — latency/memory targets unapproved (ticket 8 sets them after baseline) | Not an MVP step — Phase 3 evaluation (performance); MVP 10 only for determinism | DEP-11, DEP-14 | Performance targets proposed (ticket 8) — not approved | Proposed (not approved) | NOT RUN | Object counts and hardware TBD (DEP-14) |
| S21 | 21. Verify that placement operations preserve footprint and pad identities | Identities unchanged across placement operations | Identity round-trip test | TBD — footprint instance and pads with known IDs | Instance and pad IDs preserved | N/A (exact identity) | MVP 1, 2, 10 | DEP-04, DEP-05 | B5, B6, B7, B9, B10 (policy level) | Proposed (not approved) | NOT RUN | Pad identity details deferred until footprint integration (B5/B6) |
| S22 | 22. Verify that failed operations do not corrupt the board state | Failed operations leave authoritative state unchanged | Failure fixtures | TBD — failing operation set (invalid transform, rejected constraint, I/O failure) | State unchanged after failure | N/A (exact state comparison) | MVP 8, 9, 10 | DEP-09 | F4 (write-then-rename, file-save scope only), F6, F7 | Proposed (not approved) | NOT RUN | Transaction boundary open (DEP-09); failure fixtures TBD |
| S23 | 23. Verify candidate generation and evaluation do not mutate authoritative board state | Candidate generation/evaluation are non-mutating | Non-mutation test | TBD — board snapshot plus a candidate request | Authoritative state unchanged; candidate kept separate | N/A (exact state comparison) | MVP 9, 10 | DEP-09 | Lifecycle rule (ticket 7.5): candidate generation/scoring non-mutating | Proposed (not approved) | NOT RUN | Lifecycle test (candidate non-mutation) |
| S24 | 24. Verify infeasible candidates cannot be committed through the normal API | Commit gate rejects infeasible candidates | Negative commit test | TBD — evaluated-infeasible candidate plus a commit request | Commit rejected; no state change | N/A (binary outcome) | MVP 8, 9 | DEP-06, DEP-09 | Lifecycle rule (ticket 7.5); F1, F5, F6 | Proposed (not approved) | NOT RUN | No override path (ticket 7.5) |
| S25 | 25. Verify commit failure rolls back all proposed transforms | Atomic rollback on commit failure | Commit-failure fixture | TBD — injected commit failure (e.g. persistence/revision failure) | No partial transforms; state unchanged | N/A (exact state comparison) | MVP 9, 10 | DEP-09 | F4 (file-write atomicity only; full transaction design deferred) | Proposed (not approved) | NOT RUN | Lifecycle test (commit failure); transaction boundary open |
| S26 | 26. Verify stale board revisions are rejected or re-evaluated where revision tokens are supported | Stale candidate is not silently applied | Stale-revision test | TBD — board revision token plus a candidate generated against an older revision | Rejected or re-evaluated | N/A (binary outcome) | MVP 9 | DEP-08 | Lifecycle rule (ticket 7.5); E8 (single-threaded MVP) | Proposed (not approved) | NOT RUN | Lifecycle test; revision-token support unresolved; conditional on token support |
| S27 | 27. Verify cancellation leaves the board unchanged | Cancellation causes no mutation | Cancellation test | TBD — long-running/interruptible operation plus cancellation | No partial transforms | N/A (exact state comparison) | MVP 9 | DEP-09, DEP-10 | Lifecycle rule (ticket 7.5); A7 (harness/cancellation scope open) | Proposed (not approved) | NOT RUN | Lifecycle test; cancellation policy TBD |
| S28 | 28. Verify API/adapter validation, structured errors, and response status for invalid and infeasible requests | Invalid/infeasible requests return structured errors and status | API contract test | TBD — invalid request set plus infeasible request set | Structured errors and response status | N/A (binary outcome) | MVP 7, 8 | DEP-03, DEP-10 | F1, F2, F5, F7; E1–E4; A7 (canonical Rust API approved; adapter open) | Proposed (not approved) | NOT RUN | Error schema and transport/adapter TBD (DEP-10) |
| S29 | 29. Verify deterministic results include the configuration and seed used | Result records the configuration and seed | Provenance test | TBD — deterministic auto-placement run with a named configuration and seed | Config and seed recorded in result | N/A (exact match) | MVP 10 (determinism) + Increment F (seed) | DEP-11 | Lifecycle/provenance concepts (ticket 3.5); seed policy proposed | Proposed (not approved) | NOT RUN | Deterministic-mode/seed policy TBD |
| S30 | 30. Verify missing geometry or connectivity capabilities produce explicit diagnostics rather than silent assumptions | Missing capability yields explicit diagnostic | Negative capability test | TBD — simulated absence of a required geometry/connectivity capability | Explicit diagnostic returned; no silent assumption | N/A (binary outcome) | MVP 8, 10 | DEP-06, DEP-07 | F3, F5, F6; A5 (WHIR check pending) | Proposed (not approved) | NOT RUN | Implements ticket 3.3 step 6 and 3.4 |

**Count check:** S01–S30 = 30 scenarios, matching ticket section 9 numbering 1–30.

## MVP step to scenario mapping

| MVP step (ticket 3.3) | Relevant scenarios |
|---|---|
| 1. Resolve board + footprint via stable IDs | S01, S02, S19, S21 |
| 2. Read authoritative footprint def + board geometry | S05, S21 |
| 3. Apply exact position + rotation/side transforms | S01, S03, S04, S05 |
| 4. Support move/rotate/flip where supported | S02, S03, S04 |
| 5. Respect fixed footprints + mandatory constraints | S06, S13 |
| 6. Evaluate containment/keepouts/overlap/spacing | S07, S08, S09, S10, S11, S12, S13 |
| 7. Expose via stable engine API + adapter | S28 |
| 8. Return structured success/infeasible/validation/internal-error | S08, S22, S24, S28, S30 |
| 9. Failed/rejected edits leave authoritative state unchanged | S08, S19, S22, S23, S24, S25, S26, S27 |
| 10. Deterministic tests (transforms, constraints, persistence, failure) | S01–S13, S21, S22, S23, S25, S29, S30 |

**Scenarios not mapped to an MVP step:** S14–S18 (automatic placement and optimization — separate increments per the staged-capability rule) and S20 (Phase 3 evaluation/performance). They are retained for ticket traceability only and do not constitute manual-placement MVP obligations.

## Lifecycle tests (ticket section 7.5)

- Candidate non-mutation: **S23**
- Commit failure / rollback: **S25**
- Stale revisions: **S26**
- Cancellation: **S27**
- Infeasible candidates not committed: **S24** (and **S16** for auto placement, a separate increment)
- Failed operations / atomicity: **S22**

## Decision-record traceability summary

| Decision family | Scenarios citing it | Applicability note |
|---|---|---|
| A1–A8 (architecture) | S27, S28, S29, S30 | A5 CONDITIONAL, A7 PARTIAL, A8 OPEN; no PCB-placement-specific architecture decision exists |
| B1–B10 (identity) | S01, S02, S06, S15, S19, S21 | DECIDED at policy level; B5/B6 detail deferred until footprint integration |
| C1–C9 (definitions/revisions) | (none directly) | Informational for DEP-02/DEP-05; the placement engine does not own library definitions |
| D1–D10 (geometry/transform) | S01–S05, S07, S11, S13, S14, S15 | DECIDED at policy level, but D3/D4/D5 are schematic scope; PCB applicability unresolved (DEP-12) |
| E1–E8 (resolution/API) | S26, S28 | DECIDED at policy level for library resolution; placement commit/API boundary not covered |
| F1–F7 (validation/errors) | S08, S09, S10, S11, S12, S16, S19, S22, S24, S25, S28, S30 | DECIDED at policy level; F4 covers file-save atomicity only, not placement commit |

## Dependencies that block a test from being implemented

- Approved geometry and feasibility API (containment/overlap/clearance/keepout/cutout): blocks S07–S12, S16, S24, S30 (`DEP-06`).
- Connectivity input contract: blocks S14, S15, S30 (`DEP-07`).
- Board revision token: blocks S26 (`DEP-08`).
- Persistence and transaction/commit boundary: blocks S22, S23, S24, S25, S27 (`DEP-09`).
- UI/API boundary and error mapping: blocks S27, S28 (`DEP-10`).
- PCB coordinate/transform/board-side conventions: block S01–S05, S29 (`DEP-12`).
- Board/footprint model and identity decisions: block S01, S02, S04, S06, S13, S19, S21 (`DEP-04`, `DEP-05`).
- Benchmark and acceptance fixtures, plus the benchmark dataset: block S20 and completion of fixture/tolerance cells for all scenarios (`DEP-14`).
- Optimization baseline, thresholds, and seed policy: block S14–S18, S20, S29 as gates (`DEP-11`).
- Shared contract/WHIR authority and the API error taxonomy surface: block S28 (`DEP-03`).

## Remaining matrix gaps (cannot be filled without owner input)

The following gaps were identified and remain open. They are recorded rather than guessed, because resolving them requires owner/architecture input:

1. **Fixtures:** every `Fixture / test data` cell is `TBD`; the ticket does not specify reference boards, expected-coordinate sets, or object counts.
2. **Tolerances and thresholds:** every numeric `Tolerance` cell remains `TBD`; no tolerance, clearance, overlap severity, runtime, or quality threshold is approved.
3. **PCB-specific conventions:** rotation sign/units, board-side representation, coordinate axis/unit, and transform order for PCB placement are unresolved (`DEP-12`).
4. **Lifecycle dependencies:** board revision token and the placement commit/transaction boundary are unresolved (`DEP-08`, `DEP-09`).
5. **Geometry and connectivity availability:** cutout/keepout/clearance capability and the connectivity input contract are unverified (`DEP-06`, `DEP-07`).
6. **Entry gates:** the ticket remains *Proposed*; no acceptance criterion in this matrix is approved, so no row can move beyond NOT RUN.

## Scope exclusions preserved

- Ticket scope exclusions are preserved: PCB routing/autorouting, copper zones, full board-outline/cutout editing, complete geometry kernel, standalone DRC, ERC, integrity/thermal simulation, manufacturing output, 3D collision simulation, RL infrastructure, full PCB editor UI, AI-NL generation.
- Automatic-placement and optimizer scenarios (S14–S18) are recorded for traceability but belong to a separate increment, not the manual-placement MVP.
- No engine code, API, fixture, tolerance, or approval is introduced by this matrix.
