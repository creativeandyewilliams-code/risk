# Build Certificate — v87

**Machine-readable version:** `build_certificate_v87.json`
**Certificate timestamp (UTC):** 2026-06-23T02:16:50Z

## Headline result: FAILED — do not call this artifact kernel-checked

`lake build` does **not** succeed on this source under a pinned, current
stable Lean 4 toolchain. **14 of 23** theorem statements compile and are
genuinely accepted by the Lean kernel; **9 of 23** do not compile and exist in
the post-error environment only via Lean's `sorryAx` recovery placeholder —
i.e. they are, today, unproved. This was independently confirmed under two
different toolchains (`v4.14.0` and `v4.21.0`), so the failure is a proof
defect in the source, not a toolchain-pin artifact.

Per the task constraints, **no proof was weakened, no `sorry`/`axiom` was
inserted, and no `native_decide` was used** to force a green build. This is an
honestly reported failing build.

## 1. paper_version / lean_module_version

- `paper_version`: **v87**
- `lean_module_version`: **v82** (as declared in source — file
  `ClaimStatusRoutingV82.lean`, `namespace ClaimStatusRoutingV82`)
- See discrepancy `v82-v87-label` below — the kernel-module label trails the
  paper version; this certificate does not rename it.

## 2. Toolchain

- `lean-toolchain`: `leanprover/lean4:v4.21.0`
- `lean --version`: `Lean (version 4.21.0, x86_64-unknown-linux-gnu, commit 6741444a63ee, Release)`
- `lake --version`: `Lake version 5.0.0 (Lean version 4.21.0)`
- No Mathlib dependency (`lakefile.toml` declares zero `[[require]]` entries).
- Secondary cross-check toolchain: `leanprover/lean4:v4.14.0` — identical
  failure (same 9 theorems), confirming this is not a toolchain artifact.
- Host: Ubuntu 24.04.4 LTS, Linux 6.18.5 x86_64, x86_64-unknown-linux-gnu.

## 3. Source hashes (SHA-256)

| File | SHA-256 |
|---|---|
| `lean/ClaimStatusRoutingV82.lean` | `70f472c2d2bfbc3e7b458f50380573cbdd81c3490e74735b57728961e668974b` |
| `paper/risk_analysis_v87_main.tex` | `31f2dfb579e52d969e651fe7aa9266c0ccfee17fe3729b33e35fb493b747442f` |
| `paper/risk_analysis_v87_supplement.tex` | `7004200bc877a36d232a6eea081145ebe0e7a754b8d4bacdd9d31a246a9cd415` |
| `paper/risk_v87_hierarchy.png` | `f2cac77233c31a50c87ef179dfec3a415f588bfec2e96c29e3c5a43bf2d2ad47` |
| `paper/risk_v87_canyon.png` | `88a9cefe7c1515165da0f8a76bbeee1e81f62bd31f5459cca5741895814cbd52` |
| `paper/risk_v87_constraint_graph.png` | `ba95e01b050348bcd98f22c3f01962bd581c294f94c1547a8b2b87b578d6fa37` |
| `paper/risk_v87_topology_projection.png` | `6c86d0bf2f247042fad9b88ce6c6d69c88cbb1f18e335bf4b25cd33f544194a1` |
| `paper/risk_v87_bagua_pit.png` | `8858853a0a6c0cff368209878d22369a9838d2be9dfde9619c93f6e9e2e349b0` |

## 4. Build command and exit status

```
rm -rf .lake
lake build
```

- Exit status: **1**
- Full log: `certs/logs/lake_build_v87.log`
- Result: **FAILED** — `Some required builds logged failures: ClaimStatusRoutingV82`

## 5. Three distinct verification results (kept separate, never conflated)

### (a) Source scan — PASS
`grep -n 'sorry|admit|native_decide|^axiom' lean/ClaimStatusRoutingV82.lean`
→ **0 hits.** No `sorry`, `admit`, `axiom` declaration, or `native_decide`
anywhere in the text. This is a textual fact only and says nothing about
whether the kernel accepts the proofs.

### (b) Kernel verification (lake build) — FAILED
Clean `lake build` under the pinned toolchain exits with status 1, with 57
reported errors across 8 distinct theorem proof bodies. No `.olean` artifact
was produced. See `certs/logs/lake_build_v87.log`.

### (c) Axiom report — diagnostic, 14 clean / 9 sorryAx
Because `lake build` failed, there is no successfully built artifact to query
axioms against in the normal "kernel-checked module" sense. As a diagnostic
*only*, a scratch copy of the source had `#print axioms ClaimStatusRoutingV82.<name>`
appended for all 23 theorems and was run directly through `lean` (full output:
`certs/logs/axiom_probe_v87.log`). Lean's error-recovery mechanism still lets
single-file elaboration proceed past failures by substituting a `sorryAx`
placeholder for failed proofs, so this probe distinguishes genuinely-proved
declarations from sorry-backed ones:

| Theorem | Compiles | Axioms |
|---|---|---|
| initial_certificate_well_formed | yes | (none) |
| ordinary_routing_preserves_parent | **NO** | sorryAx |
| validated_defeat_preserves_parent | yes | (none) |
| ordinary_routing_preserves_defeat_witness | **NO** | sorryAx |
| representation_only_preserves_projection | yes | propext |
| representation_only_preserves_empirical_anchor | yes | propext |
| ordinary_routing_does_not_create_external_authorization | **NO** | sorryAx |
| representation_only_does_not_create_external_authorization | **NO** | sorryAx |
| ordinary_routing_from_initial_not_defeated | **NO** | sorryAx |
| local_chart_does_not_create_defeat | yes | propext |
| discovery_propagation_blocks_decision_use | yes | (none) |
| discovery_propagation_not_external_authorization | **NO** | sorryAx |
| validated_defeat_is_well_formed | yes | propext |
| validated_defeat_blocks_decision_use | yes | (none) |
| ordinary_router_has_no_defeat_constructor | **NO** | propext, sorryAx, Quot.sound |
| ordinary_routing_preserves_well_formed | **NO** | sorryAx, Quot.sound |
| v82_refines_v81_nondefeat_routes | **NO** | propext, sorryAx |
| v82_refines_v81_validated_projection_defeat | yes | (none) |
| channel_kind_alone_does_not_establish_fixed_point | yes | (none) |
| supplied_fixed_point_evidence_opens_formal_gate | yes | (none) |
| unsafe_carrier_mutation_violates_parent_projection | yes | (none) |
| unsafe_discovery_mutation_creates_external_authorization | yes | (none) |
| empirical_risk_truth_is_declared_out_of_scope | yes | (none) |

**14/23 compile cleanly with only the standard `propext`/`Quot.sound` axioms
(no `Classical.choice` was needed anywhere); 9/23 do not compile.**

## 6. Theorem count

Expected 23; found 23 theorem declarations in source; 14 compile, 9 do not.
Full list and per-theorem axioms: see table in §5 and `build_certificate_v87.json`.

## 7. Manuscript ↔ symbol correspondence

See `manuscript_to_symbol.md`. Summary: all 8 theorems named in the
supplement's correspondence table are present in the source (no missing or
renamed symbol). **3 of those 8 do not compile**:
`ordinary_routing_preserves_parent`,
`ordinary_routing_does_not_create_external_authorization`, and the non-defeat
refinement theorem `v82_refines_v81_nondefeat_routes`.

## 8. Scope statement

A kernel-checked result in this artifact is a consequence of the stated
definitions and the trusted Lean logical kernel ONLY. It does not establish
empirical risk truth, policy efficacy, correct real-world classification,
reviewer behavior, or adequacy of any anchor. This scope statement currently
applies only to the 14 theorems the kernel actually accepted (§5); it does
**not** apply to the 9 unproved theorems.

## 9. Discrepancies

1. **v82/v87 label** — module is internally labelled v82; paper/certificate
   are v87. Both versions recorded explicitly; no auto-rename performed.
   Decision deferred to repository owner.
2. **Build failure (HIGH)** — 9/23 theorems fail to compile; reproduced
   identically on two independent toolchains. Root causes identified per
   theorem (see `build_certificate_v87.json` → `discrepancies` →
   `build-failure`): `cases o.kind <;> rfl`/`simp [...]` proofs that don't
   actually unfold `applyObjection` after casing on the bare projection
   `o.kind`; a `simp` call missing `LegacyV81.route` from its simp set; and a
   `decide` call on a goal containing free variables. Not fixed in this pass —
   left for the repository owner to address, per the task's instruction not to
   alter proofs unilaterally.
3. **Manuscript table overstates correctness (HIGH)** — 3 of 8
   manuscript-cross-referenced theorems are among the failing 9.
4. **No-Mathlib confirmed (INFO)** — zero imports, zero Mathlib dependency.

## 10. Reproduction

```bash
curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh -s -- -y --default-toolchain none
source "$HOME/.elan/env"
# install/link leanprover/lean4:v4.21.0 per lean-toolchain (network access to
# release.lean-lang.org may be restricted in some sandboxes; a GitHub-releases
# tarball plus `elan toolchain link` is an equivalent path — see README)
cd risk/
rm -rf .lake
lake build            # currently exits 1 — see certs/logs/lake_build_v87.log
```
