# WREATH-DSN-003 — Manual-Placement MVP Specification and Implementation Sequence

## MVP vertical slice (ticket section 3.3, 10 mandatory steps)
1. Resolve board + footprint via approved stable IDs.
2. Read authoritative footprint def + board geometry via approved interfaces (no duplicate models).
3. Apply exact position + rotation/side transforms via shared coordinate/transform contract.
4. Support move/rotate/flip where board/footprint model supports.
5. Respect fixed footprints + mandatory constraints.
6. Evaluate containment/keepouts/overlap/spacing via approved geometry API; blocking dependency if unavailable.
7. Expose via stable engine API + UI/automation adapter per service conventions.
8. Return structured success/infeasible/validation/internal-error.
9. Failed/rejected edits leave authoritative state unchanged.
10. Deterministic tests for transforms, constraints, persistence integration, failure behavior.

## Implementation sequence
- Entry gates: approved MVP boundary, approved architecture contracts, resolved coordinate/transform details, defined error taxonomy, approved geometry API.
- Sequence: Increment A (footprint transforms) -> Increment B (manual placement operations) -> feasibility/fixed handling per MVP.
- Next increment after manual slice + contracts accepted: basic deterministic automatic placement.

## Constraints
- Do not duplicate board model or geometry kernel; consume approved interfaces.
- Do not invent external APIs or silently assume missing contracts.
- Hard constraints must never be overridden by optimization.
