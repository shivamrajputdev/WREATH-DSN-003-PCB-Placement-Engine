# **WREATH-DSN-003 — PCB Placement Engine**

Ticket 003 is the third Design Engine migration ticket. It covers the PCB Placement Engine, which positions component footprints on the physical board while respecting mechanical constraints, electrical connectivity, and placement-quality objectives.

The ticket will use the same migration standard as Tickets 001 and 002: reference study, native data contracts, implementation phases, evaluation metrics, reproducible tests, and explicit acceptance criteria. It covers only PCB placement; routing and board geometry remain separate engines.

# **WREATH Native EDA Migration Ticket**

## **WREATH-DSN-003 — PCB Placement Engine**

**Ticket ID:** WREATH-DSN-003  
**Category:** Design Engine  
**Migration type:** Reference-study-led native implementation  
**Reference software:** KiCad PCB Editor  
**Implementation target:** WREATH native architecture  
**Priority:** High  
**Status:** Proposed — architecture contracts and MVP boundaries require approval before dependent implementation begins  
**Depends on:** WREATH-DSN-001 and WREATH-DSN-002 interfaces, plus the approved board geometry and WHIR contracts

---

## **1\. Objective**

Build WREATH's native PCB Placement Engine by studying component placement and positioning functionality in KiCad.

The engine must place, move, rotate, mirror, align, and arrange component footprints on a PCB while respecting the board's physical constraints and the designer's explicit placement requirements.

It must support both interactive placement by a human designer and algorithmic placement initiated by WREATH's automated design workflow.

The engine must also provide an initial automatic-placement capability that arranges components using measurable placement objectives, including connectivity-based proximity, component spacing, and placement feasibility.

The engine must operate on WREATH's native board representation. KiCad is a reference, not a mandatory runtime dependency.

## **2\. Problem statement**

Component placement determines where physical components sit on a PCB before copper routing is performed.

Poor placement can create long connections, difficult routing, unnecessary vias, congestion, mechanical interference, thermal problems, and avoidable manufacturing constraints.

A placement engine must do more than assign X and Y coordinates. It must understand footprint geometry, component orientation, board boundaries, fixed components, placement constraints, and connectivity relationships.

For example, placing a decoupling capacitor near its associated IC may improve the feasibility of the power connection. Placing a connector near the correct board edge may satisfy mechanical requirements. Rotating a polarized component incorrectly, however, can make an otherwise geometrically valid placement electrically unusable.

WREATH's placement engine must therefore distinguish between:

* Geometrically feasible placement.  
* Electrically informed placement.  
* Mechanically constrained placement.  
* Designer-controlled placement.  
* Automatically optimized placement.

A good optimization score must never override a mandatory placement constraint.

---

## **3\. Scope of migration**

### **3.1 Included functionality**

**A. Footprint placement operations**

* Add a footprint instance to the board.  
* Move a footprint to an absolute position.  
* Move a footprint relative to a reference point.  
* Rotate a footprint.  
* Flip a footprint between supported board sides.  
* Apply exact coordinate and orientation changes.  
* Remove or reposition a footprint through approved board operations.

**B. Manual placement support**

* Place components using coordinates and orientation supplied by the UI.  
* Support snapping to a configurable grid where appropriate.  
* Move selected footprints.  
* Align and distribute multiple footprints.  
* Support fixed or locked footprints.  
* Preserve the designer's explicit placement decisions.

**C. Geometry-aware placement**

* Evaluate footprint outlines and relevant physical geometry.  
* Check board-outline containment.  
* Respect placement keepouts and approved mechanical constraints.  
* Detect footprint overlap and minimum spacing violations.  
* Support board cutouts and nonrectangular outlines when the board geometry API supports them.  
* Handle front-side and back-side placement.

**D. Connectivity-aware placement**

* Read the board's connectivity information.  
* Use connection relationships to guide automatic placement.  
* Calculate placement objectives based on estimated connection lengths.  
* Allow critical components and related component groups to influence placement.  
* Preserve logical connectivity when a footprint changes position or orientation.

**E. Basic automatic placement**

* Generate an initial placement for unplaced footprints.  
* Support placement of selected footprints and all eligible footprints.  
* Respect fixed components and hard constraints.  
* Use deterministic, reproducible optimization for a given configuration and seed.  
* Support configurable objective weights.  
* Report components that cannot be placed feasibly.

**F. Placement evaluation**

* Measure estimated connection length.  
* Measure footprint overlap and clearance violations.  
* Measure board-boundary violations.  
* Measure component distribution and placement density.  
* Record optimization progress and final objective values.  
* Provide structured diagnostics for infeasible placements.

**G. Native integration**

* Read component and footprint data from the approved library engine.  
* Operate on the shared native board and geometry representation.  
* Expose stable APIs for interactive and automated placement.  
* Support transactional or otherwise recoverable placement changes according to the existing architecture.

### **3.2 Explicit exclusions**

This ticket does not implement:

* PCB routing or autorouting.  
* Copper-zone generation.  
* Full board-outline and cutout editing.  
* The complete geometry kernel.  
* Design Rule Check (DRC) as an independent validation engine.  
* Electrical Rule Check (ERC).  
* Signal-integrity or power-integrity simulation.  
* Thermal simulation.  
* Manufacturing output generation.  
* Full 3D mechanical collision simulation.  
* Reinforcement-learning training infrastructure.  
* A complete PCB editor UI.  
* AI-based natural-language design generation.

The placement engine may consume geometry, connectivity, constraints, and diagnostics supplied by other components. It must not silently take ownership of those components' responsibilities.

### **3.3 Minimum Viable Placement (MVP) and release boundary**

The first accepted vertical slice must be small enough to validate independently. It must include:

1. Resolve an existing board and footprint instance through approved stable identifiers.
2. Read the authoritative footprint definition and board geometry through approved interfaces; do not create duplicate authoritative models.
3. Apply exact position and supported rotation/side transformations using the shared coordinate and transform contract.
4. Support move, rotate, and flip only where the board/footprint model explicitly supports the operation.
5. Respect fixed footprints and mandatory placement constraints.
6. Evaluate board containment, keepouts, overlap, and required spacing through the approved geometry API; if a required geometry capability is unavailable, report it as a blocking dependency rather than implementing a parallel geometry kernel.
7. Expose placement operations through a stable engine API and a UI/automation-facing endpoint or adapter using the repository's approved service conventions.
8. Return structured success, infeasibility, validation, and internal-error results.
9. Ensure failed or rejected edits leave authoritative board state unchanged.
10. Provide deterministic tests for transforms, constraints, persistence integration, and failure behavior.

**Staged capability rule:** Basic deterministic automatic placement is the next increment after the manual-placement vertical slice and its contracts are accepted. Optimization improvements, configurable multi-term objectives, and benchmark targets must not block the first manual-placement slice, but remain required milestones for the complete DSN-003 ticket. Do not claim global optimality.

### **3.4 Architecture decisions required before dependent implementation**

The following decisions must be recorded and approved before the implementation depends on them:

- **Board and placement-state ownership:** identify which existing board model owns persisted footprint position, orientation, side, lock state, and revision. The placement engine may calculate candidates, but must not maintain a competing authoritative board state.
- **Shared identity and geometry contract:** reuse approved board, footprint-instance, library-footprint, pad, unit, origin, axis, rotation-sign, reflection, transform-order, and tolerance conventions. Do not invent conflicting IDs or coordinate conventions.
- **Geometry dependency:** identify the authoritative geometry/feasibility API and its supported board outlines, cutouts, keepouts, overlap, and clearance queries. Record missing capabilities as blockers or explicit limitations.
- **Connectivity input contract:** define which authoritative net/pad relationships the engine may consume and how missing or stale connectivity is reported. Do not own schematic connectivity or derive electrical truth from visual proximity.
- **Persistence and transaction boundary:** the board/document owner persists committed placement state. Candidate generation and evaluation must not silently mutate persisted state. Define commit, rollback, cancellation, and stale-revision behavior.
- **UI/API boundary:** define request/response, transport/adapter, error mapping, and revision handling using existing conventions. This does not imply a separate microservice. UI and automation clients must use the same engine contract.
- **Optimization baseline:** approve benchmark boards, hardware, metrics, baseline algorithm, seed policy, repetitions, stopping criteria, and thresholds before a performance or quality target becomes a release gate.

### **3.5 Minimum native placement data model**

The initial model must cover the following concepts without prematurely fixing concrete Rust types or serialization schemas:

- **Placement target:** board identity and one or more footprint-instance identities.
- **Footprint instance reference:** stable instance identity, authoritative library-footprint reference/version, and pad identities supplied by the board/library contracts.
- **Transform:** board position, rotation, board side, and the shared coordinate/precision convention.
- **Constraint state:** fixed/locked flag, allowed orientations, permitted region or placement constraints, and references to applicable keepouts/clearances.
- **Board revision:** a revision or equivalent concurrency token when supported, so a candidate cannot silently overwrite newer board changes.
- **Connectivity input:** references to nets/pads and connectivity-derived costs, marked as input data rather than placement-owned electrical truth.
- **Placement request:** selected targets, operation/mode, constraints, objective settings, deterministic seed where relevant, cancellation policy, and expected board revision where supported.
- **Placement candidate/result:** proposed transforms, changed instance IDs, feasibility status, diagnostics, objective metrics, execution status, and whether changes were committed.
- **Lifecycle/provenance:** candidate identifier, source request, seed/configuration, evaluation result, and commit/rejection outcome sufficient for deterministic tests and auditability.

The architecture review must map these concepts to existing WHIR and board contracts. Names above are conceptual labels, not finalized type names.

---

## **4\. Reference software and study material**

The assignee must study the following references before finalizing the native design.

### **4.1 KiCad PCB Editor manual**

[https\://docs.kicad.org/8.0/en/pcbnew/pcbnew.html](https://docs.kicad.org/8.0/en/pcbnew/pcbnew.html)

Study:

* Adding footprints to the board.  
* Moving and rotating footprints.  
* Flipping footprints between board sides.  
* Exact positioning and reference-relative movement.  
* Fixed footprints and placement constraints.  
* Assisted and automatic placement.  
* Footprint placement files and board integration.

### **4.2 KiCad board file format**

[https\://dev-docs.kicad.org/en/file-formats/sexpr-pcb/](https://dev-docs.kicad.org/en/file-formats/sexpr-pcb/)

Study:

* Board representation.  
* Footprint instances and coordinates.  
* Layer information.  
* Net definitions.  
* Board objects and relevant geometry.  
* How placement state is stored in a PCB document.

The board format is a reference for persisted board state. It does not define the complete internal architecture WREATH must use.

### **4.3 KiCad footprint library format**

[https\://dev-docs.kicad.org/en/file-formats/sexpr-footprint/](https://dev-docs.kicad.org/en/file-formats/sexpr-footprint/)

Study:

* Footprint geometry and local coordinate systems.  
* Pad locations and dimensions.  
* Footprint properties and layers.  
* Data required to transform a footprint into board coordinates.

### **4.4 KiCad footprint placement documentation**

[https\://github.com/KiCad/kicad-doc/blob/master/src/pcbnew/pcbnew\_footprint\_placement.adoc](https://github.com/KiCad/kicad-doc/blob/master/src/pcbnew/pcbnew_footprint_placement.adoc)

Study:

* Manual placement.  
* Automatic footprint distribution.  
* Fixed footprints.  
* Allowed component orientations.  
* Connectivity-based placement objectives.  
* Placement preparation and limitations.

### **4.5 KiCad source repository**

[https\://gitlab.com/kicad/code/kicad](https://gitlab.com/kicad/code/kicad)

Use source inspection to investigate behavior that is not adequately explained by the manuals.

Record the exact version studied. Do not assume every behavior or algorithm remains identical across releases.

### **4.6 Additional study topics**

The assignee must understand:

* Computational geometry and polygon containment.  
* Coordinate transforms and footprint anchors.  
* Rigid-body rotation and reflection.  
* Spatial indexing for collision queries.  
* Graph-based connectivity and estimated connection length.  
* Constrained optimization.  
* Simulated annealing or other suitable placement heuristics.  
* Deterministic randomization and reproducible optimization.  
* Hard constraints versus soft optimization objectives.  
* Benchmark design and optimization quality measurement.

The choice of optimization algorithm must be justified against WREATH's initial scope and performance needs. Do not introduce a complex optimizer without a measurable reason.

---

## **5\. Migration phases and required deliverables**

### **Phase 0 — Repository discovery and reference study**

**Tasks**

1. Inspect the WREATH repository and current architecture.  
2. Identify the authoritative WHIR/Hardware IR definition.  
3. Locate the board, footprint-instance, geometry, and connectivity models.  
4. Review Ticket 001 and Ticket 002 interfaces.  
5. Identify existing coordinate, unit, rotation, and layer conventions.  
6. Inspect existing geometry libraries and spatial-indexing infrastructure.  
7. Study the selected KiCad placement behaviors.  
8. Identify placement features that can be migrated independently.  
9. Establish baseline test boards and reference placements.  
10. Review dependency licenses and compatibility.

**Deliverables**

* Repository discovery report.  
* Reference behavior mapping.  
* Native placement feature specification.  
* Dependency and licensing report.  
* Initial benchmark dataset.  
* Architecture proposal.

**Exit criteria**

The assignee can explain how a footprint is represented on the board, how its geometry is transformed, how connectivity influences placement, and which interfaces the engine must consume.

### **Phase 1 — Native data contracts and placement model**

**Tasks**

Define the placement request model, including:

* Target board and footprint identifiers.  
* Initial and target positions.  
* Rotation and board side.  
* Fixed or movable state.  
* Allowed orientations.  
* Relevant placement constraints.  
* Optimization settings.  
* Cancellation and error behavior.

Define the result model, including:

* Final position and orientation.  
* Changed footprint identifiers.  
* Constraint violations.  
* Optimization score and metrics.  
* Infeasible placement diagnostics.  
* Execution status.

Define the placement operations:

* Add or initialize a footprint instance.  
* Move a footprint.  
* Rotate or flip a footprint.  
* Move a footprint relative to a reference.  
* Align or distribute a selection.  
* Run automatic placement on eligible footprints.  
* Evaluate a candidate placement.  
* Commit or reject placement changes.

Specify how the engine interacts with board geometry, footprint definitions, connectivity, and the shared data model.

**Deliverables**

* Approved API contract.  
* Coordinate and transform specification.  
* Placement constraints specification.  
* Optimization objective specification.  
* Error and result schema.

**Exit criteria**

The interfaces clearly distinguish mandatory constraints from optimization preferences and prevent unauthorized modification of fixed components.

### **Phase 2 — Native implementation**

Implement the engine in the following increments.

**Increment A: Footprint transforms**

Implement position, rotation, and supported board-side transformations.

Test that footprint-local geometry is correctly transformed into board coordinates.

**Increment B: Manual placement operations**

Implement move, rotate, flip, exact positioning, and reference-relative movement.

Ensure that operation failures do not leave the board in an inconsistent state.

**Increment C: Geometry-aware feasibility**

Implement placement feasibility queries using the approved geometry interface.

Check board containment, footprint overlap, keepouts, and applicable spacing constraints.

Do not build a separate general-purpose DRC engine.

**Increment D: Fixed and constrained components**

Implement fixed-footprint handling, allowed orientations, and hard placement constraints.

Ensure that automatic placement cannot move a component that is explicitly fixed.

**Increment E: Basic automatic placement**

Implement an initial placement strategy for eligible footprints.

The strategy may use deterministic heuristics, connectivity information, component size, and allowed orientations.

It must produce a valid initial candidate where feasible, report infeasibility when appropriate, and never claim global optimality without proof.

**Increment F: Placement optimization**

Add a measurable optimization loop for improving candidate placements.

Support configurable objective weights and record the initial and final objective values.

Use a deterministic seed and documented stopping criteria where randomized methods are employed.

**Increment G: Native integration**

Integrate with the library engine, board representation, and approved connectivity interfaces.

Ensure that the placement result is visible to downstream routing and validation components without copying authoritative board state into a separate inconsistent model.

**Deliverables**

* Native placement engine.  
* Documented APIs.  
* Unit and integration tests.  
* Reference placement fixtures.  
* Benchmark datasets and scripts.  
* Implementation notes.

### **Phase 3 — Evaluation and parity testing**

**Tasks**

1. Reproduce equivalent manual placement operations in KiCad and WREATH.  
2. Compare positions, orientations, board-side changes, and footprint geometry.  
3. Test geometry feasibility against the approved test corpus.  
4. Test fixed footprints and hard constraints.  
5. Test automatic placement on representative boards.  
6. Compare estimated connection length and placement quality.  
7. Test deterministic repeatability.  
8. Test invalid requests and infeasible placement cases.  
9. Measure runtime and memory usage.  
10. Record any intentional differences from the reference.

**Deliverables**

* Automated functional test suite.  
* Reference behavior comparison.  
* Placement-quality report.  
* Performance report.  
* Known-gap register.

**Exit criteria**

All mandatory functional tests pass, no hard constraints are violated in accepted placements, and optimization quality is reported against approved baselines.

### **Phase 4 — Integration and acceptance**

**Tasks**

1. Verify compatibility with the approved board and footprint models.  
2. Verify that connectivity remains consistent after placement operations.  
3. Verify that fixed and locked components are preserved.  
4. Run regression tests for board loading and placement.  
5. Verify that downstream routing can consume the resulting placements.  
6. Complete dependency and licensing reviews.  
7. Document unsupported constraints and known limitations.  
8. Submit the final evidence package for review.

**Deliverables**

* Integrated placement engine.  
* Passing regression suite.  
* Updated architecture documentation.  
* Reproducible evaluation report.  
* Final acceptance checklist.

**Exit criteria**

The placement engine is independently testable, uses native WREATH interfaces, and passes its approved acceptance gates without requiring KiCad at runtime.

---

## **6\. Proposed native architecture**

WREATH PCB Placement Engine  
|  
\+-- Placement Request API  
|   \+-- Target footprints  
|   \+-- Position and orientation  
|   \+-- Constraints and options  
|  
\+-- Placement Operations  
|   \+-- Add / move / rotate / flip  
|   \+-- Exact and relative positioning  
|   \+-- Align and distribute  
|  
\+-- Geometry and Feasibility Adapter  
|   \+-- Footprint transforms  
|   \+-- Board containment queries  
|   \+-- Overlap and spacing queries  
|   \+-- Keepout queries  
|  
\+-- Constraint Manager  
|   \+-- Fixed components  
|   \+-- Allowed orientations  
|   \+-- Mechanical constraints  
|   \+-- Placement regions  
|  
\+-- Automatic Placement  
|   \+-- Initial candidate generation  
|   \+-- Connectivity-based objectives  
|   \+-- Optimization loop  
|   \+-- Candidate evaluation  
|  
\+-- Result and Diagnostics  
    \+-- Placement changes  
    \+-- Objective metrics  
    \+-- Constraint violations  
    \+-- Infeasibility reports

This is a logical decomposition, not a requirement to create separate microservices for every block.

---

## **7\. Placement model and optimization rules**

### **7.1 Coordinate conventions**

Use the coordinate and unit conventions already approved by WREATH.

Document:

* Board coordinate origin.  
* Footprint-local origin.  
* Position units.  
* Rotation direction and angular units.  
* Board-side representation.  
* Transform order.  
* Tolerance and precision rules.

Do not introduce an independent coordinate convention that conflicts with the shared geometry kernel.

### **7.2 Hard constraints**

Hard constraints define whether a placement is permitted.

Examples include:

* A fixed footprint cannot move.  
* A footprint must remain within the permitted board region.  
* A footprint must not intersect a mandatory keepout.  
* A connector must remain within its allowed placement region.  
* A component may use only its permitted orientations.  
* Mandatory mechanical clearances must be respected.

If a hard constraint cannot be satisfied, the engine must report infeasibility rather than quietly violating it.

### **7.3 Soft objectives**

Soft objectives rank otherwise feasible placements.

Examples include:

* Reducing estimated connection length.  
* Grouping related components.  
* Reducing unnecessary spacing between tightly coupled components.  
* Improving placement distribution.  
* Reducing congestion indicators.  
* Respecting preferred orientation or placement regions.

Soft objectives must not override hard constraints.

### **7.4 Initial objective function**

A proposed initial objective is:

\[  
J \= w\_L L \+ w\_C C \+ w\_D D \+ w\_P P  
\]

Where:

* (L) is the estimated connection-length cost.  
* (C) is a placement-congestion cost.  
* (D) is a distribution or density cost.  
* (P) is a penalty for soft preference violations.  
* (w\_L, w\_C, w\_D, w\_P) are documented configurable weights.

Hard constraints should be enforced by candidate rejection or another rigorously documented feasibility mechanism, rather than relying exclusively on large penalty values.

The objective function is a starting proposal. The assignee must define each term precisely, normalize or scale terms appropriately, and justify the final formulation through benchmarks.

The connection-length estimate must account for the available connectivity data. An unrouted connection's estimated distance is not the same as the final routed copper length.

### **7.5 Placement-state lifecycle**

Placement operations must distinguish an unmodified board state from proposed candidates and committed changes. The following conceptual lifecycle is required; map it to the existing application transaction model during architecture review:

1. **Current:** the authoritative persisted or in-memory board revision before a request.
2. **Requested:** a validated placement operation has been accepted for evaluation, but has not changed authoritative placement state.
3. **Candidate:** proposed transforms have been generated for the selected footprint instances.
4. **Evaluated-feasible:** all applicable mandatory constraints passed against the stated board revision.
5. **Evaluated-infeasible:** one or more mandatory constraints failed, or a required geometry/connectivity input was unavailable. Diagnostics must identify the reason.
6. **Committed:** a feasible candidate was applied atomically through the authoritative board/document owner, with a new revision when supported.
7. **Rejected / Cancelled / Failed:** the candidate was not committed. The authoritative board remains unchanged by that request.

Required rules:

- Candidate generation and scoring are non-mutating operations.
- Only a feasible candidate may be committed, unless an explicitly approved interactive override policy exists; any override must be visible and auditable, never implicit.
- Commit must verify the board revision or equivalent concurrency token where supported. A stale candidate must be rejected or re-evaluated, not silently applied.
- Cancellation and errors must not leave partial footprint transforms behind.
- A committed result reports the changed footprint IDs and resulting revision when available.
- An optimizer must not mark a candidate feasible solely because its objective score improved.
- The lifecycle and state transitions must be tested, including repeated requests and commit failure.

---

## **8\. Evaluation metrics and acceptance criteria**

The following thresholds are proposed initial targets and require approval after the baseline dataset is established.

### **Functional correctness**

| Metric | Proposed target |
| ----- | ----- |
| Mandatory unit and integration tests | 100% passing |
| Transform correctness | 100% on approved geometry fixtures |
| Fixed-footprint preservation | 100% |
| Hard-constraint compliance | Zero violations in accepted placements |
| Invalid-operation handling | All mandatory invalid-input tests pass |
| Failed operation atomicity | 100% of approved failure fixtures leave authoritative state unchanged |
| Hard-constraint gate | No infeasible candidate committed through the normal API |
| Deterministic repeatability | Identical result for identical inputs and seed, where deterministic mode is specified |

### **Placement quality**

Measure the following on documented test boards:

* Total estimated connection length.  
* Number and severity of placement constraint violations.  
* Footprint overlap count.  
* Board-boundary violations.  
* Placement density and distribution.  
* Number of footprints successfully placed.  
* Optimization runtime.  
* Objective improvement from initial candidate to final candidate.

Proposed target for automatic placement: achieve at least a 10% reduction in the approved connection-length objective versus the specified baseline on the designated optimization benchmark set, without violating hard constraints. This target must be validated for feasibility and approved before it becomes a mandatory acceptance gate.

A lower connection-length objective does not, by itself, prove that a placement is easier to route or better thermally. Report each metric separately.

### **Performance**

Measure:

* Single-footprint move and rotation latency.  
* Geometry-feasibility query latency.  
* Placement evaluation latency.  
* Initial placement runtime.  
* Optimization runtime.  
* Peak memory use as footprint count increases.

Use small, medium, and large benchmark boards with documented object counts and hardware.

Set final latency targets after establishing a reproducible baseline. Do not use unverified performance estimates as acceptance evidence.

### **Integration**

* The engine uses approved native WREATH interfaces.  
* Library and board footprint references remain valid.  
* Connectivity is preserved through footprint transforms.  
* Downstream routing can consume the placement result.  
* No mandatory runtime dependency on KiCad remains.  
* No unrelated engine is implemented under this ticket.

---

## **9\. Mandatory test scenarios**

1. Place one footprint at an absolute position.  
2. Move a footprint relative to a reference point.  
3. Rotate a footprint through supported angles.  
4. Flip a footprint to the opposite board side.  
5. Transform an asymmetric footprint and verify all geometry.  
6. Attempt to move a fixed footprint.  
7. Place a footprint fully within the board outline.  
8. Attempt a placement outside the permitted board region.  
9. Test overlapping footprints.  
10. Test a footprint near a board edge.  
11. Test board cutouts and nonrectangular boundaries where supported.  
12. Test placement keepouts.  
13. Test allowed and forbidden orientations.  
14. Place connected components using connectivity-aware objectives.  
15. Run automatic placement with a subset of fixed components.  
16. Run automatic placement with an infeasible constraint set.  
17. Run the optimizer twice with the same deterministic seed.  
18. Run the optimizer with different permitted seeds and compare quality.  
19. Test an empty board.  
20. Test a large board with many footprints and connections.  
21. Verify that placement operations preserve footprint and pad identities.  
22. Verify that failed operations do not corrupt the board state.  
23. Verify candidate generation and evaluation do not mutate authoritative board state.  
24. Verify infeasible candidates cannot be committed through the normal API.  
25. Verify commit failure rolls back all proposed transforms.  
26. Verify stale board revisions are rejected or re-evaluated where revision tokens are supported.  
27. Verify cancellation leaves the board unchanged.  
28. Verify API/adapter validation, structured errors, and response status for invalid and infeasible requests.  
29. Verify deterministic results include the configuration and seed used.  
30. Verify missing geometry or connectivity capabilities produce explicit diagnostics rather than silent assumptions.

Each fixture must define expected results, allowed tolerances, constraint outcomes, and failure behavior.

---

## **10\. Risks and mitigations**

| Risk | Mitigation |
| ----- | ----- |
| Incorrect coordinate transformations | Establish coordinate conventions and test asymmetric footprints |
| Overlapping or off-board placements | Enforce feasibility checks before accepting candidates |
| Optimizer moves designer-fixed parts | Make fixed state a mandatory constraint |
| Shorter estimated connections produce worse routing | Track routing-relevant metrics separately and evaluate with downstream tests |
| Excessive optimization runtime | Establish stopping conditions and benchmark representative boards |
| Nondeterministic results | Provide a reproducible mode with controlled seeds and documented behavior |
| Geometry implementation duplicated across engines | Consume the approved geometry kernel through stable interfaces |
| Scope expansion into routing or DRC | Keep those responsibilities in their designated engines |
| Incompatible source reuse | Document provenance and license compatibility before reuse |

---

## **11\. Definition of done**

* Repository discovery and reference study are documented.  
* Placement API, UI/automation adapter contract, and coordinate conventions are approved.  
* Minimum native placement data model and placement-state lifecycle are documented and mapped to shared contracts.  
* Footprint transformations are implemented and tested.  
* Manual move, rotate, flip, and positioning operations work.  
* Geometry-aware feasibility checks are integrated.  
* Fixed footprints and hard constraints are enforced.  
* Initial automatic placement is implemented.  
* Placement optimization has measurable objectives.  
* Infeasible placements produce structured diagnostics.  
* Candidate evaluation is separate from commit, and failed operations preserve authoritative board state.  
* Mandatory API, lifecycle, unit, integration, and regression tests pass.  
* Placement-quality and performance reports are recorded.  
* Integration with the native board and footprint models is verified.  
* Downstream routing compatibility is tested.  
* Licensing and dependency reviews are complete.  
* Known limitations are documented.  
* Code review and technical acceptance are complete.

## **12\. Required final engineering report**

The assignee must submit:

1. Implementation summary.  
2. Source files added or modified.  
3. Reference sources and license decisions.  
4. Placement API and coordinate-transform documentation.  
5. Hard-constraint and soft-objective definitions.  
6. Test fixtures and automated test results.  
7. Placement-quality metrics and baseline comparisons.  
8. Performance measurements.  
9. Integration and regression results.  
10. Known limitations and unresolved issues.  
11. Evidence for every acceptance criterion.

Clearly distinguish functional correctness, placement feasibility, and optimization quality.

**Scope restriction:** Do not implement PCB routing, copper zones, the standalone DRC engine, the full board geometry kernel, or another engine within WREATH-DSN-003.

**End of ticket — WREATH-DSN-003**

## **Reference sources**

* [KiCad PCB Editor manual](https://docs.kicad.org/8.0/en/pcbnew/pcbnew.html) — footprint positioning, transformations, constraints, and automatic placement.
* [KiCad board file format](https://dev-docs.kicad.org/en/file-formats/sexpr-pcb/) — board representation and footprint-instance data.
* [KiCad footprint placement documentation](https://github.com/KiCad/kicad-doc/blob/master/src/pcbnew/pcbnew_footprint_placement.adoc) — placement workflow and optimization considerations.
