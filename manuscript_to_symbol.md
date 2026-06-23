# Manuscript ↔ Symbol Correspondence — v87 / lean_module v82

Source: `paper/risk_analysis_v87_supplement.tex`, Table "Selected Lean theorem
correspondence" (`\label{tab:lean-map}`, supplement lines 496-518).

Target: `lean/ClaimStatusRoutingV82.lean` (namespace `ClaimStatusRoutingV82`).

Status column reports two independent facts, kept separate per the task's
"do not conflate" requirement:

- **Present**: the symbol exists in the source file (textual/structural fact).
- **Compiles**: the symbol's stated proof was accepted by the Lean kernel under
  a clean `lake build` with `leanprover/lean4:v4.21.0` (see
  `build_certificate_v87.md` §3 and `certs/logs/lake_build_v87.log`). A symbol
  whose elaboration falls back to Lean's `sorryAx` error-recovery placeholder
  (confirmed via the diagnostic axiom probe, `certs/logs/axiom_probe_v87.log`)
  is marked **NO** here, even though the declaration name still exists in the
  source text.

| # | Manuscript symbol (table row) | Source symbol | Source line | Present | Compiles (kernel-checked) |
|---|---|---|---|---|---|
| 1 | `ordinary_routing_preserves_parent` | `ordinary_routing_preserves_parent` | 387 | yes | **NO** — `rfl` fails on all 17 cases of `o.kind`; proof relies on `sorryAx` |
| 2 | `representation_only_preserves_projection` | `representation_only_preserves_projection` | 405 | yes | yes (axioms: `propext`) |
| 3 | `representation_only_preserves_empirical_anchor` | `representation_only_preserves_empirical_anchor` | 415 | yes | yes (axioms: `propext`) |
| 4 | `ordinary_routing_does_not_create_external_authorization` | `ordinary_routing_does_not_create_external_authorization` | 425 | yes | **NO** — `simp` leaves 17 unsolved goals; proof relies on `sorryAx` |
| 5 | `discovery_propagation_blocks_decision_use` | `discovery_propagation_blocks_decision_use` | 461 | yes | yes (no axioms) |
| 6 | `validated_defeat_is_well_formed` | `validated_defeat_is_well_formed` | 473 | yes | yes (axioms: `propext`) |
| 7 | "non-defeat refinement theorem" | `v82_refines_v81_nondefeat_routes` | 553 | yes | **NO** — `simp` set omits `LegacyV81.route`; goals left unsolved; relies on `sorryAx` |
| 8 | `unsafe_carrier_mutation_violates_parent_projection` | `unsafe_carrier_mutation_violates_parent_projection` | 630 | yes | yes (no axioms) |

## Result

**8/8 named manuscript symbols are present in the source** (no missing or
renamed theorem). **5/8 compile cleanly under the Lean kernel; 3/8
(`ordinary_routing_preserves_parent`, `ordinary_routing_does_not_create_external_authorization`,
the non-defeat refinement theorem `v82_refines_v81_nondefeat_routes`) do not
compile** — their declarations exist and type-check as *statements*, but their
proofs are rejected by `lake build` and only persist in the post-error
environment via Lean's `sorryAx` recovery axiom. This is **not** a pass; see
`build_certificate_v87.md` discrepancies section.

No mismatch in symbol presence was found. The defect is exclusively in proof
correctness for 3 of the 8 cross-referenced theorems (and 6 more theorems
elsewhere in the file not named in the manuscript table — see the full
23-theorem axiom report in the certificate).
