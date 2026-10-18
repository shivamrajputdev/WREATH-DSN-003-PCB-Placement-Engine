**## [B1] Library Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Shivam (primary), Project Lead (review)



**\*\*Problem:\*\*** How is a library uniquely identified? By name, path, UUID, or URI?



**\*\*Options:\*\***

1\. **\*\*String name\*\*** — Simple, human-readable, risk of collision.

2\. **\*\*UUID\*\*** — Globally unique, opaque.

3\. **\*\*URI / namespaced path\*\*** — Structured, supports multiple sources.



**\*\*Recommendation:\*\*** Namespaced string identifier (e.g., \`wreath:standard/resistors\`) with optional UUID for persistence. Provides human readability with uniqueness guarantees.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Must store library references in schematic data.  

**\*\*Impact on DSN-002:\*\*** Defines the primary key for library lookup.  

**\*\*Impact on shared contracts:\*\*** Library ID type must be in shared contracts.  

**\*\*Evidence or tests required:\*\*** Round-trip test preserving library identity.  

**\*\*Follow-up tasks:\*\*** Define the ID type in shared contracts.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B2] Component Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Shivam (primary), Project Lead (review)



**\*\*Problem:\*\*** How is a component definition uniquely identified within and across libraries?



**\*\*Options:\*\***

1\. **\*\*Library-scoped name\*\*** — \`(library_id, component_name)\` tuple.

2\. **\*\*Global UUID\*\*** — Independent of library.

3\. **\*\*Content-addressed hash\*\*** — Identity derived from definition content.



**\*\*Recommendation:\*\*** Library-scoped name as the primary identifier, with an optional UUID for stable cross-reference. Content hashing can serve as a revision check.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Schematic instances reference components by this ID.  

**\*\*Impact on DSN-002:\*\*** Must ensure uniqueness within a library.  

**\*\*Impact on shared contracts:\*\*** Component ID type must be in shared contracts.  

**\*\*Evidence or tests required:\*\*** Uniqueness test, round-trip preservation test.  

**\*\*Follow-up tasks:\*\*** Define ComponentId in shared contracts.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B3] Symbol Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Shivam (primary), Project Lead (review)



**\*\*Problem:\*\*** How is a symbol definition identified? Is it always tied to a component, or can symbols be independent?



**\*\*Options:\*\***

1\. **\*\*Component-scoped\*\*** — Symbol is always part of a component definition.

2\. **\*\*Independent with association\*\*** — Symbols exist independently; components reference them.

3\. **\*\*Merged\*\*** — Component and symbol are the same entity.



**\*\*Recommendation:\*\*** Component-scoped for the initial sprint. A component has one or more symbol representations. Independent symbols add complexity without clear sprint benefit.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Determines how placed instances reference their visual representation.  

**\*\*Impact on DSN-002:\*\*** Determines symbol storage and lookup model.  

**\*\*Impact on shared contracts:\*\*** Symbol ID type and its relationship to ComponentId.  

**\*\*Evidence or tests required:\*\*** Test that symbol identity survives save/load.  

**\*\*Follow-up tasks:\*\*** Define SymbolId in shared contracts.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B4] Pin Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Shivam (primary), Project Lead (review)



**\*\*Problem:\*\*** How are pins on a symbol uniquely identified? By name, number, index, or UUID?



**\*\*Options:\*\***

1\. **\*\*Name string\*\*** — e.g., "VCC", "GND", "A0". Human-readable but must be unique per symbol.

2\. **\*\*Numeric index\*\*** — Positional, fragile if pins are reordered.

3\. **\*\*Pin number string\*\*** — Matches physical pin numbers (e.g., "1", "2", "A1").

4\. **\*\*UUID\*\*** — Stable across renames but opaque.



**\*\*Recommendation:\*\*** Pin number string as primary identifier (matching physical component), with a human-readable name as metadata. Pin numbers must be unique within a symbol unit.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Wire connections reference pin IDs; stability is critical for netlisting.  

**\*\*Impact on DSN-002:\*\*** Must enforce pin ID uniqueness within a symbol.  

**\*\*Impact on shared contracts:\*\*** PinId type definition.  

**\*\*Evidence or tests required:\*\*** Connectivity survives pin identity round-trip.  

**\*\*Follow-up tasks:\*\*** Define PinId in shared contracts.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B5] Footprint Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer (not needed for schematic-only sprint slice)  

**\*\*Decision owner(s):\*\*** Shivam



**\*\*Problem:\*\*** How are footprint definitions identified?



**\*\*Options:\*\***

1\. **\*\*Library-scoped name\*\*** — Similar to component identity.

2\. **\*\*UUID\*\*** — Globally unique.

3\. **\*\*Content hash\*\*** — Based on geometry.



**\*\*Recommendation:\*\*** Library-scoped name, consistent with component identity scheme. Defer detailed design until footprint integration is in scope.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Minimal — schematic engine doesn't directly use footprints.  

**\*\*Impact on DSN-002:\*\*** Defines footprint storage and lookup.  

**\*\*Impact on shared contracts:\*\*** FootprintId type if needed in contract.  

**\*\*Evidence or tests required:\*\*** Defer.  

**\*\*Follow-up tasks:\*\*** Defer until footprint work begins.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B6] Pad Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer  

**\*\*Decision owner(s):\*\*** Shivam



**\*\*Problem:\*\*** How are pads within a footprint identified?



**\*\*Options:\*\***

1\. **\*\*Pad number string\*\*** — Matches physical pad numbers.

2\. **\*\*Positional index\*\*** — Order-dependent.

3\. **\*\*UUID\*\*** — Stable but opaque.



**\*\*Recommendation:\*\*** Pad number string, consistent with pin identity. Defer until footprint work is in scope.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Minimal.  

**\*\*Impact on DSN-002:\*\*** Defines pad storage model.  

**\*\*Impact on shared contracts:\*\*** PadId type if needed.  

**\*\*Evidence or tests required:\*\*** Defer.  

**\*\*Follow-up tasks:\*\*** Defer.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B7] Placed-Instance Identity**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead (primary), Shivam (review)



**\*\*Problem:\*\*** How is a placed instance of a component on a schematic uniquely identified?



**\*\*Options:\*\***

1\. **\*\*UUID\*\*** — Assigned at placement, stable forever.

2\. **\*\*Reference designator\*\*** — e.g., "R1", "U3". Human-readable but can change.

3\. **\*\*Both\*\*** — UUID for internal identity, refdes for user display.



**\*\*Recommendation:\*\*** UUID as the stable internal identifier; reference designator as mutable user-facing metadata. This separates identity from labeling.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Core data model decision — all instance operations use this ID.  

**\*\*Impact on DSN-002:\*\*** Minimal — instances are DSN-001's responsibility.  

**\*\*Impact on shared contracts:\*\*** InstanceId type may appear in resolution requests.  

**\*\*Evidence or tests required:\*\*** Identity stable across save/load cycles.  

**\*\*Follow-up tasks:\*\*** Define InstanceId in DSN-001.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B8] Reference Designator Rules**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer  

**\*\*Decision owner(s):\*\*** Project Lead



**\*\*Problem:\*\*** How are reference designators (R1, C2, U1) assigned, validated, and renumbered?



**\*\*Options:\*\***

1\. **\*\*Manual only\*\*** — User assigns all refdes.

2\. **\*\*Auto-assign with user override\*\*** — System assigns, user can rename.

3\. **\*\*Prefix-based auto-increment\*\*** — Based on component type prefix.



**\*\*Recommendation:\*\*** Auto-assign with user override. Defer detailed rules until after the basic integration works.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Must implement refdes assignment and validation logic.  

**\*\*Impact on DSN-002:\*\*** May need to provide default prefix per component type.  

**\*\*Impact on shared contracts:\*\*** Minimal.  

**\*\*Evidence or tests required:\*\*** Defer.  

**\*\*Follow-up tasks:\*\*** Implement after core integration.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B9] Identity Stability Across Save/Load and Library Updates**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** When a schematic is saved and reloaded, or when a library component is updated, do all identifiers remain stable?



**\*\*Options:\*\***

1\. **\*\*Strict stability\*\*** — IDs never change across save/load.

2\. **\*\*Regenerated\*\*** — IDs are reassigned on load (fragile).

3\. **\*\*Stable with migration\*\*** — IDs are stable; format changes include migration logic.



**\*\*Recommendation:\*\*** Strict stability. All IDs (instance, component reference, pin reference) must survive save/load cycles unchanged. This is a fundamental integrity requirement.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Must serialize and deserialize all IDs faithfully.  

**\*\*Impact on DSN-002:\*\*** Must preserve library identity across updates.  

**\*\*Impact on shared contracts:\*\*** Identity types must be serialization-friendly.  

**\*\*Evidence or tests required:\*\*** Round-trip test: save → load → compare all IDs.  

**\*\*Follow-up tasks:\*\*** Add save/load round-trip test to acceptance criteria.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [B10] Collision and Duplicate-ID Behavior**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** What happens when two entities have the same ID? Is it an error, a merge, or silently accepted?



**\*\*Options:\*\***

1\. **\*\*Hard error\*\*** — Reject duplicates immediately.

2\. **\*\*Last-write-wins\*\*** — Overwrite silently.

3\. **\*\*Error with recovery\*\*** — Flag the collision, offer resolution options.



**\*\*Recommendation:\*\*** Hard error for the sprint. Duplicate IDs indicate a bug or corruption. Fail loudly rather than silently corrupting data.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Must validate instance ID uniqueness.  

**\*\*Impact on DSN-002:\*\*** Must validate component/symbol/pin ID uniqueness.  

**\*\*Impact on shared contracts:\*\*** Error type for ID collision.  

**\*\*Evidence or tests required:\*\*** Test that duplicate IDs produce errors.  

**\*\*Follow-up tasks:\*\*** Add negative test for duplicates.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***


---

## Decision recording note

B1, B2, and B3 reflect the Project Lead's decisions recorded on 2026-10-13. The identity choices are decided; implementation details listed as follow-up tasks must be specified before the affected implementation relies on them. B4–B10 remain unchanged and OPEN in this document.


---

# Project Lead Decision Addendum — B4, B5, B7

**Recorded:** 2026-10-13  
**Authority:** Project Lead decision  
**Note:** This addendum is the current decision record for B4, B5, and B7 and supersedes the blank “Final decision” fields in the original template sections. The decisions below are recorded; implementation-specific details called out as follow-up work remain to be specified.

## B4 — Pin Identity

- **Status:** DECIDED — implementation details remain to be specified.
- **Selected option:** Option 3 — Pin number string as the primary identifier (for example, `1`, `2`, `A1`); retain pin name as descriptive metadata.
- **Rationale:** Pin numbers are human-readable and useful for mapping logical pins to physical component pins. Array position must not be used as persistent identity.
- **Required follow-up:** Define `PinId` and its uniqueness scope, especially for multi-unit symbols. Add tests proving connectivity survives save/load and duplicate identifiers are rejected within the defined scope.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## B5 — Footprint Identity

- **Status:** DECIDED — detailed design deferred until footprint integration.
- **Selected option:** Option 1 — Library-scoped footprint name, consistent with the library-scoped identity direction used for components.
- **Rationale:** This is human-readable and consistent with the library lookup model. Detailed footprint identity design is not required for the schematic-only slice.
- **Required follow-up:** Revisit detailed `FootprintId`, rename/move behavior, and whether the ID must appear in shared contracts when footprint integration begins.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## B7 — Placed-Instance Identity

- **Status:** DECIDED.
- **Selected option:** Option 3 — Both: a UUID-backed stable internal `InstanceId` and a separate mutable user-facing reference designator (for example, `R1` or `U3`).
- **Rationale:** Renaming or re-annotating an instance must not change the identity of the placed object. `InstanceId` is distinct from the reusable `ComponentId`.
- **Required follow-up:** Define `InstanceId` in DSN-001, reference-designator uniqueness scope and rename behavior. Add tests proving save/load preserves `InstanceId` and changing the reference designator does not change it.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## Remaining open decisions

B6 (Pad Identity), B8 (Reference Designator Rules), B9 (Identity Stability Across Save/Load and Library Updates), and B10 (Collision and Duplicate-ID Behavior) remain OPEN. B8 must remain consistent with B7: the reference designator is mutable metadata, not the placed instance's stable identity.


---

# Project Lead Decision Addendum — B6, B8, B9

**Recorded:** 2026-10-13  
**Authority:** Project Lead decision

## B6 — Pad Identity

- **Status:** DECIDED — detailed design deferred until footprint integration.
- **Selected option:** Option 1 — Pad number string as the primary identifier.
- **Rationale:** A readable pad number is consistent with the chosen pin-number direction. Pad identity rules must still account for footprints where the same pad number may occur more than once.
- **Required follow-up:** When footprint integration begins, define the uniqueness scope, duplicate-number behavior, and whether a separate internal pad identity is needed. Do not use list position as persistent identity.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## B8 — Reference Designator Rules

- **Status:** DECIDED — detailed numbering rules deferred.
- **Selected option:** Option 2 — Auto-assign reference designators with user override.
- **Rationale:** This supports efficient placement and automation while preserving user control. The reference designator remains mutable metadata and does not replace the stable `InstanceId` selected in B7.
- **Required follow-up:** Define numbering prefixes, assignment timing, uniqueness scope, duplicate handling, rename behavior, and undo/redo semantics before implementing the full annotation workflow.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## B9 — Identity Stability Across Save/Load and Library Updates

- **Status:** DECIDED — strict stability is the normal persistence invariant.
- **Selected option:** Option 1 — IDs remain unchanged across ordinary save/load cycles.
- **Rationale:** Stable identity protects references and connectivity. Ordinary loading must not regenerate identifiers. Any future schema migration must be explicit and tested; library updates must not silently replace the identity of placed schematic instances.
- **Required follow-up:** Add round-trip tests comparing IDs and relationships. Test schema migrations separately from ordinary save/load. Define behavior for references that no longer resolve after a library update.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## Remaining open decision

B10 (Collision and Duplicate-ID Behavior) remains OPEN and is presented below for review.


---

# B10 — Collision and Duplicate-ID Behavior

**Status:** OPEN  
**Priority:** Required before implementation  
**Decision owners in source:** Project Lead, Shivam

## Problem

What happens when two entities have the same identifier? Should the engine reject the data, overwrite one entity, merge them, or offer recovery?

This affects all identity decisions, including library IDs, component IDs, symbol IDs, pin identifiers, pad identifiers, placed-instance IDs, and other entities where uniqueness is required. The uniqueness scope must be defined for each entity type; not every identifier is necessarily globally unique.

## Options from the source

### Option 1 — Hard error

Reject duplicates immediately.

**Advantages**
- Prevents ambiguous lookups and silent data corruption.
- Makes broken invariants visible during validation.
- Simple and deterministic for the initial implementation.

**Trade-offs**
- Invalid imported or edited data cannot be accepted without correction.
- Error reporting must identify the conflicting IDs and relevant entity scope.

### Option 2 — Last-write-wins

The newer entity overwrites the earlier one.

**Advantages**
- May appear convenient in some update workflows.

**Risks**
- Can silently discard user data.
- Makes results order-dependent.
- Can break references or conceal corruption.
- Is unsafe as a general identity-collision policy for schematic and library entities.

### Option 3 — Error with recovery

Report the collision and offer explicit resolution choices.

**Advantages**
- Can support interactive import or repair workflows.
- Allows recovery without silently choosing which entity wins.

**Trade-offs**
- Requires a recovery model and possibly UI support.
- More complex than a deterministic rejection policy.
- Must never silently merge entities that merely share an ID.

## Recommendation in the source

The source recommends **Option 1 — Hard error for the sprint**. Duplicate IDs are treated as a bug or corruption, and the system should fail rather than silently corrupt data.

## My recommendation

Use **Option 1 — Hard error** as the default invariant for the initial MVP. If later import or repair workflows need recovery, add an explicit recovery path as a separate feature. Recovery must not change the default rule or silently overwrite or merge entities.

Clarify that the error applies within the uniqueness scope defined for the relevant entity. For example, a library-scoped component name may be valid in two different libraries, while duplicate `InstanceId` values within the same schematic document should be rejected.

## Impact and validation

- **DSN-001:** Validate placed-instance identity uniqueness and reject ambiguous references.
- **DSN-002:** Validate uniqueness for library, component, symbol, pin, footprint, and pad identifiers within their defined scopes.
- **Shared contract:** Provide a clear, structured error for identity collisions where validation crosses engine boundaries.
- **Tests:** Add negative tests for duplicate IDs and verify the system rejects the conflict without overwriting, merging, or losing existing data.

## Decision record

- **Final decision:** OPEN — awaiting Project Lead decision.
- **Rationale:** Pending.
- **Approved by:** Not recorded.
- **Approval date:** Not recorded.


---

# Project Lead Decision Addendum — B10

**Recorded:** 2026-10-13  
**Authority:** Project Lead decision

## B10 — Collision and Duplicate-ID Behavior

- **Status:** DECIDED — implementation details remain to be specified.
- **Selected option:** Option 1 — Hard error. Reject duplicate identifiers within the defined uniqueness scope.
- **Uniqueness scope:** Define uniqueness separately for each entity type and its relevant scope. Do not require all entity types to share one global namespace by default.
- **Rationale:** Silent overwrites or merges can corrupt data and make outcomes depend on processing order. Deterministic rejection is the safer initial invariant and is consistent with B9's identity-stability requirement.
- **Required follow-up:** Define the uniqueness scope for each entity type; specify the structured error payload and when validation occurs; add negative tests proving duplicates are rejected without overwriting, merging, or losing existing data.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## Consistency review — B1 through B10

These decisions are compatible at the policy level, subject to the explicit follow-up details below:

- **B1 Library Identity:** A namespaced library identifier must have a defined collision and normalization policy. Two libraries may share a display name if their canonical identifiers differ.
- **B2 Component Identity:** The `(LibraryId, ComponentName)` lookup key is scoped by library. The optional UUID supplements this key and does not create a second competing primary identity. Define behavior for name collisions within one library and for UUID collisions where UUIDs are present.
- **B3 Symbol Identity:** Symbols are component-scoped, with multiple representations allowed. Define uniqueness of representation IDs within the owning component, and ensure representation references resolve to the correct component.
- **B4 Pin Identity:** Pin number strings are primary identifiers. Define uniqueness scope carefully for multi-unit symbols; do not assume a number is globally unique across all components.
- **B5 Footprint Identity:** Footprints use library-scoped names; detailed uniqueness and rename/move rules remain deferred until footprint integration.
- **B6 Pad Identity:** Pad number strings are primary identifiers; detailed duplicate-number and uniqueness rules remain deferred until footprint integration.
- **B7 Placed-Instance Identity:** Each placed instance has a stable UUID-backed `InstanceId`; reference designators are separate mutable labels. Duplicate instance IDs within the relevant schematic scope are hard errors.
- **B8 Reference Designator Rules:** Auto-assignment with user override must validate duplicate designators within the defined schematic scope. Designator collisions do not change or merge `InstanceId` values.
- **B9 Identity Stability:** Ordinary save/load preserves IDs and relationships. Duplicate IDs must not be silently regenerated or resolved by last-write-wins. Explicit schema migrations require separate rules and tests.
- **B10 Collision Behavior:** Hard error is the default; uniqueness is scoped by entity type and relevant ownership/document scope.

## Remaining implementation details

The decisions do not yet define every concrete uniqueness scope, identifier normalization rule, structured error shape, validation timing, migration mapping, or library-update behavior. Those must be specified in the relevant design/contracts before implementation relies on them. B5 and B6 details remain deferred until footprint integration. B8's detailed numbering policy remains deferred. No global ID namespace is implied by this decision.
