# WREATH-DSN-003 — Dependencies, Blockers, and Phase 0 Deliverable Status

**Status:** DRAFT — evidence-based; no approval is claimed or invented.
**Evidence baseline:** branch `main` @ `654a1ae` ("Initial commit"); checkout contains `README.md` and `docs/` (untracked) only. No engine source, tests, contracts, CI, or configuration is present.
**Related:** `docs/project/WREATH-DSN-003_Acceptance_Test_Matrix.md`, `docs/project/WREATH-DSN-003_Phase0_Audit.md`, `docs/project/WREATH-DSN-003_Contract_Proposal.md`, `docs/project/WREATH-DSN-003_Manual_Placement_MVP.md`, `docs/project/WREATH-DSN-003_Native_Placement_Feature_Spec.md`, `docs/project/WREATH-DSN-003_Reference_Behavior_Mapping.md`, `docs/project/WREATH-DSN-003_Benchmark_Dataset_Manifest.md`.

## Dependencies (ticket dependency list, retained)

- WREATH-DSN-001 / WREATH-DSN-002 interfaces.
- Approved board geometry and footprint data.
- Approved WHIR / shared contract.
- Geometry feasibility API (containment, overlap, clearance, keepouts, cutouts).
- Connectivity input contract (nets/pads; missing/stale handling).
- Board revision token / transaction boundary.
- UI/API boundary and error mapping.
- Optimization baseline and thresholds.

## Status handling (retained and extended)

- Distinguish MISSING, NOT INSPECTED, UNVERIFIED, and BLOCKED; repo absence does not mean missing for WREATH.

Extended vocabulary used below, consistent with the workflow handover status terms:

- **ABSENT FROM THIS REPO:** positively observed to be absent from this checkout only. It is *not* a claim about the wider WREATH program.
- **WIDER PROJECT: UNVERIFIED:** the audit has no evidence either way about the wider WREATH program. This is the default for anything referenced only by the ticket or handover documents.
- **WIDER PROJECT: MISSING:** reserved for positive evidence that the dependency does not exist in the wider WREATH program. No dependency in this register currently meets that bar.
- **PARTIAL:** some aspects are decided or present; stated details remain open.
- **BLOCKED:** dependent work cannot safely proceed until a named decision or dependency is resolved.

### Repo absence versus wider-project gap

This repository is a new, documentation-only checkout at a single commit. Consequently:

- An item **absent from this repo** may still exist in the wider WREATH program (for example the DSN-001/DSN-002 engines, a shared-contract crate, or board geometry) and must not be reported as missing.
- An item that is a **decision** rather than an artifact (for example board placement-state ownership) is genuinely open for DSN-003 regardless of repo contents, because none of the recorded A/B/C/D/E/F decision records cover PCB placement.
- This audit found **no dependency that is provably missing from the wider WREATH program**; every wider-project entry is UNVERIFIED and requires owner confirmation.

## Dependency register

Status columns separate what this checkout proves from what remains unverified in the wider project.

| ID | Dependency | Needed for | Status in this repo | Status in wider WREATH project | Evidence / source | Blocks (scenarios) | Owner / required confirmation |
|---|---|---|---|---|---|---|---|
| DEP-01 | WREATH-DSN-001 schematic engine public interfaces | Shared interface conventions the ticket depends on (ticket header, 3.4) | ABSENT (no source) | UNVERIFIED | Checkout contains only `README.md` + `docs/`; `git log` shows one commit; ticket and handover reference DSN-001 but no interface artifact is present | Engine integration; not the 30 scenarios directly | Project lead — confirm canonical DSN-001 revision and public interfaces |
| DEP-02 | WREATH-DSN-002 library engine public interfaces | Authoritative footprint/pad definitions and resolution contract | ABSENT | UNVERIFIED | Same as DEP-01; E1–E8 record library-resolution policy only | S05, S21 and integration | Project lead — confirm canonical DSN-002 revision and resolution interface |
| DEP-03 | Authoritative WHIR / shared contract, or an approved minimal shared contract | Shared identity, geometry, and error semantics across engines | ABSENT | UNVERIFIED — A5 direction is CONDITIONAL and the WHIR evidence check is PENDING | A5 record: conditional Option 2 approved 2026-10-13; the absence check was not completed; Phase 0 audit lists WHIR contract as missing/unverified | S28 (shared error/contract surface); all contract-level tests | Project lead — satisfy the A5 condition (record the WHIR search scope/result) |
| DEP-04 | Authoritative board model and board/placement-state ownership (position, orientation, side, lock state, revision) | Anchor for the placement target and transforms; prevents competing authoritative state | ABSENT | UNVERIFIED — this is an OPEN ticket decision, not an artifact | Ticket 3.4 requires this decision before dependent implementation; A–F records cover DSN-001/DSN-002 only, so PCB placement-state ownership is undecided | S01, S02, S04, S06, S13, S19, S21 | Project lead — record the board/placement-state owner |
| DEP-05 | Authoritative footprint definition and footprint-instance model, including pad identities | Reading footprint geometry; preserving identities | ABSENT | PARTIAL (policy only) — B5 footprint identity DECIDED (detail deferred), B6 pad identity DECIDED (detail deferred), B7 instance identity DECIDED, B9/B10 DECIDED | B decision records, recorded 2026-10-13; no PCB footprint-instance model artifact present | S05, S21 | DSN-002 / board owner — define PCB footprint-instance and pad-identity details (B5/B6 follow-ups) |
| DEP-06 | Approved geometry and feasibility API: board containment, overlap, clearance, keepout, cutout, outline | Feasibility evaluation; the ticket requires an external API, not a parallel geometry kernel | ABSENT | UNVERIFIED — D1–D10 are DECIDED at policy level, but no feasibility API or geometry-kernel evidence exists | Ticket 3.3 step 6 and 3.4; geometry decision record D1–D10 (policy decisions, schematic/library scope) | S07, S08, S09, S10, S11, S12, S16, S24, S30 | Geometry / shared-contract owner — identify the authoritative API and its supported queries |
| DEP-07 | Connectivity input contract (nets/pads; missing/stale reporting) | Connectivity-aware placement; forbids deriving electrical truth from proximity | ABSENT | UNVERIFIED — OPEN for placement | Ticket 3.4; D8 note that proximity is not electrical connectivity | S14, S15, S30 | Project lead — define the connectivity input contract and stale/missing behavior |
| DEP-08 | Board revision token / concurrency token | Rejecting or re-evaluating stale candidates | ABSENT | UNVERIFIED — OPEN; E8 selects a single-threaded MVP with no concurrency promise | Ticket 3.4, 3.5, 7.5; E8 record; ticket qualifies this as "where revision tokens are supported" | S26 | Project lead — decide whether a revision token is supported for the MVP |
| DEP-09 | Persistence and placement commit/transaction boundary | Atomic commit, rollback, cancellation, and non-mutating candidates | ABSENT | PARTIAL — F4 DECIDED covers basic write-then-rename for the file-save path only; the placement commit/rollback boundary is OPEN | Ticket 3.4, 7.5; F4 record and its stated limitations | S22, S23, S24, S25, S27 | Board/document owner — define commit, rollback, cancellation, and stale-revision behavior |
| DEP-10 | UI/API boundary, adapter/transport, and error mapping | Exposing placement through a stable engine API and a UI/automation-facing adapter | ABSENT | PARTIAL — A7 canonical Rust API APPROVED (locked); Python/other bridge OPEN; F1/F2/F5/F7 DECIDED at policy level; no placement-facing endpoint decision | A7 record; F1–F7 record; ticket 3.4 | S27, S28 | Project lead — decide adapter/transport and placement error mapping |
| DEP-11 | Optimization baseline, benchmark set, thresholds, and seed policy | Automatic placement and optimizer release gates | ABSENT | PROPOSED only — ticket 8 targets require validation and approval before becoming gates | Ticket 3.3 staged rule and 8; no baseline dataset or threshold record found | S14, S15, S16, S17, S18, S20, S29 | Project lead — approve boards, hardware, metrics, seed policy, and thresholds |
| DEP-12 | PCB coordinate, unit, rotation-sign, board-side, transform-order, and tolerance conventions | Correct and reproducible transforms | ABSENT | UNRESOLVED — D1–D10 are DECIDED at policy level, but D3 is explicitly schematic Y-axis and D4/D5 are schematic; the Contract Proposal 4 records PCB position unit, axis, rotation sign, transform order, and board side as UNRESOLVED | Geometry D1–D10 record; Contract Proposal 4 (boundary correction that D3 does not decide PCB board side) | S01, S02, S03, S04, S05, S29 | Project lead — record the PCB placement coordinate/transform convention |
| DEP-13 | Reference-study record and licensing/provenance review | Phase 0 dependency and licensing report; any reuse decision | ABSENT | OPEN — A8 direction is not approved (reference/compatibility/licensing); the studied reference version is not recorded | Ticket 4 requires recording the exact reference version studied; A8 record status OPEN; workflow handover requires licensing evidence before reuse | Phase 0 exit; any source-reuse decision | Project lead — approve the A8 direction and record the reference version and licensing review |
| DEP-14 | Benchmark and acceptance fixtures, board corpus, and tolerances | Deterministic fixtures; scale tests; completion of matrix fixture/tolerance cells | ABSENT | BLOCKED — ticket 9 requires every fixture to define expected results, tolerances, constraint outcomes, and failure behavior; none exists | Ticket 9 and 8; acceptance matrix rows all show `TBD` | S20 and all fixture/tolerance cells | Test owner — establish the fixture corpus once DEP-11/DEP-12 are resolved |

## Licensing and provenance status (ticket 5, Phase 0 task 10; deliverable 4)

This section records **status and evidence only**. It draws no legal conclusion, asserts no licence identifier that was not verified, and invents no compatibility determination.

### What was actually inspected

- This checkout contains **no dependency manifest** (no `Cargo.toml`, no lockfile, no vendored or submodule source) and no `LICENSE`, `NOTICE`, or `COPYING` file. There is therefore nothing from which a WREATH dependency licence could be enumerated or verified.
- The reference study (`WREATH-DSN-003_Reference_Behavior_Mapping.md`) read **published documentation pages only** (R1–R4, R6). It did **not** access the reference source repository (R5). No source code was downloaded, cloned, inspected, copied, adapted, or vendored.
- The licence or terms of the reviewed documentation pages were **not** reviewed in this task. No licence text, SPDX identifier, or compatibility statement was read or recorded.

### Status of licensing evidence

| Item | Status | Evidence |
|---|---|---|
| WREATH dependency licences (DSN-001, DSN-002, shared contract, geometry/connectivity inputs) | UNKNOWN — no manifest to inspect | The dependency artefacts themselves are absent from this repo and unverified in the wider project (`DEP-01`, `DEP-02`, `DEP-03`, `DEP-06`, `DEP-07`) |
| Reference-study artefact licence/compatibility | UNRESOLVED | R1–R4/R6 are documentation links; no licence review performed; R5 (source) not accessed; A8 direction OPEN (`DEP-13`) |
| Reuse of any reference material | NOT AUTHORISED | Nothing was copied; A8 proposes repository restrictions that are not approved |
| Benchmark corpus provenance | TBD | No fixture exists; see `WREATH-DSN-003_Benchmark_Dataset_Manifest.md` |
| Compatibility determination (dependency A vs dependency B) | NOT PERFORMED | No dependency artefacts or licences were available to compare |

### Required before any reuse or dependency adoption

1. A recorded **dependency inventory** once the dependencies exist (`DEP-01`, `DEP-02`, `DEP-03`, `DEP-06`, `DEP-07`).
2. A recorded **licence review** of each dependency's actual licence text, by the owner or a qualified reviewer — no compatibility conclusion is asserted by this audit.
3. Recording the **exact studied reference version** (ticket 4.5) and the **documentation/source licence terms** applicable to it (`DEP-13`).
4. Approval of the **A8 reference/compatibility/licensing direction**, which is currently OPEN and not approved.

**No legal conclusion is made or implied by this document.**

## Resolved decisions (policy level) — statuses preserved

Recorded decisions are preserved exactly as filed; no status is promoted.

- **Architecture A1–A8:** A1 APPROVED, A2 APPROVED, A3 APPROVED, A4 APPROVED, A5 CONDITIONAL (WHIR evidence check pending), A6 APPROVED, A7 PARTIAL (canonical Rust API approved; consumer bridge OPEN), A8 OPEN (proposed direction, not approved).
- **Identity B1–B10:** policy-level DECIDED with Project Lead approvals dated 2026-10-13; implementation details OPEN/DEFERRED. B5 (footprint identity) and B6 (pad identity) details are DEFERRED until footprint integration; B8 numbering rules DEFERRED.
- **Definitions/revisions C1–C9:** DECIDED at policy level; C2 (multi-unit), C3 (footprint association), C4 (pin-to-pad mapping), and C9 (provenance) explicitly DEFERRED from the current slice; revision-model details remain OPEN.
- **Geometry D1–D10:** DECIDED at policy level; implementation details remain OPEN (range/overflow, quantization/rounding, mirror-flag semantics, rotation matrix sign, transform order, pin-anchor semantics).
- **Resolution/API E1–E8:** DECIDED at policy level; exact type definitions, revision lifecycle, snapshot fields, and error payloads remain OPEN.
- **Validation/errors F1–F7:** DECIDED at policy level; exact error variants, validation rules, unknown-data preservation, and logging details remain OPEN.

**Important scope note:** A1–A8, B, C, D, E and F are scoped to DSN-001 (schematic) and DSN-002 (library). **None of them is a PCB-placement decision.** DEP-04, DEP-07, DEP-08, DEP-09, DEP-10, DEP-11, and DEP-12 are therefore genuinely open for DSN-003 even where a related decision exists.

## Unresolved decisions relevant to DSN-003 (preserved, not re-opened)

1. Board and placement-state ownership — OPEN (DEP-04).
2. PCB coordinate/unit/axis/rotation-sign/board-side/transform-order conventions — UNRESOLVED (DEP-12).
3. Connectivity input contract and stale/missing handling — OPEN (DEP-07).
4. Board revision token / concurrency token — OPEN (DEP-08).
5. Placement commit/rollback/cancellation boundary — OPEN (DEP-09).
6. Placement UI/API adapter and error mapping — OPEN (DEP-10; A7 sub-decisions OPEN).
7. Optimization baseline, benchmark set, seed policy, thresholds — PROPOSED, not approved (DEP-11).
8. WHIR/shared-contract authority — CONDITIONAL, evidence check pending (DEP-03; A5).
9. Reference/compatibility/licensing direction — OPEN (DEP-13; A8).
10. Open implementation-level details listed under each decision family above.

## Blockers (evidence-based)

1. **Ticket status Proposed — implementation not approved.** Status: BLOCKED. Evidence: ticket header states *Proposed — architecture contracts and MVP boundaries require approval before dependent implementation begins*; no approval record exists in this checkout.
2. **Open architecture decisions.** Status: BLOCKED where they gate implementation — DEP-04, DEP-07, DEP-08, DEP-09, DEP-10, DEP-11, DEP-12; plus A5 (CONDITIONAL), A7 sub-decisions (OPEN), A8 (OPEN).
3. **Dependencies absent from this repo and unverified in the wider project.** Status: UNVERIFIED for DEP-01, DEP-02, DEP-03, DEP-06, DEP-13; PARTIAL for DEP-05, DEP-09, DEP-10; UNRESOLVED for DEP-12. No dependency is classified MISSING for the wider project (see above).
4. **Open implementation-level details.** Status: NOT RUN / OPEN. Preserved from the decision records: coordinate range and overflow; quantization/rounding of transformed coordinates; mirror-flag semantics; rotation matrix sign under Y-down axes; pin-anchor/connection-endpoint semantics; revision identifier type; error variant names and payloads; diagnostic model (F5 deferred); unsafe-save rejection criteria.
5. **Proposed performance and quality targets.** Status: PROPOSED, not gates. Evidence: ticket 8 states the thresholds require approval after the baseline dataset is established.
6. **Phase 0 deliverables incomplete.** Status: PARTIAL/BLOCKED (see next section).

## Phase 0 deliverable status (ticket section 5, Phase 0)

| # | Ticket deliverable | Status | Evidence | Remaining work |
|---|---|---|---|---|
| 1 | Repository discovery report | PRESENT (draft) | `docs/project/WREATH-DSN-003_Phase0_Audit.md` | Refresh if the checkout or branch changes; add the new repo baseline when the ticket is ratified |
| 2 | Reference behavior mapping | PRESENT (draft) | `docs/project/WREATH-DSN-003_Reference_Behavior_Mapping.md` | R4 placement doc has no identifiable version; R5 (source) not reviewed (`DEP-13`, A8 OPEN); owner review of the recorded reference differences |
| 3 | Native placement feature specification | PRESENT (draft) | `docs/project/WREATH-DSN-003_Native_Placement_Feature_Spec.md` (A–G beyond the MVP slice) plus `WREATH-DSN-003_Manual_Placement_MVP.md` (MVP slice) | Owner approval; still gated on `DEP-04`, `DEP-06`, `DEP-07`, `DEP-11`, `DEP-12` |
| 4 | Dependency and licensing report | PARTIAL — dependencies covered; licensing status recorded, no licence reviewed | This document (register `DEP-01`–`DEP-14`; "Licensing and provenance status" section); A8 OPEN | Dependency inventory and licence review once dependencies exist; record the studied reference version and its licence terms (`DEP-13`) |
| 5 | Initial benchmark dataset | PRESENT (manifest only; no data) | `docs/project/WREATH-DSN-003_Benchmark_Dataset_Manifest.md` — all fixtures PROPOSED, **no measured baselines exist** | Create or source the boards, record object counts/provenance, and measure real baselines (`DEP-11`, `DEP-13`, `DEP-14`) |
| 6 | Architecture proposal | PARTIAL, NOT APPROVED | `docs/project/WREATH-DSN-003_Contract_Proposal.md` (DRAFT/PROPOSED) | Owner approval after DEP-04/DEP-07/DEP-08/DEP-09/DEP-10/DEP-12 are resolved |

**Phase 0 exit criteria:** the ticket requires the assignee to explain how a footprint is represented on the board, how its geometry is transformed, how connectivity influences placement, and which interfaces the engine must consume. Drafts now exist for all six deliverables, but the explanations cannot be substantiated: the footprint-instance and board model (`DEP-04`, `DEP-05`), PCB coordinate/transform convention (`DEP-12`), geometry API (`DEP-06`), and connectivity contract (`DEP-07`) are all unresolved, items 3, 4, 5, and 6 are unapproved, the licensing review has not been performed, and no measured baseline exists. **Phase 0 exit is therefore NOT MET** on evidence, not on document count.

## Required confirmations

- Project lead confirmation for each blocker; no approvals invented.
- Confirmation of the canonical ticket revision and the implementation slice before any dependent work.
- Confirmation that DEP-01/DEP-02 interfaces, the shared contract (DEP-03), and the geometry/connectivity inputs (DEP-06/DEP-07) exist somewhere in the wider WREATH program, or an explicit decision that they are missing and must be created.
