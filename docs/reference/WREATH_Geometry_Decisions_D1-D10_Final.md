**# D. Geometry**



\---



**## [D1] Coordinate Units**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** What units do coordinates use internally? Millimeters, mils (thousandths of an inch), nanometers, or abstract units?



**\*\*Options:\*\***

1\. **\*\*Millimeters (f64)\*\*** — SI standard, used by many modern EDA tools.

2\. **\*\*Nanometers (i64)\*\*** — Integer precision, avoids floating-point issues. KiCad uses this internally.

3\. **\*\*Mils (f64)\*\*** — Traditional EDA unit.

4\. **\*\*Abstract grid units\*\*** — Decoupled from physical units.



**\*\*Recommendation:\*\*** Nanometers as i64 internally. Avoids floating-point precision issues. Convert to mm/mils at display/export boundaries.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** All schematic coordinates use this unit.  

**\*\*Impact on DSN-002:\*\*** All symbol/footprint geometry uses this unit.  

**\*\*Impact on shared contracts:\*\*** Coordinate type must be defined in shared contracts.  

**\*\*Evidence or tests required:\*\*** Round-trip precision test.  

**\*\*Follow-up tasks:\*\*** Define coordinate type.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D2] Origin Conventions**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** Where is the origin for schematic sheets, symbols, and footprints?



**\*\*Options:\*\***

1\. **\*\*Top-left (0,0)\*\*** — Screen convention.

2\. **\*\*Center of sheet/symbol\*\*** — Mathematical convention.

3\. **\*\*Bottom-left\*\*** — Traditional drafting convention.



**\*\*Recommendation:\*\*** Decide per context: symbol origin at center (pin-placement convenience), schematic sheet origin at top-left (screen convention). Document both.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Schematic coordinate system.  

**\*\*Impact on DSN-002:\*\*** Symbol and footprint local coordinate system.  

**\*\*Impact on shared contracts:\*\*** Origin convention documentation.  

**\*\*Evidence or tests required:\*\*** Visual verification against expected placement.  

**\*\*Follow-up tasks:\*\*** Document conventions.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D3] Axis Direction and Handedness**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** Does Y increase upward (math convention) or downward (screen convention)?



**\*\*Options:\*\***

1\. **\*\*Y-down\*\*** — Screen convention (KiCad schematic uses this).

2\. **\*\*Y-up\*\*** — Mathematical convention (KiCad PCB uses this for some contexts).



**\*\*Recommendation:\*\*** Y-down for schematics (consistent with screen rendering). Document clearly.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** All schematic rendering and hit-testing.  

**\*\*Impact on DSN-002:\*\*** Symbol geometry definition.  

**\*\*Impact on shared contracts:\*\*** Axis convention documentation.  

**\*\*Evidence or tests required:\*\*** Visual verification.  

**\*\*Follow-up tasks:\*\*** Document conventions.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D4] Rotation Convention**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** Are rotations clockwise or counter-clockwise? Degrees or radians? What increments?



**\*\*Options:\*\***

1\. **\*\*CCW in degrees\*\*** — Mathematical standard.

2\. **\*\*CW in degrees\*\*** — Screen-space convention.

3\. **\*\*90° increments only\*\*** — Simplest for schematic placement.



**\*\*Recommendation:\*\*** CCW in degrees (f64), stored as a continuous value. 90° snapping is a UI concern, not a data model concern.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Instance placement transform.  

**\*\*Impact on DSN-002:\*\*** Symbol geometry rotation.  

**\*\*Impact on shared contracts:\*\*** Rotation type definition.  

**\*\*Evidence or tests required:\*\*** Rotation round-trip test.  

**\*\*Follow-up tasks:\*\*** Define rotation in geometry types.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D5] Mirroring Convention**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer (basic support needed)  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** How is mirroring represented? Separate X/Y mirror flags, or part of the transform matrix?



**\*\*Options:\*\***

1\. **\*\*Boolean flags\*\*** — \`mirror_x: bool, mirror_y: bool\`.

2\. **\*\*Transform matrix\*\*** — Full 2D affine transform.

3\. **\*\*Enum\*\*** — \`Normal | MirrorX | MirrorY | MirrorBoth\`.



**\*\*Recommendation:\*\*** Boolean flags for simplicity. A full transform matrix is overkill for schematic placement.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Instance placement.  

**\*\*Impact on DSN-002:\*\*** Symbol rendering.  

**\*\*Impact on shared contracts:\*\*** Mirror type definition.  

**\*\*Evidence or tests required:\*\*** Mirror round-trip test.  

**\*\*Follow-up tasks:\*\*** Define in geometry types.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D6] Transform Order**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** When placing an instance with position, rotation, and mirror, what order are transforms applied?



**\*\*Options:\*\***

1\. **\*\*Mirror → Rotate → Translate\*\*** — Common in EDA tools.

2\. **\*\*Rotate → Mirror → Translate\*\*** — Alternative.

3\. **\*\*Explicit matrix\*\*** — No ambiguity, more complex.



**\*\*Recommendation:\*\*** Mirror → Rotate → Translate. Document this clearly as a convention. Both engines must agree.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Instance placement rendering.  

**\*\*Impact on DSN-002:\*\*** Pin position calculation.  

**\*\*Impact on shared contracts:\*\*** Transform convention documentation.  

**\*\*Evidence or tests required:\*\*** Pin positions match expected values after transform.  

**\*\*Follow-up tasks:\*\*** Add transform test.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D7] Pin and Pad Geometry Conventions**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Required before implementation (pins); Can defer (pads)  

**\*\*Decision owner(s):\*\*** Shivam (primary), Project Lead (review)



**\*\*Problem:\*\*** How are pin positions, lengths, and orientations defined within a symbol?



**\*\*Recommendation:\*\*** Pins have a position (in symbol-local coordinates), a length, and an orientation (0°/90°/180°/270°). Pad geometry can be deferred.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Must calculate global pin positions for wire connections.  

**\*\*Impact on DSN-002:\*\*** Must define pin geometry in symbol definitions.  

**\*\*Impact on shared contracts:\*\*** Pin geometry types.  

**\*\*Evidence or tests required:\*\*** Pin position calculation test.  

**\*\*Follow-up tasks:\*\*** Define pin geometry type.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D8] Grid, Precision, and Tolerance**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** Is there a snap grid? What precision is used for coordinate comparisons?



**\*\*Recommendation:\*\*** Defer detailed grid/snap design. Use exact integer comparison for nanometer coordinates. Grid snapping is a UI concern.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Wire endpoint snapping.  

**\*\*Impact on DSN-002:\*\*** Pin placement precision.  

**\*\*Impact on shared contracts:\*\*** Minimal.  

**\*\*Evidence or tests required:\*\*** N/A if deferred.  

**\*\*Follow-up tasks:\*\*** Design after sprint.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D9] Required Text and Graphic Primitive Support**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer  

**\*\*Decision owner(s):\*\*** Shivam (symbols), Project Lead (schematic)



**\*\*Problem:\*\*** What graphic primitives must be supported for symbol drawing? Lines, arcs, circles, rectangles, text, polygons?



**\*\*Recommendation:\*\*** Defer detailed primitive catalog. For the sprint, support: lines, rectangles, circles, and text as the minimal set for simple symbol rendering.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Schematic rendering primitives.  

**\*\*Impact on DSN-002:\*\*** Symbol definition primitives.  

**\*\*Impact on shared contracts:\*\*** Primitive types if shared.  

**\*\*Evidence or tests required:\*\*** Visual rendering test.  

**\*\*Follow-up tasks:\*\*** Expand primitive set post-sprint.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---



**## [D10] KiCad Geometry Compatibility Expectations**



**\*\*Status:\*\*** OPEN  

**\*\*Priority:\*\*** Can defer  

**\*\*Decision owner(s):\*\*** Project Lead, Shivam



**\*\*Problem:\*\*** Must WREATH geometry exactly match KiCad's coordinate system, or is an independent system acceptable?



**\*\*Recommendation:\*\*** Independent system with documented conversion rules. Defer KiCad import/export geometry mapping.



**\*\*Final decision:\*\***  

**\*\*Rationale:\*\***  

**\*\*Impact on DSN-001:\*\*** Conversion logic if importing KiCad schematics.  

**\*\*Impact on DSN-002:\*\*** Conversion logic if importing KiCad libraries.  

**\*\*Impact on shared contracts:\*\*** Minimal.  

**\*\*Evidence or tests required:\*\*** N/A if deferred.  

**\*\*Follow-up tasks:\*\*** Design conversion when import/export is in scope.  

**\*\*Approved by:\*\***  

**\*\*Approval date:\*\***



\---


---

# Project Lead Decision Record: D1–D8

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Status:** D1–D8 decided. D9–D10 remain open.  
**Repository status:** This is a separate decision document. No repository files were modified.

The blank decision fields in the original source template are superseded by the decisions recorded below. The source template is retained above for traceability.

## D1 — Coordinate Units

- **Status:** DECIDED
- **Selected option:** Option 2 — Nanometers (`i64`).
- **Decision:** Use signed 64-bit integer nanometers for stored internal geometry coordinates. Convert to display and interchange units at defined boundaries.
- **Rationale:** Exact integer comparisons for stored coordinates avoid floating-point equality issues and support deterministic pin/wire endpoint comparisons.
- **DSN-001:** Schematic coordinates use this shared representation.
- **DSN-002:** Symbol geometry uses the same representation. Footprint geometry can adopt it when footprint integration is in scope.
- **Shared contract:** Define a common coordinate type and unit semantics.
- **Validation:** Serialization round-trip; exact equality for representable values; unit conversion; range and overflow tests.
- **Unresolved:** Supported range, overflow behavior, decimal-to-nanometer rounding, serialization representation, and compatibility guarantees.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D2 — Origin Conventions

- **Status:** DECIDED
- **Selected direction:** Context-specific origins.
- **Decision:** Schematic sheet coordinates use a top-left origin. Symbol geometry uses an explicitly defined symbol-local anchor. Each coordinate space must document its origin and its transform into its parent/global space.
- **Rationale:** Sheet and symbol geometry have different purposes; one universal origin would add ambiguity.
- **DSN-001:** Apply sheet and symbol-local conventions consistently for placement and rendering.
- **DSN-002:** Symbol definitions must follow the agreed symbol-local anchor convention.
- **Shared contract:** Document coordinate-space semantics and local-to-global transforms.
- **Validation:** Known-point placement tests, global pin-position tests, and save/load round-trip tests.
- **Unresolved:** Exact anchor definition, whether anchors are configurable, sheet bounds, out-of-bounds behavior, future footprint-local origins, and any serialized origin metadata.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D3 — Axis Direction and Handedness

- **Status:** DECIDED
- **Selected option:** Option 1 — Y-down for schematic coordinates.
- **Decision:** Positive X increases to the right; positive Y increases downward in schematic coordinate space.
- **Rationale:** This is convenient for screen-oriented schematic layout and aligns with the selected top-left sheet origin.
- **DSN-001:** Rendering, hit-testing, placement, and wire geometry must follow the same convention.
- **DSN-002:** Symbol-local geometry must use the agreed convention or convert through an explicit transform.
- **Shared contract:** Axis direction is documented semantics, not an implicit engine assumption.
- **Validation:** Positive-axis known-point tests, visual placement tests, and rotation/mirroring tests.
- **Unresolved:** Conventions for future geometry domains and exact conversion behavior between coordinate spaces.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D4 — Rotation Convention

- **Status:** DECIDED
- **Selected option:** Option 1 — Continuous counter-clockwise rotation in degrees.
- **Decision:** Represent rotation as a continuous angle in degrees using a counter-clockwise convention. UI snapping, including 90-degree increments, is separate from the underlying data model.
- **Rationale:** The model remains flexible and does not encode current UI limitations.
- **DSN-001:** Instance placement applies the documented convention.
- **DSN-002:** Symbol geometry and pin-position calculations use the same convention.
- **Shared contract:** Document rotation units, sign, normalization, and transform semantics.
- **Validation:** Round-trip tests; known points at 0°, 90°, 180°, and 270°; combined rotation/translation tests; cross-engine agreement tests.
- **Unresolved:** Angle storage type, canonical range, precision, rounding of transformed coordinates, interpretation under Y-down axes, and initial-slice support for non-right angles.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D5 — Mirroring Convention

- **Status:** DECIDED
- **Selected option:** Option 1 — Boolean X/Y mirror flags.
- **Decision:** Represent mirroring with explicit Boolean flags for X and Y mirroring.
- **Rationale:** Explicit flags are simple to serialize and suitable for the current schematic MVP.
- **DSN-001:** Instance placement carries and applies the mirror flags.
- **DSN-002:** Symbol rendering and pin calculations interpret the flags consistently.
- **Shared contract:** Define the flags and their axis-relative semantics unambiguously.
- **Validation:** Round-trip tests for no mirroring, X-only, Y-only, and both flags; verify asymmetric symbol and pin positions.
- **Unresolved:** Whether each flag means reflection across the named axis or reflection of a coordinate component; exact relationship to symbol-local axes; whether both flags are canonicalized to a 180° rotation-equivalent representation; serialization names.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D6 — Transform Order

- **Status:** DECIDED
- **Selected option:** Option 1 — Mirror → Rotate → Translate.
- **Decision:** Apply mirroring in symbol-local coordinates, then rotation, then placement translation. For column-vector notation, the intended composition is `p_global = T × R × M × p_local`.
- **Rationale:** An explicit order prevents DSN-001 and DSN-002 from producing different pin positions from the same placement data.
- **DSN-001:** Use the agreed composition for placement, rendering, and connectivity-point calculation.
- **DSN-002:** Use the same composition for symbol geometry and pin positions.
- **Shared contract:** Document operation order and coordinate/vector convention.
- **Validation:** Test asymmetric symbols and known pin coordinates with mirror-only, rotation-only, translation-only, and combined transforms. Include cross-engine fixtures.
- **Unresolved:** Exact mirror-flag semantics, the rotation matrix sign under Y-down coordinates, whether transforms use column or row vectors in code, rounding/quantization of fractional intermediate results, and overflow behavior.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D7 — Pin and Pad Geometry Conventions

- **Status:** DECIDED
- **Selected direction:** Minimal explicit symbol-pin geometry; defer pad geometry.
- **Decision:** A symbol pin has a symbol-local position, length, and cardinal orientation restricted to 0°, 90°, 180°, or 270° for the selected direction. Pad geometry is deferred until footprint integration.
- **Rationale:** DSN-001 needs reliable pin connection-point calculation. Pad geometry is not required for the schematic-only slice.
- **DSN-001:** Calculate global pin positions from local pin geometry and the D5/D6 transform convention.
- **DSN-002:** Store the required symbol-local pin geometry.
- **Shared contract:** Define pin position, length, orientation, and their units/semantics. Preserve B4's pin-number identity decision; geometry is not identity.
- **Validation:** Known local-to-global pin-position tests; orientation/length tests; mirror and rotation combinations; connectivity endpoint tests.
- **Unresolved:** Whether position denotes the pin anchor, connection endpoint, or another reference point; exact meaning of orientation; how length determines the electrical connection endpoint; handling of zero/negative lengths; and rounding after transforms.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D8 — Grid, Precision, and Tolerance

- **Status:** DECIDED
- **Selected option:** Option 1 — Exact integer comparison; defer detailed grid/snap design.
- **Decision:** Compare stored integer-nanometer coordinates exactly where equality is intended. Detailed grid spacing and UI snapping policy are deferred.
- **Rationale:** This matches D1 and avoids introducing an unapproved global tolerance into coordinate identity/equality.
- **DSN-001:** Wire endpoint and pin connection logic must use explicit connectivity rules; proximity alone must not imply electrical connectivity.
- **DSN-002:** Pin positions use the shared integer coordinate representation. Transformed coordinates still require an explicit quantization policy.
- **Shared contract:** Document exact coordinate equality and keep any future geometric tolerance separate from identity and electrical-connectivity semantics.
- **Validation:** Exact endpoint equality tests, near-but-not-equal tests, transform quantization tests, and wire/pin connectivity tests.
- **Unresolved:** Grid interval and snapping defaults; rounding policy for rotated/transformed points; overflow; and whether tolerance is allowed for non-connectivity geometric operations.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## Consistency Review — D1–D8 Against Prior WREATH Decisions

### Consistent directions

- **A1/A2/A4/A6/A7:** A common coordinate type and documented transforms fit the Rust workspace, shared-contract hub, and in-process engine APIs. Exact crate types and public API signatures remain open.
- **B4/B6:** Pin numbers and pad numbers are identity choices, not geometric coordinates. D7 does not replace those identifiers. Pad geometry remains deferred.
- **B7–B9:** Instance identity, reference designator, and revision pinning remain independent of geometry. A transform or definition revision must not silently regenerate the stable instance identity.
- **C1:** Component-scoped symbol representations can each have symbol-local geometry. Symbol selection/default behavior remains open.
- **C2:** Multi-unit support remains deferred; D7 describes the pin geometry needed for supported single-unit symbols and does not implement unit semantics.
- **C3/C4:** Footprint association and pin-to-pad mapping remain deferred. D7 explicitly defers pad geometry.
- **C5–C8:** Revision pinning and missing-definition recovery remain independent of the coordinate representation. A library update must not silently change the geometry resolved by a pinned instance.
- **D1–D4 with D5–D6:** The chosen units, origin, axis direction, rotation convention, mirror representation, and transform order can form a consistent model, but the exact matrix/sign/rounding rules must be specified together before implementation.

### Critical unresolved geometry details

1. Mirror X/Y flags must have precise axis-relative meanings.
2. The rotation matrix must be explicitly defined for Y-down coordinates and the chosen counter-clockwise convention.
3. The transform order must be tested using asymmetric shapes and known pin coordinates, not only symmetric shapes.
4. Arbitrary rotations may yield fractional intermediate coordinates even when inputs are integer nanometers; the final quantization/rounding rule is not yet decided.
5. Signed 64-bit coordinate range and intermediate overflow behavior must be specified.
6. Pin anchor, connection endpoint, length direction, and cardinal orientation semantics must be defined.
7. Exact coordinate equality does not itself define electrical connectivity; connectivity rules must be explicit.

## D9–D10 Status

- **D9 — Required Text and Graphic Primitive Support:** OPEN. The source recommends lines, rectangles, circles, and text as a minimal set. No final decision has been made.
- **D10 — Geometry Compatibility Expectations:** OPEN. The source recommends an independent geometry system and deferred conversion mapping. No final decision has been made.

## Change-Control Note

This record captures the Project Lead's D1–D8 selections. It does not authorize repository edits or implementation by itself. D9 and D10 remain open. Before implementation, reconcile the selected geometry conventions with the approved slice scope and acceptance criteria. No repository files were modified when creating this decision-document copy.


---

# Project Lead Decision Record: D9–D10

**Decision date:** 2026-10-13  
**Decision owner:** Project Lead  
**Status:** D1–D10 decided.  
**Repository status:** This is a separate decision-document copy. No repository files were modified.

## D9 — Required Text and Graphic Primitive Support

- **Status:** DECIDED
- **Selected option:** Option 1 — Minimal symbol primitives.
- **Decision:** The initial symbol-geometry slice must support lines, rectangles, circles, and text. Additional primitive types are deferred until supported by a concrete requirement.
- **Rationale:** A deliberately small primitive set keeps initial rendering, serialization, transformation, and validation scope manageable.
- **DSN-001:** Must be able to consume/render the agreed minimal primitives where required by schematic symbol instances.
- **DSN-002:** Symbol definitions must represent the agreed primitive set in the shared geometry contract.
- **Shared contract:** Define primitive types and their required fields and semantics without prematurely specifying a general-purpose shape framework.
- **Validation:** Add fixtures for each primitive; verify coordinate units, local-to-global transforms, serialization round trips, and rendering/geometry consistency.
- **Unresolved:** Line endpoint representation and stroke semantics; rectangle position/size and negative-dimension policy; circle center/radius and zero-radius policy; text anchor, font/fallback behavior, size, alignment, rotation, and text-mirroring behavior; line width/stroke styling; clipping; and whether unsupported primitives are rejected or preserved as opaque data.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## D10 — Geometry Compatibility Expectations

- **Status:** DECIDED
- **Selected option:** Option 1 — Independent geometry with documented conversion rules.
- **Decision:** WREATH defines and owns its geometry semantics independently. Conversion rules at file-format or integration boundaries must be documented. Detailed compatibility mapping is deferred until concrete requirements and validation cases justify it.
- **Rationale:** This avoids prematurely coupling the native geometry model to an external tool's internal representation while preserving a path for explicit, testable conversions.
- **DSN-001:** Uses the WREATH geometry contract and any approved conversion boundary rather than depending on undocumented external geometry assumptions.
- **DSN-002:** Defines symbol geometry using the same WREATH contract. Import/export conversion must be explicit where introduced.
- **Shared contract:** Document units, coordinate spaces, origins, axis direction, rotation, mirroring, transform order, primitive semantics, and conversion boundaries.
- **Validation:** Test WREATH-native round trips independently. When a format adapter is added, test conversion with documented fixtures and expected losses or limitations.
- **Unresolved:** Which external formats must be supported first; the authoritative format/specification versions; conversion scope and loss policy; handling of unsupported or unrepresentable primitives; compatibility corpus and acceptance thresholds; and whether any future parity requirement is behavioral, visual, file-level, or a combination.
- **Constraint:** This decision does not authorize source inspection, copying, or adaptation, and does not override the project's existing repository-content and provenance restrictions. Compatibility claims must be supported by approved specifications, documented observable behavior, and independently created tests.
- **Approved by:** Project Lead
- **Approval date:** 2026-10-13

## Consistency Review — D1–D10 and Prior WREATH Decisions

### Consistent directions

- **D1–D4:** D9's primitive set can use integer-nanometer coordinates, context-specific origins, Y-down schematic coordinates, and continuous counter-clockwise rotation. D10 keeps those conventions owned by WREATH and requires conversion behavior to be documented at boundaries.
- **D5–D6:** Symbol primitives are transformed using the selected Boolean X/Y mirror flags and Mirror → Rotate → Translate order. Primitive-specific behavior under mirroring and rotation still needs tests, especially for text.
- **D7–D8:** Pin geometry remains separate from graphic primitive geometry. Exact integer equality applies where coordinate equality is intended; it does not make near coordinates electrically connected. Transformed coordinates still require a quantization policy.
- **A1/A2/A4/A6/A7:** A minimal, documented geometry contract fits the Rust workspace, shared-contract hub, and in-process Rust APIs. D9/D10 do not require a new process boundary or UI commitment.
- **B4/B6/B7–B10:** Primitive geometry does not replace pin, pad, component, or instance identity rules. Collision handling, stable identity, and explicit revision behavior remain unchanged.
- **C1–C8:** Component-scoped symbols and revision-pinned definitions remain compatible with this primitive set. Deferred multi-unit support, footprint association, and pin-to-pad mapping remain deferred.
- **A8 and existing repository restrictions:** Independent geometry and documented conversion rules do not authorize prohibited source inspection or source-specific material in the repository. Any legally required provenance must still be handled truthfully in the approved location outside the restricted repository scope.

### Cross-decision risks to resolve before implementation

1. Define transform matrices and rotation sign explicitly for Y-down coordinates and counter-clockwise degrees.
2. Define final rounding/quantization for transformed integer-nanometer coordinates, including overflow behavior.
3. Specify the symbol-local anchor and the semantics of pin connection points.
4. Define text behavior under mirroring and rotation; text often needs different visual treatment from geometric lines and shapes.
5. Specify validation and preservation behavior for unsupported primitives at future import/export boundaries.
6. Do not claim compatibility or parity until the relevant formats, versions, fixtures, and acceptance criteria are explicitly approved.

## Final Geometry Decision Status

| Decision | Status | Selected direction |
|---|---|---|
| D1 | DECIDED | Nanometers (`i64`) |
| D2 | DECIDED | Top-left sheet origin; explicit symbol-local anchor |
| D3 | DECIDED | Y-down schematic coordinates |
| D4 | DECIDED | Continuous counter-clockwise degrees |
| D5 | DECIDED | Boolean X/Y mirror flags |
| D6 | DECIDED | Mirror → Rotate → Translate |
| D7 | DECIDED | Symbol-local pin position, length, cardinal orientation; defer pads |
| D8 | DECIDED | Exact integer comparison; defer detailed grid/snap design |
| D9 | DECIDED | Lines, rectangles, circles, and text |
| D10 | DECIDED | Independent WREATH geometry; documented conversion rules; defer detailed compatibility mapping |

## Change-Control Note

This record captures the Project Lead's D9–D10 selections and the combined D1–D10 geometry direction. It does not, by itself, authorize repository edits or implementation. Remaining implementation details and acceptance criteria must be resolved through the project's approval workflow. No repository files were modified when creating this decision-document copy.
