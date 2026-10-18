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

---

# Project Lead Decision Addendum — C1–C4

**Recorded:** 2026-10-13  
**Authority:** Project Lead decision, based on the explicit choices supplied in conversation  
**Repository status:** No repository files modified. This is a separate decision-document copy.

## C1 — Component-to-Symbol Relationship

- **Status:** DECIDED.
- **Selected option:** Option 2 — 1:N. A component may own multiple symbol representations; symbols remain component-scoped.
- **Rationale:** This is consistent with B3, which already selected component-scoped symbols and allowed multiple representations per component. It avoids prematurely introducing cross-component symbol sharing and its ownership/update rules.
- **Scope boundary:** This decision does not itself implement multi-unit symbols, variant selection, De Morgan transformations, or shared symbol definitions across components. C2 separately defers multi-unit support.
- **DSN-001 impact:** A placed component/instance must be able to refer to the appropriate symbol representation when that capability is in scope. Selection semantics are not yet specified.
- **DSN-002 impact:** A component definition can own multiple symbol representations, each with a stable identity within the owning component.
- **Shared-contract impact:** The relationship must allow a component to reference one or more component-scoped symbol representations; exact Rust types and API shape remain open.
- **Validation:** Test that a symbol reference resolves within its owning component and that duplicate representation identifiers in the defined scope are rejected under B10. Broader multi-representation placement tests depend on approved slice scope.
- **Follow-up:** Define symbol-representation identity and uniqueness scope, representation selection/default rules, and save/load behavior before implementing dependent behavior.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C2 — Multi-Unit Symbols and Variants

- **Status:** DECIDED — deferred from the current MVP slice.
- **Selected option:** Option 1 — Defer; the current slice targets single-unit components only.
- **Rationale:** Multi-unit support requires additional modeling and behavior, including unit identity, pin membership, placement, and connectivity rules. Those requirements are not necessary to validate the current schematic/library integration slice.
- **Scope boundary:** C1's 1:N relationship remains valid for multiple symbol representations; it must not be misrepresented as implementation or acceptance of multi-unit components.
- **DSN-001 impact:** The current slice need not implement multi-unit placement/connectivity. Unsupported multi-unit definitions must be reported clearly or rejected explicitly; they must not be silently flattened into incorrect schematic behavior.
- **DSN-002 impact:** The current slice need not implement unit/variant semantics. The data model should not unnecessarily foreclose a future explicit unit model.
- **Shared-contract impact:** Do not add a full multi-unit schema solely for this slice. Avoid assumptions that make a future unit identity impossible; exact forward-compatibility measures remain to be designed.
- **Validation:** No multi-unit feature acceptance is implied for this slice. If such an input is encountered, test clear unsupported/error behavior where that input path exists.
- **Follow-up:** Design and ratify the multi-unit/variant model before that capability enters a later slice.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C3 — Component-to-Footprint Relationship

- **Status:** DECIDED — deferred from the current MVP slice.
- **Selected option:** Option 3 — Defer footprint association.
- **Rationale:** Footprint association is outside the schematic-only slice and aligns with B5's library-scoped footprint identity and B6's pad-number identity direction, both of which defer detailed footprint work.
- **DSN-001 impact:** No footprint association or footprint selection behavior is required for the current slice.
- **DSN-002 impact:** Do not implement a required component-to-footprint relationship in the current slice. Preserve the ability to design such an association later without asserting that the relationship is already defined.
- **Shared-contract impact:** No mandatory footprint-association field or selection API is required solely for the current slice. Future contract changes remain possible through an explicit design decision.
- **Validation:** No footprint-association acceptance is implied for the current slice. Tests must not treat a missing footprint as an error unless an independently approved requirement says otherwise.
- **Follow-up:** Revisit 1:1 versus 1:N footprint association, compatibility constraints, selection/default behavior, and identity/update rules when footprint integration is planned.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C4 — Pin-to-Pad Mapping

- **Status:** DECIDED — deferred until footprint integration.
- **Selected direction:** Defer pin-to-pad mapping implementation until footprint integration is in scope.
- **Rationale:** Mapping is not required to validate the schematic-only slice and depends on footprint and pad semantics that are themselves deferred.
- **DSN-001 impact:** No pin-to-pad mapping behavior is required in the current slice.
- **DSN-002 impact:** Do not implement the mapping model or mapping workflows in the current slice.
- **Shared-contract impact:** No mapping type or mapping API is required solely for the current slice. The future mapping design must preserve stable pin/pad identities and must not infer mappings from list positions or ordering.
- **Validation:** No pin-to-pad mapping acceptance is implied for the current slice. Mapping-specific tests belong to the later footprint-integration slice.
- **Follow-up:** Define mapping cardinality and constraints, unmatched-pin/pad behavior, duplicate mapping validation, multi-unit interactions, and migration/round-trip behavior when footprint integration is designed.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## Consistency review — B1–B10 and C1–C4

### Consistent decisions

- **C1 and B3:** Directly consistent. Both specify component-scoped symbols and allow multiple symbol representations per component. C1 does not reverse B3 or establish cross-component symbol sharing.
- **C1 and B10:** Symbol representation identifiers must be unique within their defined scope. A duplicate identifier is a hard error within that scope; exact scope and structured error details remain to be specified.
- **C1 and B4:** Pin-number strings remain the primary pin identity. A symbol representation's ownership and pin-identity scope must be defined without accidentally treating pin numbers as globally unique across every component.
- **C2 and C1:** Compatible because multiple symbol representations are not the same thing as multi-unit support. The current slice supports the former relationship at the model/design level but defers multi-unit behavior. Exact multi-representation implementation scope must still match the approved sprint slice.
- **C2 and B9/B10:** Deferring multi-unit support does not permit unstable identities or silent collision repair in supported entities. Future schema migration and unit identity rules require explicit design and tests.
- **C3 and B5:** Consistent. B5's library-scoped footprint identity direction remains recorded, but C3 adds no footprint association requirement to the current slice.
- **C4 and B4/B6/B10:** Consistent. Pin numbers and pad numbers are the intended primary identifiers; future mappings must be explicit and duplicate/collision behavior must follow scoped hard-error rules. No positional mapping is implied.
- **C3/C4 and B1/B2/B7/B8:** These decisions do not alter library identity, component identity, placed-instance identity, or reference-designator policy.
- **C3/C4 and B9:** Deferring footprint work does not authorize silent identity replacement or implicit rebinding when footprint support is later added.

### Explicitly unresolved

1. Whether every component must have at least one symbol representation, and how the default/selected representation is chosen.
2. Whether multiple representations are implemented in the first integration slice or only supported by the definition contract; this must be reconciled with the approved DSN-001/DSN-002 slice scope before implementation.
3. Exact `SymbolId` shape, representation identity uniqueness scope, and cross-reference validation timing.
4. How unsupported multi-unit definitions are detected and reported, including whether parsing/loading can preserve them without enabling editing or placement.
5. Future unit/variant semantics, including pin membership, shared pins, unit placement, and connectivity.
6. Whether future component definitions can offer one or multiple footprints, and the compatibility/default/selection policy.
7. Future pin-to-pad mapping cardinality, validation, unmatched entities, multi-unit interactions, and serialization/migration rules.
8. How later additions to the shared contract will be versioned and migrated without violating B9 identity stability.

## Change-control note

These records capture the Project Lead's selected directions. They do not authorize repository edits or implementation by themselves. Before implementation, reconcile C1's 1:N relationship with the approved slice's actual acceptance criteria, and document any deferred behavior explicitly in the slice plan and tests. No repository files were modified when creating this decision-document copy.


---

# C5–C9 Decision Addendum

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Record status:** Selected directions recorded; unresolved implementation details remain open.  
**Repository status:** This is a new decision-document copy. No repository files were modified.

## C5 — Definition and Revision Identity

- **Status:** DECIDED — selected direction; detailed revision rules remain open.
- **Selected option:** Option 2 — Stable definition identity with a revision counter.
- **Decision:** A component definition retains its stable component identity across ordinary definition edits. Changes are represented by a distinct revision/version value rather than by silently changing the identity of the component itself.
- **Rationale:** This aligns with B2, where `(LibraryId, ComponentName)` is the primary component lookup identity and a UUID may supplement it. A content hash may support revision/integrity checks but is not the primary component identity. It also supports B9's requirement for stable identities across normal save/load.
- **Caution:** A revision counter alone is insufficient for C6 pinning if old revisions cannot be retrieved. The design must distinguish the stable component identity from the exact revision referenced by a placed instance.
- **DSN-001 impact:** Instances need a way to retain and resolve the intended definition revision. A revision mismatch must not silently substitute a newer revision.
- **DSN-002 impact:** The library engine must track definition revisions and provide version-specific lookup for revisions that it claims to retain.
- **Shared-contract impact:** The contract must eventually distinguish component identity from revision identity/version. Exact Rust types and API signatures remain open.
- **Validation required:** Tests must show that changing a definition changes its revision indicator while preserving the component's stable identity; a pinned older revision must not resolve to the latest revision by accident.
- **Follow-up:** Define revision numbering, when a revision increments, revision immutability/publication, retention and deletion rules, rename/move behavior, and whether hashes are stored as optional integrity metadata.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C6 — Schematic Instance Pinning Policy

- **Status:** DECIDED — selected direction; exact representation remains open.
- **Selected option:** Option 1 — Pin an exact revision.
- **Decision:** A placed schematic instance references a specific component definition revision. Resolution must not default to the latest revision when the pinned revision is unavailable.
- **Rationale:** This provides reproducible schematic behavior without requiring every instance to store a full definition snapshot. It is consistent with C5's stable component identity plus revision model, C7's no-automatic-update rule, and B9's identity-stability requirement.
- **DSN-001 impact:** The instance/document model must retain the component identity and exact revision reference needed to resolve the placed definition. Missing pinned revisions must be reported rather than silently rebound.
- **DSN-002 impact:** The library engine must support version-specific lookup for retained revisions and report when a requested revision cannot be found.
- **Shared-contract impact:** A future resolution request must be able to specify an exact revision. Whether this is a revision number, immutable revision ID, or another explicit type remains unresolved.
- **Validation required:** Place an instance against revision R, publish a later revision, and verify the instance continues to resolve R. Remove or make R unavailable in a test fixture and verify that resolution reports the missing revision rather than returning the latest revision.
- **Follow-up:** Decide revision identifier type, revision retention guarantees, offline behavior, whether a minimal cached representation is needed, and the document's persistence format.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C7 — Behavior When a Library Definition Changes

- **Status:** DECIDED — selected direction for the current MVP slice.
- **Selected option:** Option 1 — Existing instances retain their pinned revision; no automatic update.
- **Decision:** Updating a library definition does not silently change existing schematic instances. Existing instances continue to reference their pinned revisions.
- **Rationale:** This is consistent with C5/C6 and B9. A deliberate future update workflow can be designed separately; it is not implied by a library edit.
- **Scope boundary:** “Nothing” means no automatic migration or update. It does not mean silently ignoring a missing pinned revision; C8's missing-definition/revision diagnostics still apply.
- **DSN-001 impact:** A library update must not mutate the definition revision used by an existing instance without an explicit user-approved update operation.
- **DSN-002 impact:** Revision changes must not invalidate existing pinned references merely because a newer revision is published. The exact historical retention guarantee remains to be defined.
- **Shared-contract impact:** Revision-specific resolution must preserve the requested revision and report failure when it cannot be resolved.
- **Validation required:** Update a library component and verify that existing instances continue to use their pinned revision; verify that no implicit latest-version fallback occurs.
- **Follow-up:** Design optional update notifications, revision diffing, deliberate update/migration operations, and whether updates apply per instance or to a selected group.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C8 — Missing-Definition Recovery

- **Status:** DECIDED — graceful degradation with explicit diagnostics.
- **Selected option:** Option 3 — Load what can be loaded, explicitly report missing definitions, and preserve recoverable information.
- **Decision:** A missing component definition or missing pinned revision should not automatically force rejection of the entire schematic if the remaining document can be loaded safely. The engine must explicitly report unresolved references and must not silently substitute another definition.
- **Rationale:** This preserves access to otherwise valid schematic data while keeping the unresolved state visible. It aligns with B10's hard-error policy for identity collisions: graceful recovery from missing data is not permission to overwrite, merge, or invent identity.
- **DSN-001 impact:** The engine must represent and report unresolved component references without crashing or falsely claiming full resolution. Stable `InstanceId` and the original reference-designator value must be preserved where the document data is valid.
- **DSN-002 impact:** Resolution must distinguish a successful lookup from a missing component or missing revision, using an explicit result/error representation.
- **Shared-contract impact:** A structured missing-definition/missing-revision diagnostic is required when the relevant contract is designed. Exact error type, fields, and severity remain open.
- **Validation required:** Load a schematic fixture with a missing component and another with a missing pinned revision. Verify valid portions load, unresolved references are reported, the original reference is retained, and no latest-version fallback or silent rebind occurs.
- **Follow-up:** Define placeholder/rendering behavior, whether editing and saving unresolved schematics are allowed, what unresolved reference data must survive round-trip, diagnostic severity and payload, and recovery after the definition becomes available again.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## C9 — Provenance and Source-Format Metadata

- **Status:** DECIDED — deferred from the current MVP slice.
- **Selected direction:** Defer provenance metadata.
- **Decision:** Provenance/source-format metadata is not required for the current component-definition model or schematic/library integration slice.
- **Rationale:** It is not necessary to establish stable component identity, revision pinning, or missing-definition recovery for the current slice. Deferral avoids adding an undefined metadata model prematurely.
- **DSN-001 impact:** No provenance behavior is required in the current slice.
- **DSN-002 impact:** Do not require provenance fields for library definitions in the current slice.
- **Shared-contract impact:** No provenance field or provenance API is required solely for the current slice.
- **Repository constraint:** This decision does not authorize false provenance claims or concealment of required records. Any necessary legal/provenance records must be handled truthfully in an approved location. The previously selected repository restriction on source-specific references remains in force.
- **Validation required:** No provenance feature acceptance is implied for this slice. Tests must not depend on provenance fields that have not been approved.
- **Follow-up:** Revisit provenance when import workflows and their legal/audit requirements are explicitly in scope; define schema, scope, retention, and approved record location before implementation.
- **Approved by:** Project Lead.
- **Approval date:** 2026-10-13.

## Consistency review — B1–B10 and C1–C4

### Consistent decisions

- **C5 with B2:** Stable component identity and revision identity are separate concepts. The primary lookup identity remains `(LibraryId, ComponentName)`; an optional UUID supplements it. A content hash does not replace the primary identity.
- **C5/C6 with B7/B9:** Component revision changes do not regenerate a placed instance's stable `InstanceId`. Ordinary save/load must preserve stable identities and relationships.
- **C5/C6/C7 with B10:** Revision collisions or invalid/ambiguous references must not be resolved by last-write-wins or silent fallback. Duplicate identities in their defined scopes remain hard errors; a missing revision is a distinct unresolved-reference condition.
- **C6/C7 with C1:** A component's selected symbol representation belongs to its component definition/revision model. The exact rule for whether a symbol representation can change between revisions, and how that affects existing instances, remains open. No implicit latest-revision substitution is permitted.
- **C2 with C5/C6:** Deferring multi-unit support does not remove the need to pin revisions for supported single-unit definitions. Unit-level revision semantics remain outside the current slice.
- **C3/C4 with C5/C6:** Footprint association and pin-to-pad mapping remain deferred. Revision pinning does not itself imply footprint resolution or mapping support.
- **C8 with B10:** Missing definitions are handled through explicit diagnostics and recoverable unresolved references where possible. This does not weaken the hard-error rule for duplicate IDs/collisions and does not permit silent overwrite, merge, or fabricated replacement identities.
- **C8 with B7/B8/B9:** Recovery must preserve a valid placed instance's stable identity and reference designator, and must not silently rebind the instance to a different definition. Whether editing/saving is allowed while unresolved remains open.
- **C9 with the existing A8 direction:** Provenance metadata is deferred, but this does not permit falsifying provenance or omitting legally required records. The previously selected restriction on source-specific references in the WREATH Git repository remains separate from legal recordkeeping.
- **C5–C9 overall:** The choices support reproducibility and graceful recovery without requiring full definition snapshots in the current slice. That outcome depends on defining revision retention and exact revision lookup before claiming reproducibility.

### Explicitly unresolved implementation details

1. **Revision model:** Revision counter format, initial value, increment rules, publication semantics, and whether published revisions are immutable.
2. **Revision retention:** Whether every revision referenced by a saved schematic must be retained indefinitely, can be exported with the schematic, or can become unavailable. C6/C7 require that unavailability be explicit and never trigger silent latest-version fallback.
3. **Revision identity in contracts:** Whether references use `(ComponentId, revision_number)`, an immutable `DefinitionRevisionId`, or another explicit representation.
4. **Definition mutation:** Whether editing a published definition creates a new revision atomically and how failed edits/transactions affect revision counters.
5. **Rename/move semantics:** How component renames, library moves, and namespace changes preserve or migrate stable identity and pinned revision references.
6. **Symbol changes:** Whether existing instances retain symbol geometry/metadata from their pinned definition revision, and how future deliberate revision migration affects placed geometry and connectivity.
7. **Missing definition versus missing revision:** Exact error/diagnostic taxonomy, payload fields, severity, and distinction between absent component identity and unavailable historical revision.
8. **Graceful-degradation boundary:** Which document structures can still load when a definition is missing, how placeholders are represented, and whether editing/saving unresolved documents is allowed.
9. **Round-trip recovery:** Which unresolved reference fields must survive save/load, and what happens when the missing definition later becomes available.
10. **Offline behavior:** Whether pinning alone is sufficient for the desktop MVP or a limited cached representation is needed. Full snapshots were not selected, and no cache model has been approved.
11. **Update workflow:** Notifications, diffing, explicit migration, scope of updates, undo/redo, and compatibility checks remain future design work.
12. **Provenance:** Schema, source/import identifiers, retention, and approved location for any necessary audit/legal records remain deferred.
13. **Acceptance scope:** Map the above behaviors to the approved DSN-001/DSN-002 slice and authoritative acceptance criteria before implementation; do not count deferred capabilities as implemented acceptance obligations.

## Change-control note

C5–C9 record the Project Lead's selected directions on 2026-10-13. They do not authorize repository edits or implementation by themselves. Revision retention and exact version-specific lookup are prerequisites to delivering the intended reproducibility guarantee. Missing-definition recovery must remain distinct from identity-collision handling. No repository files were modified when creating this new decision-document copy.
