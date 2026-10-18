# WREATH-DSN-003 — Native Placement Feature Specification

**Status:** DRAFT / PROPOSED — NOT APPROVED
**Phase 0 deliverable:** Native placement feature specification (ticket section 5, Phase 0).
**Scope:** Ticket section 3.1 included functionality A–G, bounded by the ticket section 3.2 exclusions and staged by the ticket section 3.3 staged capability rule.
**Evidence baseline:** branch `main` @ `654a1ae` ("Initial commit"); checkout contains `README.md` and `docs/` (untracked) only. No engine source, tests, contracts, CI, or configuration is present.
**Related:** `docs/reference/WREATH-DSN-003_Ticket.md`, `docs/project/WREATH-DSN-003_Manual_Placement_MVP.md`, `docs/project/WREATH-DSN-003_Contract_Proposal.md`, `docs/project/WREATH-DSN-003_Implementation_Plan.md`, `docs/project/WREATH-DSN-003_Reference_Behavior_Mapping.md`, `docs/project/WREATH-DSN-003_Dependencies_Blockers.md`, `docs/project/WREATH-DSN-003_Acceptance_Test_Matrix.md`, `docs/project/WREATH-DSN-003_Benchmark_Dataset_Manifest.md`.

## 1. Purpose and authority

This document extends the native placement feature specification **beyond the manual-placement MVP**, but only where the ticket provides sufficient requirements to do so. It is a **draft / proposed** Phase 0 artifact. It is **NOT APPROVED** and authorizes no implementation.

Boundaries observed:

- The manual-placement MVP slice is specified in `WREATH-DSN-003_Manual_Placement_MVP.md` (ticket 3.3, 10 steps). That slice is **referenced, not repeated** here.
- The Phase 1 request/result, constraint, lifecycle, and error concepts are in `WREATH-DSN-003_Contract_Proposal.md`. Those concepts are **referenced, not re-defined** here.
- Where the ticket does not give a requirement, the detail is marked `TBD` or `UNRESOLVED` rather than invented.
- Excluded functionality (ticket 3.2) is not specified here at all.

## 2. How to read

- **TICKET-REQUIRED** — stated in ticket section 3.1, 3.3, or section 8. The requirement itself is not a proposal.
- **PROPOSED** — this document's staging or scoping choice; not approved.
- **UNRESOLVED / TBD** — the ticket requires it but the supporting decision, contract, or data does not exist yet.
- **OUT OF SCOPE** — ticket section 3.2 exclusions.
- **MVP** — part of the first accepted vertical slice (ticket 3.3).
- **LATER INCREMENT** — required for the complete ticket, staged after the manual slice (ticket 3.3 staged capability rule).
- Dependency IDs (`DEP-01`–`DEP-14`) are defined in `WREATH-DSN-003_Dependencies_Blockers.md`. Scenario IDs (`S01`–`S30`) are the ticket section 9 scenarios traced in `WREATH-DSN-003_Acceptance_Test_Matrix.md`.

## 3. Capability map and staging

Staging is this document's proposal. Only the ticket 3.3 rule is TICKET-REQUIRED: the manual slice is first; basic deterministic automatic placement is next after the manual slice and its contracts are accepted; optimization, configurable multi-term objectives, and benchmark targets must not block the first slice but remain required milestones; no global optimality is claimed.

| Capability (ticket 3.1) | Staging (PROPOSED) | Implementation-plan increment | Primary dependency | Related scenarios |
|---|---|---|---|---|
| A. Footprint placement operations | MVP (ticket 3.3 steps 3–4) | A (transforms), B (manual ops) | `DEP-04`, `DEP-12` | S01–S06, S19, S21 |
| B. Manual placement support | MVP (3.3 steps 3–5); align/distribute later | B (manual ops), D (fixed/constraints) | `DEP-04`, `DEP-12` | S01–S06, S13, S15, S19, S21 |
| C. Geometry-aware placement | MVP subset (3.3 step 6) | C (geometry feasibility) | `DEP-06` | S07–S12, S16, S24, S30 |
| D. Connectivity-aware placement | LATER INCREMENT (with E/F); "preserve logical connectivity" is inherent to all transforms | E (basic auto), F (optimization) | `DEP-07` | S14, S15, S21, S30 |
| E. Basic automatic placement | LATER INCREMENT (next after MVP per 3.3) | E (basic auto) | `DEP-07`, `DEP-11`, `DEP-12` | S14–S18 |
| F. Placement evaluation | PARTLY MVP (structured results + deterministic tests, 3.3 steps 8, 10); full metrics later | F (optimization/evaluation) | `DEP-11`, `DEP-14` | S08, S16–S18, S20, S24, S28–S30 |
| G. Native integration | MVP subset (3.3 steps 1, 2, 7, 9) | G (native integration) | `DEP-01`, `DEP-02`, `DEP-03`, `DEP-05`, `DEP-09`, `DEP-10` | S05, S21, S22–S28, S30 |

Increment letters above follow `WREATH-DSN-003_Implementation_Plan.md` (Phase 2 increments A–G). They are **not** the same letters as the ticket scope headings A–G.

## 4. Capability detail beyond the MVP

Each entry below states the ticket requirement, what the MVP already covers (by reference), and what this specification adds beyond the MVP. Details that the ticket does not determine are marked `TBD`/`UNRESOLVED`.

### A. Footprint placement operations (ticket 3.1A)

- **TICKET-REQUIRED:** add an instance to the board; move to an absolute position; move relative to a reference point; rotate; flip between supported board sides; apply exact coordinate/orientation changes; remove or reposition a footprint through approved board operations.
- **MVP coverage (reference):** `WREATH-DSN-003_Manual_Placement_MVP.md` steps 3–5 cover exact position, rotation, side, and move/rotate/flip where the board/footprint model supports the operation.
- **Beyond MVP (PROPOSED):**
  - **Add instance:** staged with G, because it depends on the authoritative footprint-instance and board model (`DEP-04`, `DEP-05`). Scenario S19 (empty board) is the trace.
  - **Reference-relative move:** MVP-adjacent; included in increment B. Reference-point semantics (which origin: footprint anchor, local origin, or a design origin) are `UNRESOLVED` — `DEP-12`.
  - **Remove/reposition:** included in increment B but `UNRESOLVED` at the model level; the review located no footprint-removal representation or WREATH board-operation interface (`DEP-04`).
- **Unresolved / TBD:** PCB unit, axis, rotation sign, board-side representation, transform order, and tolerance (`DEP-12`); board-operation ownership (`DEP-04`).
- **Evidence needed:** the `DEP-12` convention decision and the `DEP-04` ownership decision.

### B. Manual placement support (ticket 3.1B)

- **TICKET-REQUIRED:** place components using coordinates and orientation supplied by the UI; snapping to a configurable grid where appropriate; move selected footprints; align and distribute multiple footprints; support fixed or locked footprints; preserve the designer's explicit placement decisions.
- **MVP coverage (reference):** steps 3–5 cover coordinate/orientation placement and fixed footprints. Fixed/locked handling is a mandatory constraint (ticket 7.2).
- **Beyond MVP (PROPOSED):**
  - **Selection/batch operations:** move-selected, align, and distribute are staged in increment B. ⚠ `UNRESOLVED`: the Phase 1 contract proposal does not enumerate align/distribute, and no acceptance scenario covers them (coverage gap noted in the reference mapping and the matrix).
  - **Grid snapping:** the engine-vs-UI boundary is `UNRESOLVED`; the reference study records grid origin as persisted board state, which is a model question, not a settled requirement (D8 defers grid/snap).
  - **Preserve designer decisions:** in MVP as a constraint; the ticket 7.5 override policy is `UNRESOLVED` (no implicit override permitted).
- **Unresolved / TBD:** selection semantics; align/distribute scope; grid ownership; override policy.
- **Evidence needed:** owner scope decision for align/distribute and grid; `DEP-04`.

### C. Geometry-aware placement (ticket 3.1C)

- **TICKET-REQUIRED:** evaluate footprint outlines and relevant physical geometry; check board-outline containment; respect keepouts and approved mechanical constraints; detect overlap and minimum-spacing violations; support cutouts and nonrectangular outlines when the geometry API supports them; handle front-side and back-side placement.
- **MVP coverage (reference):** MVP step 6 covers containment, keepouts, overlap, and required spacing through the approved geometry API, with a blocking dependency if a capability is unavailable.
- **Beyond MVP (PROPOSED):** extended geometry queries (cutouts, nonrectangular outlines, additional mechanical constraints) are increment C and gated entirely on `DEP-06`. If a query is unsupported, the ticket requires a recorded blocker or explicit limitation, not a parallel geometry kernel (ticket 3.3 step 6, 3.4).
- **Unresolved / TBD:** geometry API identity and supported query set (`DEP-06`); edge-clearance rule for footprints (`TBD`); overlap/keepout enforcement mechanism (reject vs report) — the reference study shows reference behaviour differs from ticket 7.2 and the difference is unresolved.
- **Evidence needed:** the `DEP-06` geometry API confirmation and a WREATH enforcement-semantics decision.

### D. Connectivity-aware placement (ticket 3.1D)

- **TICKET-REQUIRED:** read board connectivity; use connection relationships to guide automatic placement; calculate objectives from estimated connection lengths; allow critical components and related component groups to influence placement; preserve logical connectivity when a footprint changes position or orientation.
- **MVP coverage (reference):** connectivity is **not** in the 10 MVP steps. The invariant that transforms must not alter logical connectivity applies throughout, but the engine owns no connectivity data.
- **Beyond MVP (PROPOSED):** read-connectivity, connection-length objectives, and critical-group influence are increment D, coupled to E/F. All are gated on `DEP-07`.
- **Unresolved / TBD:** connectivity input contract and stale/missing behaviour (`DEP-07`); definition of "critical components and related groups" (`TBD`; the reference study found no reference scoring term for it); estimated-connection-length definition (`UNRESOLVED`, tied to `DEP-11`); whether move-like (leave routing) or drag-like (maintain connections) semantics are wanted.
- **Evidence needed:** the `DEP-07` connectivity contract; a WREATH definition for related-group influence.

### E. Basic automatic placement (ticket 3.1E)

- **TICKET-REQUIRED:** generate an initial placement for unplaced footprints; support placement of selected footprints and all eligible footprints; respect fixed components and hard constraints; deterministic, reproducible optimization for a given configuration and seed; configurable objective weights; report components that cannot be placed feasibly.
- **MVP coverage (reference):** none — this is explicitly the next increment after the manual slice (ticket 3.3).
- **Beyond MVP (PROPOSED):** increment E implements a basic deterministic placer for selected and all eligible footprints. Optimization quality (multi-term objectives, benchmark targets) is deferred to increment F and must not block the manual slice.
- **Unresolved / TBD:** seed policy, algorithm, repetitions, and stopping criteria (`DEP-11`); baseline dataset (`DEP-11`, `DEP-14`); how "unplaced" and "eligible" footprints are determined (`TBD`, depends on `DEP-04`/`DEP-05`).
- **Required invariant (TICKET-REQUIRED):** the placer respects fixed components and hard constraints; an infeasible component is reported, never placed by weakening a constraint; no global optimality is claimed.
- **Evidence needed:** approved baseline and seed policy; footprint-instance model.

### F. Placement evaluation (ticket 3.1F, section 8)

- **TICKET-REQUIRED:** measure estimated connection length; measure overlap and clearance violations; measure board-boundary violations; measure component distribution and density; record optimization progress and final objective values; provide structured diagnostics for infeasible placements.
- **MVP coverage (reference):** MVP step 8 returns structured success/infeasible/validation/internal-error results, and step 10 requires deterministic tests. Full metric measurement is not in the MVP.
- **Beyond MVP (PROPOSED):** increment F implements the ticket section 8 measurement set. Metric definitions and thresholds are `PROPOSED` only; the ticket states thresholds require approval after a baseline dataset is established. Each metric is reported separately; a lower connection-length objective is explicitly **not** accepted as proof of routability or thermal quality (ticket 8).
- **Unresolved / TBD:** metric definitions and normalisation (`TBD`); threshold approval (`DEP-11`, `DEP-14`); diagnostic payloads (`F5`/`F1` open, `DEP-03`, `DEP-10`).
- **Evidence needed:** benchmark dataset with measured baselines (see `WREATH-DSN-003_Benchmark_Dataset_Manifest.md`); approved metric definitions.

### G. Native integration (ticket 3.1G)

- **TICKET-REQUIRED:** read component/footprint data from the approved library engine; operate on the shared native board and geometry representation; expose stable APIs for interactive and automated placement; support transactional or otherwise recoverable placement changes per the existing architecture.
- **MVP coverage (reference):** MVP steps 1, 2, 7, and 9 cover stable identifiers, reading authoritative definitions through approved interfaces, exposing a stable engine API plus adapter, and leaving authoritative state unchanged on rejection.
- **Beyond MVP (PROPOSED):** increment G completes integration: transactional/recoverable commit through the board/document owner, revision handling, and the shared error/contract surface.
- **Unresolved / TBD:** board/placement-state ownership (`DEP-04`); DSN-001/DSN-002 interfaces (`DEP-01`, `DEP-02`); shared contract / WHIR authority (`DEP-03`, A5 CONDITIONAL); persistence and transaction boundary (`DEP-09`); UI/API adapter and error mapping (`DEP-10`); board revision token (`DEP-08`).
- **Required invariant (TICKET-REQUIRED):** the engine must not maintain a competing authoritative board state and must not silently take ownership of geometry, connectivity, constraints, or diagnostics supplied by other components (ticket 3.2, 3.4).
- **Evidence needed:** the interface confirmations listed above.

## 5. Exclusions preserved (ticket 3.2)

OUT OF SCOPE for this ticket: PCB routing/autorouting; copper-zone generation; full board-outline and cutout editing; the complete geometry kernel; DRC as an independent validation engine; ERC; signal/power-integrity simulation; thermal simulation; manufacturing output generation; full 3D mechanical collision simulation; reinforcement-learning training infrastructure; a complete PCB editor UI; AI-based natural-language design generation. The placement engine may consume geometry, connectivity, constraints, and diagnostics supplied by other components but must not take ownership of them.

## 6. Traceability to the migration phases

- **Phase 0 (this document):** feature specification, dependencies, licensing, benchmark manifest, architecture proposal.
- **Phase 1:** request/result model, operations list, coordinate/transform spec, constraints spec, objective spec, error schema (`WREATH-DSN-003_Contract_Proposal.md`).
- **Phase 2:** increments A–G in the order in section 3 (`WREATH-DSN-003_Implementation_Plan.md`).
- **Phase 3:** evaluation and parity testing; consumes the benchmark dataset.
- **Phase 4:** integration and acceptance.

## 7. Unresolved items and next step

- All PCB convention questions (`DEP-12`), board/placement-state ownership (`DEP-04`), geometry API (`DEP-06`), connectivity contract (`DEP-07`), revision token (`DEP-08`), transaction boundary (`DEP-09`), adapter/error mapping (`DEP-10`), and optimization baseline (`DEP-11`) remain open; see `WREATH-DSN-003_Dependencies_Blockers.md`.
- **Status:** DRAFT / PROPOSED — NOT APPROVED. Owner approval is required before this specification constrains Phase 1 work.
- **Next step:** resolve `DEP-12` and `DEP-04`, then confirm the `DEP-06`/`DEP-07` inputs; those five decisions unblock the manual slice and the increment-D/E/F specifications.
