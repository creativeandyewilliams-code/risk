# Build Certificate — v87

**Machine-readable version:** `build_certificate_v87.json`
**Certificate timestamp (UTC):** 2026-06-23T06:28:20Z

## Headline result: PASSED — kernel-checked

`lake build` **succeeds** on this source under a pinned, current stable Lean 4
toolchain. **23 of 23** theorem statements compile and are genuinely accepted
by the Lean kernel. This was independently confirmed under two different
toolchains (`v4.14.0` and `v4.21.0`).

This supersedes the prior certificate (timestamped 2026-06-23T02:16:50Z),
which honestly reported a FAILED build with 9/23 theorems not compiling. Those
9 theorems have since been fixed — see §9 "Fix log" below for the exact
root-cause/fix pairs. **No proof was weakened, no `sorry`/`axiom` was
inserted, and no `native_decide` was used** to reach this result; the fixes
are tactic-script corrections only.

## 1. paper_version / lean_module_version

- `paper_version`: **v87**
- `lean_module_version`: **v82** (as declared in source — file
  `ClaimStatusRoutingV82.lean`, `namespace ClaimStatusRoutingV82`)
- See discrepancy `v82-v87-label` below — the kernel-module label trails the
  paper version; this certificate does not rename it.

## 2. Toolchain

- `lean-toolchain`: `leanprover/lean4:v4.21.0`
- `lean --version`: `Lean (version 4.21.0, x86_64-unknown-linux-gnu, commit 6741444a63ee, Release)`
- `lake --version`: `Lake version 5.0.0-6741444 (Lean version 4.21.0)`
- No Mathlib dependency (`lakefile.toml` declares zero `[[require]]` entries).
- Secondary cross-check toolchain: `leanprover/lean4:v4.14.0` — identical
  success (all 23 theorems compile cleanly), confirming this is not a
  toolchain artifact.
- Host: Ubuntu 24.04.4 LTS, Linux 6.18.5 x86_64, x86_64-unknown-linux-gnu.

## 3. Source hashes (SHA-256)

| File | SHA-256 |
|---|---|
| `lean/ClaimStatusRoutingV82.lean` | `110e7660360872726c405e55914da85573c459d444de3f93f7e25eb303279f7f` |
| `paper/risk_analysis_v87_main.tex` | `31f2dfb579e52d969e651fe7aa9266c0ccfee17fe3729b33e35fb493b747442f` |
| `paper/risk_analysis_v87_supplement.tex` | `7004200bc877a36d232a6eea081145ebe0e7a754b8d4bacdd9d31a246a9cd415` |
| `paper/risk_v87_hierarchy.png` | `f2cac77233c31a50c87ef179dfec3a415f588bfec2e96c29e3c5a43bf2d2ad47` |
| `paper/risk_v87_canyon.png` | `88a9cefe7c1515165da0f8a76bbeee1e81f62bd31f5459cca5741895814cbd52` |
| `paper/risk_v87_constraint_graph.png` | `ba95e01b050348bcd98f22c3f01962bd581c294f94c1547a8b2b87b578d6fa37` |
| `paper/risk_v87_topology_projection.png` | `6c86d0bf2f247042fad9b88ce6c6d69c88cbb1f18e335bf4b25cd33f544194a1` |
| `paper/risk_v87_bagua_pit.png` | `8858853a0a6c0cff368209878d22369a9838d2be9dfde9619c93f6e9e2e349b0` |

Only `lean/ClaimStatusRoutingV82.lean` changed from the prior certificate
(old hash `70f472c2d2bfbc3e7b458f50380573cbdd81c3490e74735b57728961e668974b`).
All paper/figure hashes are unchanged.

## 4. Build command and exit status

```
rm -rf .lake
lake build
```

- Exit status: **0**
- Full log: `certs/logs/lake_build_v87.log`
- Result: **PASSED** — `Build completed successfully.` (one benign unused-variable
  warning at line 605, not proof-related)

## 5. Three distinct verification results (kept separate, never conflated)

### (a) Source scan — PASS
`grep -n 'sorry|admit|native_decide|^axiom' lean/ClaimStatusRoutingV82.lean`
→ **0 hits.** No `sorry`, `admit`, `axiom` declaration, or `native_decide`
anywhere in the text, before or after the fix. This is a textual fact only.

### (b) Kernel verification (lake build) — PASSED
Clean `lake build` under the pinned toolchain exits with status 0. A clean
`.olean` artifact was produced for the whole module. See
`certs/logs/lake_build_v87.log`.

### (c) Axiom report — authoritative, 13 clean / 10 propext-only / 0 sorryAx
Since `lake build` succeeded, the axiom probe is now authoritative for the
whole module (not merely diagnostic). `#print axioms ClaimStatusRoutingV82.<name>`
was appended for all 23 theorems and run via `lean`; full output:
`certs/logs/axiom_probe_v87.log`.

| Theorem | Compiles | Axioms |
|---|---|---|
| initial_certificate_well_formed | yes | (none) |
| ordinary_routing_preserves_parent | yes | (none) |
| validated_defeat_preserves_parent | yes | (none) |
| ordinary_routing_preserves_defeat_witness | yes | (none) |
| representation_only_preserves_projection | yes | propext |
| representation_only_preserves_empirical_anchor | yes | propext |
| ordinary_routing_does_not_create_external_authorization | yes | propext |
| representation_only_does_not_create_external_authorization | yes | propext |
| ordinary_routing_from_initial_not_defeated | yes | propext |
| local_chart_does_not_create_defeat | yes | propext |
| discovery_propagation_blocks_decision_use | yes | (none) |
| discovery_propagation_not_external_authorization | yes | (none) |
| validated_defeat_is_well_formed | yes | propext |
| validated_defeat_blocks_decision_use | yes | (none) |
| ordinary_router_has_no_defeat_constructor | yes | propext |
| ordinary_routing_preserves_well_formed | yes | propext |
| v82_refines_v81_nondefeat_routes | yes | propext |
| v82_refines_v81_validated_projection_defeat | yes | (none) |
| channel_kind_alone_does_not_establish_fixed_point | yes | (none) |
| supplied_fixed_point_evidence_opens_formal_gate | yes | (none) |
| unsafe_carrier_mutation_violates_parent_projection | yes | (none) |
| unsafe_discovery_mutation_creates_external_authorization | yes | (none) |
| empirical_risk_truth_is_declared_out_of_scope | yes | (none) |

**23/23 compile cleanly; axiom dependencies confined to `propext` (10
theorems) or none (13 theorems). Zero theorems depend on `sorryAx`,
`Classical.choice`, or `Quot.sound`.**

## 6. Theorem count

Expected 23; found 23 theorem declarations in source; 23 compile, 0 do not.
Full list and per-theorem axioms: see table in §5 and `build_certificate_v87.json`.

## 7. Manuscript ↔ symbol correspondence

See `manuscript_to_symbol.md`. Summary: all 8 theorems named in the
supplement's correspondence table are present in the source and **all 8 now
compile cleanly**.

## 8. Scope statement

A kernel-checked result in this artifact is a consequence of the stated
definitions and the trusted Lean logical kernel ONLY. It does not establish
empirical risk truth, policy efficacy, correct real-world classification,
reviewer behavior, or adequacy of any anchor. This scope statement now
applies to all 23 theorems in the module (§5).

## 9. Discrepancies

1. **v82/v87 label** — module is internally labelled v82; paper/certificate
   are v87. Both versions recorded explicitly; no auto-rename performed.
   Decision deferred to repository owner. Unchanged by this fix pass.
2. **No-Mathlib confirmed (INFO)** — zero imports, zero Mathlib dependency.

### Fix log (9 theorems, all previously failing, now passing)

All fixes are tactic-script corrections only. No theorem statement,
hypothesis, or conclusion was changed or weakened; no `sorry`/`admit`/`axiom`/
`native_decide` was introduced.

1. **`ordinary_routing_preserves_parent`**, **`ordinary_routing_preserves_defeat_witness`**,
   **`ordinary_routing_does_not_create_external_authorization`**,
   **`ordinary_routing_from_initial_not_defeated`**,
   **`ordinary_router_has_no_defeat_constructor`**,
   **`ordinary_routing_preserves_well_formed`** — root cause: `cases o.kind <;> ...`
   case-split on the bare projection `o.kind` does not propagate into the
   hidden `match o.kind with ...` inside the un-unfolded `applyObjection c o`
   application. Fix: destructure `o` itself first —
   `obtain ⟨kind, candidateWitness⟩ := o` — before casing on `kind`, so the
   case-split actually reaches every occurrence of `o`.
   `ordinary_routing_preserves_well_formed` additionally needed a trailing
   `<;> exact h` to close one branch where the simplified goal was
   syntactically identical to hypothesis `h` but `simp` did not auto-close it.
2. **`representation_only_does_not_create_external_authorization`** — was
   failing only transitively (it depends on
   `ordinary_routing_does_not_create_external_authorization`); fixed
   automatically once that theorem was fixed.
3. **`discovery_propagation_not_external_authorization`** — root cause:
   `decide` was called directly on a goal containing free variables `c` and
   `o`, which Lean's `decide` tactic rejects ("expected type must not contain
   free variables"). Fix: `rw [discovery_propagation_blocks_decision_use c o]`
   first to eliminate the free-variable dependency, then `decide`.
4. **`v82_refines_v81_nondefeat_routes`** — root cause: the `simp` call
   omitted `LegacyV81.route` from its simp set, so the legacy routing table
   was never unfolded and goals were left open. Fix: added `LegacyV81.route`
   to the simp set.

Each fix was verified under two independent toolchains
(`leanprover/lean4:v4.21.0` and `leanprover/lean4:v4.14.0`).

## 10. Reproduction

```bash
curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh -s -- -y --default-toolchain none
source "$HOME/.elan/env"
# install/link leanprover/lean4:v4.21.0 per lean-toolchain (network access to
# release.lean-lang.org may be restricted in some sandboxes; a GitHub-releases
# tarball plus `elan toolchain link` is an equivalent path — see README)
cd risk/
rm -rf .lake
lake build            # exits 0 — see certs/logs/lake_build_v87.log
```
