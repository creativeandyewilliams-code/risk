# Manuscript ↔ Symbol Correspondence — v87 / lean_module v82

Source: `paper/risk_analysis_v87_supplement.tex`, Table "Selected Lean theorem
correspondence" (`\label{tab:lean-map}`, supplement lines 496-518).

Target: `lean/ClaimStatusRoutingV82.lean` (namespace `ClaimStatusRoutingV82`).

Status column reports two independent facts, kept separate per the task's
"do not conflate" requirement:

- **Present**: the symbol exists in the source file (textual/structural fact).
- **Compiles**: the symbol's stated proof was accepted by the Lean kernel under
  a clean `lake build` with `leanprover/lean4:v4.21.0` (see
  `build_certificate_v87.md` §3 and `certs/logs/lake_build_v87.log`), confirmed
  via the axiom probe (`certs/logs/axiom_probe_v87.log`) showing no theorem
  depends on `sorryAx`.

| # | Manuscript symbol (table row) | Source symbol | Source line | Present | Compiles (kernel-checked) |
|---|---|---|---|---|---|
| 1 | `ordinary_routing_preserves_parent` | `ordinary_routing_preserves_parent` | 387 | yes | **yes** (no axioms) |
| 2 | `representation_only_preserves_projection` | `representation_only_preserves_projection` | 406 | yes | **yes** (axioms: `propext`) |
| 3 | `representation_only_preserves_empirical_anchor` | `representation_only_preserves_empirical_anchor` | 416 | yes | **yes** (axioms: `propext`) |
| 4 | `ordinary_routing_does_not_create_external_authorization` | `ordinary_routing_does_not_create_external_authorization` | 426 | yes | **yes** (axioms: `propext`) |
| 5 | `discovery_propagation_blocks_decision_use` | `discovery_propagation_blocks_decision_use` | 462 | yes | **yes** (no axioms) |
| 6 | `validated_defeat_is_well_formed` | `validated_defeat_is_well_formed` | 474 | yes | **yes** (axioms: `propext`) |
| 7 | "non-defeat refinement theorem" | `v82_refines_v81_nondefeat_routes` | 555 | yes | **yes** (axioms: `propext`) |
| 8 | `unsafe_carrier_mutation_violates_parent_projection` | `unsafe_carrier_mutation_violates_parent_projection` | 632 | yes | **yes** (no axioms) |

## Result

**8/8 named manuscript symbols are present in the source and 8/8 compile
cleanly** under the pinned Lean kernel (`leanprover/lean4:v4.21.0`), with
axiom dependencies confined to `propext` (no `sorryAx`, no `Classical.choice`,
no `Quot.sound`). No mismatch in symbol presence or proof correctness was
found. See `build_certificate_v87.md` for the full 23-theorem axiom report
covering theorems beyond this table.

## Fix history

This file previously reported 3 of these 8 theorems (rows 1, 4, 7) as
failing to compile, due to proof bugs in the source: `cases o.kind <;> rfl` /
`simp [...]` patterns that case-split on the *projection* `o.kind` without
first destructuring `o` itself, so the case-split never propagated into the
hidden `match o.kind with ...` inside `applyObjection c o`. These were fixed
by destructuring `o` first (`obtain ⟨kind, candidateWitness⟩ := o`) before
casing on `kind`. See `build_certificate_v87.md` §9 "Fix log" for the full
list of changes (9 theorems total were fixed; this table covers only the 3
of those 9 that the manuscript table cross-references).
