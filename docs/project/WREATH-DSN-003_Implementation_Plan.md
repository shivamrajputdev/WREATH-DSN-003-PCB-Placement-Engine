# WREATH-DSN-003 — Implementation Plan

## Phase 0 — Repository discovery and reference study
- Deliverables: repository discovery report, reference behavior mapping, native placement feature spec, dependency/licensing report, benchmark dataset, architecture proposal.
- Exit: explain footprint representation, geometry transform, connectivity influence, required interfaces.

## Phase 1 — Native data contracts and placement model
- Tasks: placement request model; result model; operations (add/move/rotate/flip/relative/align/distribute/auto/evaluate/commit); interaction with geometry, footprint, connectivity, shared model.
- Deliverables: approved API contract, coordinate/transform spec, constraints spec, optimization objective spec, error/result schema.

## Phase 2 — Native implementation
- Increments A-G: transforms; manual ops; geometry feasibility; fixed/constraints; basic auto; optimization; native integration.

## Phase 3 — Evaluation and parity testing.

## Phase 4 — Integration and acceptance.

## Staged capability rule
- First accepted vertical slice: manual placement (MVP steps 1-10).
- Basic deterministic automatic placement: next increment after manual slice and contracts accepted.
- Optimization improvements must not block first slice; remain required milestones; no global optimality claim.

## Gates
- Pre-implementation architecture decisions must be approved before dependent implementation.
- Optimization baseline/thresholds must be approved before becoming release gates.
