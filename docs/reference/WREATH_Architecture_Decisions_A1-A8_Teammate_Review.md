# WREATH Architecture Decisions: A1–A8 Master Record

**Status:** Working master decision record  
**Last updated:** 2026-10-13 (A8 discussion draft)  
**Purpose:** Preserve owner decisions, alternatives, rationale, governance, trade-offs, and unresolved questions for teammates.  
**Authority note:** This document records decisions approved in conversation. It does not claim the repository has been updated.

## How to read this record

- **Approved:** Explicitly selected by the project owner.
- **Recommended:** Suggested but not approved.
- **Open:** Must be decided later; do not infer an answer.
- **Deferred:** Intentionally postponed.

Approval of one decision does not approve adjacent implementation details. Preserve decision history if a decision changes.

## Executive summary

| ID | Topic | Status | Decision |
|---|---|---|---|
| A1 | Workspace and broad architecture | **Approved** | Rust workspace for both engines and shared contracts; in-process APIs initially; defer UI and service transport decisions. |
| A2 | Engine execution model | **Approved** | Separate DSN-001 and DSN-002 Rust crates, used in-process initially, with clear public APIs and independent testing. Separate crates do not automatically provide independent binary/runtime upgrades. |
| A3 | Engine ownership and definition updates | **Approved** | Strict ownership boundaries and agreed interfaces. Preserve the original definition state for existing designs and require explicit review before applying library updates. |
| A4 | Shared-contract ownership and language neutrality | **Approved** | Dedicated shared-contract crate. Contract is Rust-native initially; library contribution formats remain language-neutral through importers. No language-neutral contract representation required in the first release. |
| A5 | Shared IR / WHIR | **Conditional direction approved; evidence check pending** | If no authoritative, versioned WHIR specification exists, create a minimal, explicitly versioned shared contract for DSN-001/DSN-002 integration and testing. |
| A6 | Engine dependency direction | **Approved** | Both engines depend on the shared-contract crate; neither depends directly on the other engine's concrete implementation. Runtime capability calls still require an explicit interface/adapter. |
| A7 | UI and automation integration boundary | **Partially decided** | Rust API is the canonical engine API. Python bridge choice and other consumer integration details remain open. |
| A8 | KiCad reference, compatibility, and licensing | **Open; proposed direction for teammate review** | Proposed: use KiCad as a reference, target selected file-format compatibility and selected behavioral parity in phases, and keep source inspection/reuse subject to licensing and provenance review. |

---

# A1. Workspace and broad architecture

**Status: APPROVED**

## Decision
Use a Rust workspace for both engines and shared contracts. Keep engine APIs in-process initially. Do not commit to a UI framework, IPC mechanism, network transport, or service architecture before those requirements are known.

## Rationale
- A Rust workspace supports compile-time type checking and convenient testing across crate boundaries.
- In-process APIs avoid prematurely introducing process deployment, transport, serialization, and service operations.
- Clear APIs and ownership boundaries leave room for adapters later.
- This is an initial architecture, not a claim that every component must always run in one process.

## Alternatives considered

### Option 1: Rust workspace with in-process APIs — selected
**Benefits:** shared Rust types where appropriate; simpler debugging and testing; fewer deployment concerns; modular source/test evolution.  
**Costs:** no independent runtime upgrades merely from separate crates; APIs must prevent tight coupling; external boundaries may later need serialization and adapters.

### Option 2: Separate local processes or services
**Benefits:** stronger runtime isolation and potential independent deployment or restart.  
**Costs:** IPC, protocol versioning, process lifecycle, packaging, observability, and failure-handling complexity.

### Option 3: Hybrid architecture
**Benefits:** isolate selected workloads while keeping others simple.  
**Costs:** multiple execution/failure models and the need to justify each boundary.

### Option 4: Network-first services
**Benefits:** suitable if remote execution and independent deployment are core requirements from the start.  
**Costs:** commits to networking, deployment, authentication, and distributed failure handling before the need is established.

## Governance
- Keep domain logic independent of UI and transport adapters.
- Proposals are not approvals.
- Do not infer a UI framework, persistence format, IPC protocol, or service boundary from A1.
- Changes to execution boundaries require an explicit decision and documented migration/compatibility implications.

---

# A2. Engine execution model

**Status: APPROVED**

## Decision
Use distinct Rust crates for DSN-001 (Schematic Engine) and DSN-002 (Component and Library Engine) in the workspace. Use their public APIs in-process for the initial MVP. Prioritize debuggability, verification, maintainability, and evidence-led evolution over rushing to market at any cost.

Preserve a credible path toward separately packaged or executed components later. That capability is not automatically delivered by in-process crates.

## Alternatives considered

### Option 1: In-process Rust crates — selected
**Benefits:** straightforward debugging and local tests; shared types without serialization for internal calls; lower operational complexity; separate source and test evolution.  
**Costs:** source independence is not runtime independence; cross-crate coupling can grow; resource-heavy operations may later need scheduling or isolation.

### Option 2: Separate local processes
**Benefits:** independent lifecycle and potential runtime upgrades; stronger fault isolation.  
**Costs:** IPC, serialization, lifecycle management, error propagation, packaging, and compatibility testing become immediate requirements.

### Option 3: Hybrid execution
**Benefits:** isolates selected workloads while retaining simple in-process calls elsewhere.  
**Costs:** multiple execution models and more complicated testing.

### Option 4: Network-first services
**Benefits:** remote execution and independent deployment from the outset.  
**Costs:** networking, deployment, authentication, observability, and distributed failure modes before their necessity is established.

## Governance and implementation rules
- Expose documented public engine APIs; do not rely on private internals across crate boundaries.
- Keep domain behavior transport-neutral.
- Make errors explicit and testable.
- Add observability and long-running-operation handling where needed.
- Do not claim independent binary/runtime upgrades until process boundaries, versioned protocols, packaging, and update behavior are designed and tested.
- Revisit this decision only when concrete requirements justify independent execution/deployment.

---

# A3. Engine ownership and library-definition updates

**Status: APPROVED**

## Decision
Use strict ownership boundaries between two distinct engines. They own different domain data and communicate through agreed, documented interfaces.

- **DSN-001 Schematic Engine:** schematic documents and sheets, placed component instances, placement/orientation, instance-specific reference designators, wiring, junctions, connectivity, and references to reusable library definitions.
- **DSN-002 Component and Library Engine:** reusable component/symbol definitions, pins and properties, footprints, pads, pin-to-pad mappings, library metadata, revision resolution, and reusable definition geometry/properties.

These are agreed domain responsibilities. Exact schemas, API signatures, and dependency mechanics remain to be designed against authoritative requirements.

## Ownership rules
- An engine must not mutate another engine's private state directly.
- Cross-engine operations use public interfaces and explicit request/response/error behavior.
- A schematic instance differs from a reusable library definition. Multiple instances can reference one definition while retaining distinct identity and placement.
- The library is the source of truth for reusable definitions; the schematic engine is the source of truth for schematic instances and connectivity.

## Approved definition-versioning and update policy
**Preserve the original definition state used by an existing design and require explicit review before applying library updates to that design.**

- New placements resolve the currently approved library definition.
- Existing placements retain a revision reference and enough preserved information to reproduce their original effective definition.
- Newer revisions can be presented for review rather than silently changing existing designs.
- Applying an update triggers appropriate validation and reports failures/incompatibilities.
- A snapshot need not mean copying every library file into every schematic. Immutable revision references and/or stored snapshots remain implementation options.

## Alternatives considered

### Option 1: Always use the latest definition
**Benefits:** new library changes become immediately available.  
**Risks:** existing designs may change silently; reproducibility and debugging suffer.  
**Decision:** not the default for existing placements. Latest approved definitions are suitable for new placements.

### Option 2: Preserve the original definition snapshot
**Benefits:** designs remain reproducible if a library changes or disappears.  
**Risks:** storage, snapshot format/versioning, and migration rules are required.  
**Decision:** approved as a behavioral guarantee; exact storage mechanics remain open.

### Option 3: User reviews and applies updates
**Benefits:** library improvements remain available without silently changing designs.  
**Risks:** requires comparison, compatibility classification, validation, and recovery behavior.  
**Decision:** approved for updates to existing designs.

## Additional policies to define
1. **Identity and revision:** stable definition identity and unambiguous revision references.
2. **Impact analysis:** identify affected instances and distinguish metadata changes from geometry, pin, electrical, footprint, or mapping changes.
3. **Persistence/recovery:** preserve enough state to reopen the original definition when a library or revision is unavailable.
4. **Failure handling:** explicitly report missing definitions, incompatible revisions, failed updates, and interrupted writes. No silent substitution.
5. **Auditability/undo:** record applied revisions and affected instances; integrate with undo/redo where appropriate.
6. **Validation scope:** structural validity is not proof of electrical correctness or manufacturing suitability.

## Governance
- Preserve historical revision meaning.
- Updates are deliberate and validated.
- Successful parsing does not certify a community contribution as electrically correct.
- Do not infer exact schemas, snapshot storage, compatibility classes, or undo implementation from A3.

---

# A4. Shared-contract ownership and language neutrality

**Status: APPROVED**

## Decision
Use a **dedicated shared-contract crate** for stable types and rules that DSN-001 and DSN-002 must agree on.

The contract is initially Rust-native at the type/API level. **Library contribution formats remain language-neutral**, and contributors must not need to write Rust or change engine source to add supported components or footprints.

Do not require a language-neutral contract representation, generated bindings, or network protocol from the first release. Preserve a path to those capabilities if concrete consumers later require them.

## Why these are separate concerns
1. **Community library data:** reusable components, symbols, footprints, pads, mappings, metadata, and revisions. These are data contributions, not engine code.
2. **Import/validation adapters:** format-specific parsers and converters translate supported external formats into DSN-002's internal representation and validate content.
3. **Engine shared contract:** stable interface through which DSN-001 and DSN-002 exchange required information.

The shared-contract crate must not become a repository for every footprint or the full internal data model of both engines.

## Proposed shared-contract responsibilities
Subject to detailed design, it may include:
- Stable library and definition identifiers.
- Revision references and snapshot metadata.
- Definition-resolution requests/responses.
- Shared errors and diagnostics.
- Contract compatibility/versioning rules.
- Only minimal shared geometry or identity types that genuinely require identical semantics.

Exact fields, types, crate name, serialization format, and API signatures remain open.

## Community contribution and format policy
- Adding/updating a library package should not require recompiling WREATH or changing engine source.
- Contributors should be able to use supported language-neutral formats and authoring tools.
- Format-specific parsing belongs in importers/adapters, not the shared-contract crate.
- Each supported format needs explicit semantics for units, geometry, identities, pins, electrical properties, footprints, and mappings where relevant.
- Unsupported or ambiguous data must be reported, not silently guessed.
- Record provenance, source revision, and validation status where applicable.
- Structural validation does not certify electrical correctness, footprint suitability, or manufacturing readiness.

## Alternatives considered

### Option 1: Dedicated shared-contract crate — selected
**Benefits:** neutral ownership; both engines can evolve internals behind a stable boundary; Rust compile-time checking for initial in-process calls; future adapters can expose the contract without making external serialization mandatory now.  
**Costs:** shared types need disciplined scope; breaking changes affect both engines; Rust-native APIs do not automatically provide cross-language bindings.

### Option 2: DSN-002 owns the contract
**Benefits:** simple if the library engine is the sole provider of definitions.  
**Costs:** couples DSN-001 to DSN-002's crate ownership and evolution; less neutral if the contract grows.  
**Decision:** not selected.

### Option 3: Each engine defines its own types and uses adapters
**Benefits:** maximum internal freedom.  
**Costs:** duplicate representations, translation code, and semantic drift around identity, geometry, errors, and revisions.  
**Decision:** not selected for the initial in-process architecture. Adapters remain appropriate at external file-format boundaries.

### Option 4: Language-neutral schema/IDL from the first release
**Benefits:** can support generated bindings, non-Rust clients, and independent processes.  
**Costs:** schema tooling, generation, compatibility rules, and possible serialization constraints before a concrete consumer requires them.  
**Decision:** not required for the first release; reconsider when a real non-Rust or separately executed consumer establishes the need.

## Contract governance
- Keep the shared crate limited to stable public concepts needed by both engines.
- Keep engine-specific internal models private.
- Review contract changes explicitly and test compatibility.
- Document breaking changes and migration expectations.
- Do not require library contributors to modify the shared crate to add a component or footprint.
- Keep file-format compatibility separate from engine API compatibility.
- Add a language-neutral contract representation only through a separate explicit decision.

---

# A5. Shared IR / WHIR model

**Status: CONDITIONAL DIRECTION APPROVED; EVIDENCE CHECK PENDING**

## Decision
The project owner selected **Option 2: create a minimal, explicitly versioned shared contract**, conditional on confirming that no authoritative, versioned WHIR specification already exists.

Before creating a new contract, inspect the WREATH project documentation and repository for an authoritative WHIR specification and record the evidence. If a suitable authoritative specification is found, review it against DSN-001/DSN-002 integration and testing needs before adopting or extending it. Do not assume that a file or concept named WHIR is authoritative merely because it exists.

If no suitable specification exists, draft the smallest contract that supports the agreed integration slice and demonstrable tests.

## Rationale
- Both engines need compatible shared semantics to integrate and be tested together.
- A narrow contract reduces duplicated conversion logic and semantic drift around identities, references, revisions, geometry, and errors.
- The contract can be designed before either engine is fully implemented, using concrete workflow and acceptance-test requirements.
- A1/A2 select a Rust workspace with in-process APIs initially, so the initial contract may be Rust-native. This decision does not require a network protocol, UI protocol, serialization format, or separate service.
- The shared contract is not the Python automation harness and must not become a catalogue of every engine operation.

## Scope boundaries
- The shared contract defines only agreed types and semantics needed across the engine boundary.
- Engine APIs define callable operations.
- The Python harness, if adopted for orchestration, coordinates workflows and test execution through an adapter/interface that will be decided separately.
- Exact identifiers, geometry conventions, revision semantics, error types, persistence, crate names, and API signatures are not approved by A5; they require their own decisions and evidence.

## Alternatives considered
1. **Adopt existing WHIR:** preferred only if an authoritative, versioned specification is found and validated.
2. **Create a minimal shared contract:** selected conditional direction if no suitable authoritative WHIR specification exists.
3. **No shared IR:** not selected for the initial integration path because separate schemas would increase conversion code and risk semantic mismatch without a demonstrated benefit.

## Required evidence and follow-up
1. Search project documentation and repository history for WHIR/shared-IR specifications.
2. Record the exact source, version, owner/status, and whether it defines usable integration semantics.
3. If found, map it to DSN-001/DSN-002 acceptance and integration-test needs; record gaps before adopting it.
4. If not found, record the search scope and results, then draft the minimal versioned contract.
5. Map each shared-contract obligation to at least one demonstrable assertion or integration test; shared evidence must not double-count distinct obligations.

**Approval:** Owner selected the conditional Option 2 direction in conversation on 2026-10-13.  
**Not implied:** The WHIR absence check is not complete; the contract schema and its individual fields are not yet approved.

# A6. Dependency direction

**Status: OPEN**

## Decision question
Which crates may depend on which, and how should DSN-001 obtain library definitions from DSN-002 without creating circular dependencies or coupling the engines' private internals?

## Options
1. **Hub model:** both engine crates depend on the dedicated shared-contract crate; neither engine crate depends directly on the other.
2. **DSN-001 depends on DSN-002:** the schematic engine calls the library engine directly.
3. **DSN-002 depends on DSN-001:** the library engine depends on the schematic engine.

## Initial recommendation
Option 1, the hub model, is the strongest starting point given A1/A2's Rust workspace and in-process API direction and A5's conditional shared-contract direction. It keeps ownership clear, reduces circular-dependency risk, and permits independent engine testing. However, the hub model does not by itself explain runtime orchestration: DSN-001 may still need a library-resolution capability supplied through an explicit interface or injected dependency. The exact mechanism is part of this decision and the contract/API design.

## Trade-offs to review
- Whether DSN-001 can consume a library-resolution interface without depending on DSN-002's concrete crate.
- Whether shared-contract types stay limited to boundary concepts rather than absorbing both engines' internal models.
- How integration tests instantiate both engines and exercise resolution across the boundary.
- How errors and missing/incompatible definitions cross the interface.
- Whether the chosen dependency graph supports future adapters without making Python or a UI part of the core engine dependency graph.

## Owner-approved decision
**Option 1: shared-contract hub model.** Both DSN-001 and DSN-002 depend on the shared-contract crate; neither engine crate depends directly on the other's concrete implementation.

**Rationale:** Keeps engine ownership separate, reduces circular-dependency risk, and supports isolated unit testing and independent source evolution.

**Critical clarification:** Compile-time crate dependency is not the same as runtime calling. DSN-001 may still call a library-resolution capability implemented by DSN-002 through an explicit interface, dependency injection, or adapter. The exact interface and wiring remain to be designed.

## Guardrails
- No circular crate dependencies.
- No direct mutation or access to another engine's private state.
- Keep shared-contract types limited to genuinely shared boundary concepts.
- Do not confuse compile-time crate dependencies with runtime call direction.
- Do not decide the Python-to-Rust bridge, UI framework, or service transport under A6; those belong to A7 or later decisions.

**Approval:** Owner selected Option 1 in conversation on 2026-10-13.  
**Not implied:** This does not finalize crate names, exact interface signatures, dependency-injection mechanics, or the Python/UI integration bridge.

# A7. UI and automation integration boundary

**Status: PARTIALLY DECIDED — Option 1 approved as the canonical engine API; Python integration approach remains OPEN**

## Decision question
How will the desktop UI and automation tools invoke DSN-001 and DSN-002 while preserving the Rust workspace, in-process engine APIs, strict engine ownership, and shared-contract hub model selected in A1, A2, A4, and A6?

The original decision register marks A7 as deferrable and recommends exposing a Rust API for the sprint, using tests as the demonstration interface, and revisiting UI integration later. This section preserves that recommendation while adding detail to support a later informed choice.

## Existing architecture decisions that constrain A7
- **A1:** Rust workspace; keep engine APIs in-process initially; do not commit prematurely to UI or service transport.
- **A2:** separate DSN-001 and DSN-002 engine crates; prioritize debugging, verification, maintainability, and evidence-led evolution over speed at any cost.
- **A4:** dedicated shared-contract crate; Rust-native internal contract initially, while external library formats/importers can remain language-neutral.
- **A6:** both engines depend on shared-contract types; neither depends directly on the other's concrete implementation. Runtime calls can still cross an explicit interface.
- **Product direction:** desktop software, not browser-first.
- **Automation direction:** Python orchestration/harness work is being considered or planned, but the exact Python-to-Rust bridge is not yet approved.

## Fit with the wider WREATH architecture
The project context describes a common engine harness responsible for typed request/response contracts, engine registration and capability discovery, job lifecycle and priority, preconditions, timeouts, cancellation, retry/idempotency policy, structured events and logs, metrics/tracing, resource budgets, failure classification/recovery, artifact and partial-result handling, and reproducible execution metadata. These are the wider harness responsibilities, not a requirement to implement every item in the first slice.

For the current DSN-001/DSN-002 slice, keep the harness small and evidence-driven. First demonstrate engine operations, explicit errors, integration behavior, and testable outcomes. Add job management, eventing, cancellation, resource controls, and other operational features when the slice's workflows and acceptance tests require them. Do not build a large orchestration framework before there is a working vertical slice.

The project context also says that AI agents may interpret goals and plan work, but deterministic engineering engines must remain responsible for geometry, connectivity, rule evaluation, and other correctness-critical operations. Any Python harness or future agent integration must preserve that invariant.

## Important distinctions
These are separate decisions and should not be collapsed into a single “API” choice:

1. **Canonical engine API:** the public Rust operations and domain semantics that engine consumers use.
2. **Adapter/bridge:** how a particular consumer invokes the Rust API, such as direct Rust linkage, Python bindings, or a CLI process.
3. **Orchestration harness:** workflow sequencing, retries, test execution, result collection, and automation policy. Python may own this layer; it must not become the source of truth for EDA engineering rules.
4. **User interface:** the desktop UI and its interaction model. The UI framework is not selected by A7.
5. **Deployment boundary:** in-process calls, local processes, or remote services. A1/A2 prefer in-process engine APIs initially; this does not forbid adding a justified adapter later.
6. **Data contract:** shared types and semantics. A5 concerns this contract, not every callable function or orchestration step.

## Owner decision and rationale (2026-10-13)

**Approved:** Option 1 is locked as WREATH's canonical engine API foundation. DSN-001 and DSN-002 will expose documented Rust APIs, and the first engine behavior should be testable through Rust unit/integration tests. This does **not** mean that Rust must be the only way every future consumer can call the engines.

**Still undecided:** whether WREATH should add Option 4 (FFI/language bindings, potentially for Python) as a consumer adapter, and whether Python needs engine access in the first implementation slice. The owner plans to discuss the trade-offs with teammates before making that decision. Do not treat Option 4 as approved or rejected.

### Human rationale for Option 1 — approved
- Rust APIs provide a strong, type-checked foundation for the native engines.
- Direct calls support debugging and tracing through engine code without adding a process or serialization boundary to internal calls.
- This fits the Rust workspace and desktop-first product direction.
- Keeping a canonical Rust API avoids making the Python bridge, UI framework, or other consumer technology dictate the engines' domain design.
- It provides a stable foundation on which a CLI or language binding could later be built, if evidence and acceptance requirements justify one.

### Human rationale for Option 4 — remains under consideration
- Bindings could let Python call engine functions directly, which may be convenient if the automation harness needs frequent calls or rich in-memory data exchange.
- Calls can avoid repeated subprocess startup and some serialization overhead associated with a CLI.
- However, bindings add maintained boundary code and packaging work, and require explicit decisions about Rust-to-Python type conversion, error/diagnostic mapping, ownership, threading, and compatibility across Python versions and platforms.
- These trade-offs need teammate discussion and, if useful, a small representative prototype. No binding framework such as PyO3/maturin is selected by this record.

**Scope of this decision:** Option 1 is approved for the canonical engine API. Option 4 is an open candidate for Python integration. This record does not select the Python bridge, require Python in the first slice, approve a UI framework, or approve WASM.

## Option 1 — Rust API only / direct native integration

**Meaning:** Keep public Rust APIs as the only consumer-facing integration path for the first slice. Rust tests and integration tests exercise the engines. A native desktop application written in Rust could link the crates directly.

**Advantages**
- Lowest conceptual and operational complexity for Rust-native consumers.
- Strong compile-time type checking and direct error propagation.
- Easier debugging, profiling, and stepping across engine code.
- No serialization boundary or subprocess lifecycle for direct calls.
- Keeps the canonical API close to domain types and engineering invariants.
- Good fit for the initial Rust workspace and in-process direction.

**Costs and limitations**
- Python cannot call ordinary Rust functions directly merely because the crates exist; Python needs bindings, a CLI, or another bridge.
- A UI written in another language will also need a bridge.
- A Rust-only API does not prove that the planned Python automation workflow can invoke the engines end-to-end.
- If consumers later require independent processes or remote execution, an adapter and potentially a versioned protocol will be needed.

**Best fit:** Initial engine development and Rust acceptance tests, especially if Python integration can safely follow after the core APIs stabilize.

**Risk to avoid:** Calling this “Rust API only” must not accidentally mean Python automation is guaranteed to be unnecessary. It only means no non-Rust bridge is committed in this decision.

## Option 2 — WebAssembly (WASM) target

**Meaning:** Compile some or all engine functionality to WASM for execution in a web UI or another WASM runtime.

**Advantages**
- Can enable browser execution and sandboxed deployment.
- May provide a portable execution target where the required runtime and dependencies are supported.
- Could be useful for selected deterministic, self-contained computations.

**Costs and limitations**
- The current product direction is desktop-first, not browser-first, so WASM solves no established requirement yet.
- Native libraries, filesystem access, threading, long-running jobs, and platform-specific integrations may require adaptation or may be unavailable in a target runtime.
- A WASM target is not itself a complete UI integration architecture and does not automatically solve Python orchestration.
- Supporting both native and WASM builds adds compilation, testing, and compatibility surface.
- EDA workloads may need filesystem, package-library, or process capabilities that complicate browser execution.

**Best fit:** A concrete future web/browser requirement or a well-isolated engine subsystem that benefits from WASM.

**Risk to avoid:** Selecting WASM merely because it sounds portable or modern. Portability is useful only when the target and required capabilities are real.

## Option 3 — Command-line interface (CLI)

**Meaning:** Provide executable commands that Python scripts, CI jobs, and developers can invoke. The CLI calls the Rust engine APIs internally and exposes structured inputs and outputs.

**Advantages**
- Straightforward to invoke from Python and common automation systems.
- Process isolation can make failures and test scenarios easier to reproduce.
- Language-neutral invocation boundary; Python does not need to load Rust directly.
- Easy to run manually, in CI, and in scripted integration tests.
- Can later become a useful diagnostic or batch interface even if bindings are also introduced.

**Costs and limitations**
- Process startup and input/output parsing add overhead, especially for many tiny calls.
- A robust contract is needed for arguments, structured output, exit codes, diagnostics, and version reporting.
- Long-running operations need defined progress, cancellation, timeout, and partial-result behavior.
- Large in-memory objects may be expensive to serialize repeatedly.
- Subprocess execution and packaging need careful handling across operating systems.
- A CLI is a consumer adapter, not a replacement for clean engine APIs.

**Best fit:** Batch automation, reproducible integration tests, CI, and Python workflows that naturally operate in discrete commands or jobs.

**Risk to avoid:** Building a large ad-hoc command surface that leaks internal data structures or becomes the only place where workflow logic lives.

## Option 4 — FFI / language bindings

**Meaning:** Expose a stable boundary that lets another language call Rust functionality. For Python, a binding framework such as PyO3/maturin could be evaluated as one implementation approach; this is an example, not an approved technology choice. The original register describes a C-compatible FFI.

**Advantages**
- Python can call operations without launching a separate CLI process for each call.
- Convenient for interactive automation and passing results between workflow steps.
- Can reduce repeated serialization and process-startup overhead.
- Can provide a typed, ergonomic Python-facing API while preserving Rust as the implementation language.
- Potentially supports richer progress/callback interactions than a simple one-shot CLI.

**Costs and limitations**
- Binding code and packaging become additional maintained surface.
- Rust types do not automatically become appropriate or stable Python types; an explicit mapping layer is required.
- Exceptions/errors, ownership, lifetimes, callbacks, cancellation, and thread-safety need deliberate rules.
- Native extension builds and Python/Rust version/platform combinations complicate distribution.
- ABI stability is a concern for a manually maintained C ABI; high-level binding frameworks have different trade-offs but do not remove packaging and API-design work.
- Debugging across language boundaries may be more involved than direct Rust calls.

**Best fit:** Frequent, interactive Python-to-engine calls where direct process invocation is awkward or where in-memory object exchange materially helps.

**Risk to avoid:** Exposing internal Rust structures directly and then finding every refactor has become a cross-language breaking change.

## Comparison summary

| Criterion | Rust API only | WASM | CLI | FFI / bindings |
|---|---|---|---|---|
| Initial implementation complexity | Low | Medium to high, depending on dependencies | Low to medium | Medium to high |
| Python invocation | Not directly | Not automatically | Strong fit | Strong fit |
| Direct native Rust debugging | Best | More runtime-specific | Good inside process; process boundary outside | Cross-language boundary adds complexity |
| Process isolation | No for direct linked calls | Runtime-dependent | Yes | Usually no |
| Per-call overhead | Low | Runtime-dependent | Higher due to process/I/O | Usually low |
| Long-running jobs/progress | In-process API design needed | Runtime-dependent | Must design protocol | Must design binding/callback model |
| Browser use | No by itself | Best fit | No | No by itself |
| Desktop-first fit today | Strong core foundation | No established need | Useful for automation | Useful if Python needs tight integration |
| Packaging complexity | Lowest for Rust-only app | Adds target/runtime concerns | Executable distribution | Native extension/binding distribution |
| Best role | Canonical engine API and tests | Future browser/WASM target | Automation/CI adapter | In-process Python adapter |

The ratings are qualitative and depend on workload, platform, and implementation choices; they are not benchmark results.

## The three practical sub-decisions

### A7.1 — When must Python invoke the engines?
- **In the first implementation slice:** Python-to-Rust integration is an acceptance requirement. Select or prototype a bridge early and add an end-to-end test that invokes both engines through it.
- **Later, after Rust APIs and tests work:** keep the first slice Rust-native; add the Python bridge once the public operations and errors are stable enough to wrap.
- **Undecided:** preserve a bridge-neutral Rust API and do not claim Python automation has been integration-tested. Record the limitation in the slice's acceptance evidence.

### A7.2 — If Python is required immediately, which bridge should be evaluated?
- **Bindings first:** appropriate when workflows need frequent calls, in-memory data exchange, and interactive operations. Costs include binding and packaging complexity.
- **CLI first:** appropriate when workflows are job-oriented, reproducibility and isolation matter, and inputs/results can be represented as structured messages.
- **Prototype and compare both:** strongest evidence-led choice when uncertainty is material. Build a deliberately small vertical slice, measure implementation/debugging effort and operation overhead, test error propagation and packaging, then decide. Do not build two full production adapters just to compare them.
- **Keep bridge undecided:** valid while Python timing is undecided, but it leaves the integration requirement unresolved. Define the trigger and evidence needed to revisit it.

### A7.3 — Should UI and automation share one adapter?
- **One shared adapter where practical:** reduces duplicated integration logic if UI and automation have similar needs and lifecycle requirements. A shared adapter must not force UI-specific concerns into batch workflows or vice versa.
- **Separate adapters over the same engine APIs:** permits each consumer to have a suitable interaction model, such as UI progress/cancellation versus batch CLI jobs. This adds adapter code but can preserve cleaner consumer-specific behavior.
- **Decide later:** reasonable while the UI framework and Python bridge are both open. Preserve stable public engine APIs and avoid duplicating domain logic, so the choice remains reversible.

These sub-decisions are related but not identical. Choosing Python in the first slice does not automatically choose CLI or bindings; choosing bindings does not automatically require the UI to use the same adapter.

## Recommended decision posture

**Recommendation, not owner approval:** keep the public Rust engine APIs canonical and use Rust unit/integration tests as the first proof of engine behavior. Decide whether Python must participate in the first implementation slice based on the acceptance matrix and actual harness requirements. If it must, compare a minimal CLI path and a minimal Python-binding path with the same representative workflow before selecting a production bridge. Do not choose WASM absent a concrete browser/WASM requirement.

This recommendation aligns with the original decision register's recommendation to defer detailed UI integration, expose Rust APIs for the sprint, and revisit CLI/UI integration later. It does not assume that Python is optional forever.

## Evidence required before locking a bridge
Use one representative end-to-end workflow that:
1. invokes a DSN-002 library-resolution operation;
2. passes the resolved definition/reference into a DSN-001 operation through the approved boundary;
3. captures a structured success result and a structured failure;
4. tests missing/invalid definitions without silent corruption;
5. reports diagnostics and operation status;
6. exercises timeout/cancellation/progress only if the initial slice requires them;
7. runs reproducibly on the supported developer/CI environment;
8. documents packaging and version compatibility assumptions.

Compare options using implementation complexity, debugging experience, error fidelity, throughput for the expected call pattern, packaging friction, and how much consumer-specific code is required. Avoid inventing performance requirements before representative measurements exist.

## Guardrails
- Preserve Rust engine APIs as the canonical domain interface unless evidence justifies a change.
- Do not let the Python harness own or duplicate EDA engineering rules.
- Do not select WASM or a network service without a concrete requirement.
- Keep shared-contract types distinct from engine operations and orchestration workflows.
- A7 does not approve a UI framework, network protocol, service architecture, or independent binary-upgrade capability by implication.
- If any A7 sub-decision remains undecided, record it as open, state what is deliberately deferred, and name the evidence or trigger required to revisit it.
- A7 is partially decided: Option 1 is approved as the canonical engine API; the consumer bridge and other A7 sub-decisions remain open.

## Owner decision record
- Option 1 / canonical Rust engine API: **APPROVED and locked as the foundation**.
- Option 4 / FFI or language bindings: **OPEN; under consideration as a possible Python adapter, pending teammate discussion**.
- Python invocation timing: **OPEN**.
- Production Python-to-Rust bridge: **OPEN; no binding framework or CLI choice approved**.
- Shared versus separate consumer adapters: **OPEN**.
- WASM target: **not selected; revisit only with a concrete requirement**.
- Decision boundary: **Option 1 approval establishes the canonical engine API; it does not mean Rust API is the only future consumer interface, and it does not approve Option 4**.
- Harness scope: **small and acceptance-driven for the first vertical slice; expand job/event/recovery infrastructure only when demonstrated requirements justify it**.
- Correctness boundary: **Python orchestration and LLM planning must not replace deterministic engine validation**.



## Communication protocol clarification — proposed, pending teammate review

- The canonical engine API remains a Rust API.
- For the initial in-process MVP, DSN-001 and DSN-002 communicate through direct Rust calls and explicit Rust interfaces. Do not add gRPC merely to connect crates that run in the same process.
- gRPC is a candidate for future process boundaries or remote services if those requirements arise.
- gRPC service definitions, protobuf schemas, compatibility/versioning, deployment, and whether a process boundary is needed remain open.
- This clarification does not approve a separate-process architecture for the initial MVP.
# A8. KiCad reference usage, compatibility, and licensing

**Status: OPEN — proposed direction prepared for teammate review; not owner-approved**

**Priority:** Required before implementation choices that depend on KiCad-derived material or compatibility commitments  
**Decision owners listed in the original template:** Project Lead and Shivam  
**Date prepared:** 2026-10-13

## Problem

Define how WREATH may use KiCad as a reference, what compatibility it intends to provide, and what legal/provenance checks are required before any source-derived material is used. Keep three questions separate:

1. **Research method:** documentation, observable behavior, file-format analysis, and potentially source-code inspection.
2. **File compatibility:** which KiCad schematic and library formats and versions WREATH will read and/or write.
3. **Behavioral compatibility:** which specific behaviors WREATH will compare against KiCad, such as ERC, netlist generation, symbol/library resolution, and related edge cases.

These are separate axes. Choosing a research method does not automatically promise compatibility, and choosing a compatibility target does not authorize copying source code.

## Options from the decision template

### Option 1 — Reference only; implement independently

Study public documentation and observable behavior, then implement WREATH independently. By itself, this option does not promise KiCad file-format compatibility or broad behavioral parity.

### Option 2 — File-format compatible

Support explicitly selected KiCad schematic and/or library formats. The scope must name the format families, versions, read/write direction, unsupported constructs, and round-trip expectations. “KiCad compatible” without this scope is too vague to test.

### Option 3 — Behavioral parity

Match explicitly selected KiCad behaviors using a defined reference version and a reproducible comparison corpus. Candidate areas include ERC results, netlist output, symbol/library resolution, and relevant geometry or connectivity behavior. Parity must be scoped per behavior; it should not be interpreted as a promise to reproduce all of KiCad.

### Option 4 — Independent; no compatibility goal

Use WREATH-defined formats and behavior without a KiCad compatibility commitment. This does not prevent adding selected compatibility later, but it removes compatibility as a current acceptance requirement.

## Proposed direction for discussion, not approval

The proposed long-term direction is to combine **Option 1 as the research/implementation principle** with **Options 2 and 3 as separately scoped compatibility goals**:

- Implement WREATH's engines, domain model, and shared contracts independently.
- Research KiCad documentation and observable behavior.
- Consider KiCad source-code inspection only after the project completes and records its licensing/provenance review.
- Add import/export support for specifically selected KiCad schematic and library formats in a defined phase.
- Add behavioral comparisons for selected behaviors and a named reference version in a defined phase.
- Do not promise universal file compatibility or full behavioral parity.
- Treat Option 4 as an alternative product direction if the team decides that compatibility cost outweighs its value.

This proposal does **not** mean Options 1, 2, and 3 are all approved. The owner and teammates must agree on the intended scope, sequence, and acceptance criteria.

## Research, independent implementation, and repository policy

**Proposed strict policy — pending owner and teammate approval**

### Permitted research inputs

- Approved documentation.
- Public file-format specifications.
- Observable application behavior.
- Independently created test cases and test results.

**No KiCad source-code inspection for now.** Agents must not download or clone the KiCad source repository, inspect its source code, or use source code as an implementation reference unless the project later changes this policy through an explicit human decision and appropriate licensing review.

### Independent implementation requirement

Agents must independently author WREATH implementation code. They must not copy, paste, port, translate, adapt, or otherwise incorporate KiCad source code or source-derived implementation artifacts. Compatibility with a file format or selected behavior does not authorize code reuse.

### Strict repository rule

The proposed repository rule is: **no KiCad mentions or KiCad-specific research records anywhere in the WREATH Git repository**, including source comments, code, documentation, commit messages, branches, pull requests, issue templates, test names, fixtures, compatibility reports, and migration notes.

Implementation and tests may describe required file-format behavior in vendor-neutral terms where technically accurate. Do not make false or misleading claims about provenance, and do not disguise copied or adapted material by renaming it or removing references.

This repository restriction is a project hygiene rule, not a method for avoiding legal obligations. Any necessary legal, licensing, or provenance records must be truthful and maintained in an approved location outside the repository, with appropriate access controls and retention. Do not conceal, destroy, or falsify records to avoid licensing obligations.

### Stop-and-escalate rule

If an agent encounters KiCad source material, cannot establish that a proposed artifact is independently authored, or is uncertain about licensing or provenance, it must stop before writing or committing the affected artifact and request human review. Agents may not override this rule through task prompts, subagent instructions, workflow configuration, or compatibility requirements.

No agent may download, clone, inspect, copy, or incorporate KiCad source under the current policy. Any future exception requires an explicit owner decision and licensing/provenance review before the activity occurs.

## Impact by subsystem

- **DSN-001 schematic engine:** decide whether WREATH's native schematic model remains independent and which KiCad schematic formats, if any, must be imported/exported.
- **DSN-002 component/library engine:** decide which symbol, footprint, pad, pin-mapping, and library formats, if any, must be supported.
- **Shared contract:** define WREATH identity and geometry semantics from explicit requirements. Do not inherit KiCad assumptions accidentally merely because a format adapter needs to translate them.
- **Verification harness:** maintain versioned fixtures and comparisons for each supported format and selected behavior. Separate file-format conformance tests from behavioral-parity tests.
- **Migration/provenance:** document source references, format/version assumptions, compatibility decisions, and licensing review outcomes.

## Required evidence and follow-up work

1. Name the KiCad reference version(s) before establishing compatibility baselines.
2. Inventory the schematic and library formats relevant to the first supported use cases.
3. Define read/write scope and unsupported constructs for each selected format.
4. Build a legally reviewed fixture corpus and reproducible round-trip tests.
5. Select behavioral comparison targets, expected differences, and pass/fail criteria for ERC, netlist, and library resolution as applicable.
6. Complete the source inspection/licensing/provenance review before any KiCad source is downloaded, cloned, copied, or incorporated.
7. Record which items are first-slice requirements and which are later-phase goals.

## Decision record

- **Final decision:** OPEN
- **Rationale:** Pending owner and teammate discussion.
- **Approved by:** Not recorded.
- **Approval date:** Not recorded.
- **Follow-up owner:** To be assigned during review.

---

## Cross-decision rules

1. **Domain ownership is separate from deployment.** Separate crates do not automatically mean separate processes or independently upgradeable binaries.
2. **Library data is separate from engine code.** Community packages should be addable without changing/recompiling engines.
3. **Format neutrality is separate from API neutrality.** External library formats can be language-neutral while the initial in-process contract remains Rust-native.
4. **No silent design changes.** Existing instances preserve their original definition state; updates require review and validation.
5. **Explicit errors over silent corruption.** Missing, malformed, ambiguous, or incompatible definitions must be surfaced.
6. **Proposals are not approvals.** Record a decision as approved only after explicit owner approval.
7. **Do not infer unmade decisions.** UI framework, persistence format, exact schemas, IPC/network transport, and future process boundaries remain open unless separately approved.
8. **Keep evidence and history.** Record rationale, alternatives, known costs, constraints, and later revisions.

## Known unresolved project matters

Not resolved by A1–A4:
- Authoritative versioned WHIR schema and its relationship to the proposed contract.
- Repository licensing and legal/provenance disposition for compatibility research.
- KiCad reference version and parity criteria/baselines.
- Exact component and footprint interchange formats for the first release.
- Exact identity, geometry, persistence, error, and revision schemas.
- UI framework and future external transport requirements.
- Ticket authority and acceptance-baseline ratification where still unresolved.

## Decision history
| 2026-10-13 | A7/A8 clarification draft | Direct Rust calls for the in-process MVP; gRPC reserved as a future process-boundary candidate. A8 proposes documentation/format/behavior research only, no source inspection, and no KiCad mentions or KiCad-specific records in Git. Pending teammate/owner approval. |


| Date | Decision | Record |
|---|---|---|
| 2026-10-13 | A1 approved | Rust workspace; in-process APIs initially; defer UI/transport commitment. |
| 2026-10-13 | A2 approved | Separate in-process Rust engine crates initially; prioritize quality, debugging, and verification. |
| 2026-10-13 | A3 approved | Strict ownership; preserve original definition state and require explicit review for existing-design updates. |
| 2026-10-13 | A4 approved | Dedicated shared-contract crate; Rust-native contract initially; language-neutral library formats and importers. |
| 2026-10-13 | A5 conditional direction approved | Check for authoritative versioned WHIR; if none exists, propose a minimal explicitly versioned integration contract. |
| 2026-10-13 | A6 approved | Shared-contract hub dependency model; runtime interfaces/wiring remain to be specified. |
| 2026-10-13 | A7 partially decided | Canonical Rust engine API approved; Python bridge and other consumer integration details remain open. |
| 2026-10-13 | A8 discussion draft prepared | Options 1, 2, and 3 proposed as a phased direction for teammate review; not approved. Source inspection/reuse remains subject to licensing/provenance review. |

## Review checklist for teammates

- [ ] Which engine owns this domain data and mutation?
- [ ] Is cross-engine communication using the public contract?
- [ ] Can a community package be added without changing engine source?
- [ ] Are format-specific conversions isolated in importers/adapters?
- [ ] Are identity and revision references explicit?
- [ ] Could this silently alter an existing design? If yes, stop and resolve the update policy.
- [ ] Are failures explicit and recoverable?
- [ ] Is the decision status recorded accurately?
- [ ] Are tests tied to acceptance criteria and do they demonstrate the actual obligation?
