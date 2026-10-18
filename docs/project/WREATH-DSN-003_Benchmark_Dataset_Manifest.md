# WREATH-DSN-003 — Initial Benchmark Dataset Manifest

**Status:** DRAFT / PROPOSED — NOT APPROVED
**Phase 0 deliverable:** Initial benchmark dataset (ticket section 5, Phase 0; task 9 "Establish baseline test boards and reference placements").
**Evidence baseline:** branch `main` @ `654a1ae` ("Initial commit"); checkout contains `README.md` and `docs/` (untracked) only. **No engine, no test harness, no fixtures, and no recorded results exist in this repository.**
**Related:** `docs/reference/WREATH-DSN-003_Ticket.md`, `docs/project/WREATH-DSN-003_Native_Placement_Feature_Spec.md`, `docs/project/WREATH-DSN-003_Dependencies_Blockers.md`, `docs/project/WREATH-DSN-003_Acceptance_Test_Matrix.md`, `docs/project/WREATH-DSN-003_Reference_Behavior_Mapping.md`.

## 1. Critical status statement

**There are no measured baseline results.** Nothing in this document is a measurement, a benchmark outcome, or evidence of engine behaviour. Every board, count, tolerance, and target below is either:

- **TICKET-REQUIRED** — the ticket says such an item must exist (for example "use small, medium, and large benchmark boards with documented object counts and hardware", ticket section 8); or
- **PROPOSED** — a placeholder fixture or metric definition proposed by this manifest; and
- **TBD** — a value that cannot be filled until a real board corpus and a working engine exist.

The proposed target in ticket section 8 (at least a 10% reduction in the approved connection-length objective versus a baseline) is a **proposed target, not a measured baseline**, and must be validated for feasibility and approved before it becomes an acceptance gate. No baseline against which to measure it has been established.

## 2. How to read

- **FIXTURE (PROPOSED)** — a test board the manifest proposes. Not created. Not validated. Not approved.
- **MEASURED BASELINE (NONE)** — no measured value exists anywhere in this repository.
- **TBD** — value undetermined and not to be invented.
- **UNRESOLVED** — depends on an open decision or dependency.
- Dependency IDs (`DEP-01`–`DEP-14`) are defined in `WREATH-DSN-003_Dependencies_Blockers.md`. Scenario IDs (`S01`–`S30`) are the ticket section 9 scenarios.

## 3. Proposed board corpus (all FIXTURE (PROPOSED), all counts TBD)

The ticket requires small, medium, and large benchmark boards with documented object counts (section 8). The manifest proposes the classes below; every concrete value is `TBD` until a real corpus is built and its provenance is cleared (`DEP-13`).

| Board ID | Class | Purpose | Proposed object count | Proposed outline characteristics | Proposed placement constants | Data source / provenance | Status |
|---|---|---|---|---|---|---|---|
| BM-S1 | Small | Single-capability correctness (transforms, constraints) | TBD (small) | TBD — whether rectangular is sufficient for this class is UNRESOLVED | TBD | PROPOSED to be created by WREATH; provenance/licence TBD (`DEP-13`) | FIXTURE (PROPOSED) — not created |
| BM-M1 | Medium | Manual-placement integration and geometry feasibility | TBD (medium) | TBD — to exercise at least one nonrectangular or cutout case if `DEP-06` supports it | TBD | PROPOSED; provenance/licence TBD | FIXTURE (PROPOSED) — not created |
| BM-L1 | Large | Scale, density, and memory growth (S20) | TBD (large) | TBD | TBD | PROPOSED; provenance/licence TBD | FIXTURE (PROPOSED) — not created |
| BM-OPT1 | Optimization | Connection-length baseline for the ticket 8 target | TBD | TBD | TBD | PROPOSED; provenance/licence TBD | FIXTURE (PROPOSED) — not created |

Proposed per-fixture attributes that must be recorded before a fixture is usable for acceptance (ticket section 9): expected result, tolerance, constraint outcome, and failure behaviour. **All four are TBD for every board above.**

## 4. Proposed fixture characteristics to capture

For each board, the ticket-aligned record must eventually include (all values currently TBD):

- Object (footprint) count and instance identifiers.
- Board outline form and, where used, cutouts (ticket 3.1C).
- Fixed/locked footprints and their count.
- Keepout regions and mechanical constraints (ticket 3.1C, 7.2).
- Permitted orientations per component (ticket 7.2).
- Board side(s) used (front/back) (ticket 3.1C).
- Connectivity input (nets/pads) and whether it is complete or stale (`DEP-07`).
- Expected result, tolerance, constraint outcome, and failure behaviour (ticket section 9).
- A reference placement, if the fixture defines a comparand (ticket section 5, task 9).

## 5. Proposed metric set (definitions TBD, targets PROPOSED)

Metric names come from ticket section 8. Definitions, units, and normalisation are `TBD` and are a `DEP-11`/`DEP-14` dependency. No metric has a measured baseline.

| Metric | Ticket ref | Unit | Definition status | Measured baseline | Target |
|---|---|---|---|---|---|
| Total estimated connection length | 8, 3.1F | TBD | TBD | NONE | PROPOSED: >=10% reduction vs baseline (ticket 8); baseline does not exist |
| Constraint violations (count and severity) | 8, 3.1F | TBD | TBD | NONE | TICKET-REQUIRED: zero violations in accepted placements |
| Footprint overlap count | 8, 3.1F | TBD | TBD | NONE | TBD after baseline |
| Board-boundary violations | 8, 3.1F | TBD | TBD | NONE | TICKET-REQUIRED: zero in accepted placements |
| Placement density and distribution | 8, 3.1F | TBD | TBD | NONE | TBD after baseline |
| Footprints successfully placed | 8, 3.1E | TBD | TBD | NONE | TBD after baseline |
| Optimization runtime | 8 | TBD | TBD | NONE | Set after baseline (ticket 8) |
| Objective improvement (initial to final candidate) | 8, 3.1F | TBD | TBD | NONE | TBD after baseline |
| Single-footprint move/rotation latency | 8 | TBD | TBD | NONE | Set after baseline |
| Geometry-feasibility query latency | 8 | TBD | TBD | NONE | Set after baseline |
| Placement-evaluation latency | 8 | TBD | TBD | NONE | Set after baseline |
| Initial placement runtime | 8 | TBD | TBD | NONE | Set after baseline |
| Peak memory vs footprint count | 8 | TBD | TBD | NONE | TBD after baseline |

The ticket states a lower connection-length objective does **not** by itself prove better routability or thermal behaviour, and that each metric must be reported separately. No combined or derived score is proposed here.

## 6. Proposed functional-correctness checks (ticket section 8)

These are TICKET-REQUIRED checks; the fixtures that exercise them do not yet exist.

| Check | Ticket target | Fixture status |
|---|---|---|
| Mandatory unit and integration tests | 100% passing | Fixture TBD |
| Transform correctness | 100% on approved geometry fixtures | Fixtures TBD (`DEP-12`) |
| Fixed-footprint preservation | 100% | Fixture TBD (`DEP-04`) |
| Hard-constraint compliance | Zero violations in accepted placements | Fixture TBD (`DEP-06`) |
| Invalid-operation handling | All mandatory invalid-input tests pass | Fixture TBD |
| Failed-operation atomicity | 100% of approved failure fixtures leave authoritative state unchanged | Fixtures TBD (`DEP-09`) |
| Hard-constraint gate | No infeasible candidate committed through the normal API | Fixture TBD |
| Deterministic repeatability | Identical result for identical inputs and seed where deterministic mode is specified | Fixture TBD (`DEP-11`) |

## 7. Determinism, seed, hardware, and repetitions (all PROPOSED / TBD)

- **Seed policy:** TBD; proposed only (`DEP-11`). No determinism mechanism was located in the reference study, so this is a WREATH-specific requirement.
- **Repetitions and stopping criteria:** TBD; must be approved with the baseline (ticket 3.4, 8).
- **Hardware and environment:** TBD; the ticket requires documented hardware before latency targets are set (section 8).
- **Reference placements:** TBD; the ticket Phase 0 task 9 requires them, but no reference placement exists.

## 8. Provenance and licensing of benchmark data

- No board, footprint, netlist, or reference placement has been created, imported, or measured.
- Provenance is `TBD` for every proposed fixture. If any fixture is derived from an external source, its licence and reuse terms must be reviewed before use (`DEP-13`; see the licensing/provenance section of `WREATH-DSN-003_Dependencies_Blockers.md`).
- **No legal conclusion is drawn here.** The manifest proposes that fixtures be created by WREATH or sourced only after a recorded provenance review.

## 9. What must happen before any number here becomes acceptance evidence

1. Create or import the boards and record object counts and characteristics (Phase 1/2).
2. Confirm the geometry/connectivity inputs (`DEP-06`, `DEP-07`) and PCB conventions (`DEP-12`).
3. Run the engine and record **measured** baselines (currently NONE).
4. Approve metric definitions, thresholds, seed policy, and repetitions (`DEP-11`, `DEP-14`).
5. Record fixture provenance and licence status (`DEP-13`).

**Status:** DRAFT / PROPOSED — NOT APPROVED. **Phase 0 exit criteria are not met by this manifest**; it records a dataset plan, not a dataset.
