# risk — v87 artifact bundle

This repository holds the v87 risk-analysis paper, supplement, figures, and
the `ClaimStatusRoutingV82.lean` Lean 4 kernel artifact that the paper cites.

## Layout

- `paper/` — `risk_analysis_v87_main.tex`, `risk_analysis_v87_supplement.tex`,
  five `risk_v87_*.png` figures, and `README.txt` with LaTeX build notes.
- `lean/` — `ClaimStatusRoutingV82.lean`, the pure Lean 4 kernel module (no
  Mathlib dependency), plus `lean/README`.
- `lean-toolchain`, `lakefile.toml` — pinned toolchain and library target.
- `build_certificate_v87.json` / `build_certificate_v87.md` — the
  reproducible build certificate for the Lean artifact.
- `manuscript_to_symbol.md` — cross-check of every theorem named in the
  supplement's correspondence table against the source.
- `certs/logs/` — raw `lake build` log and `#print axioms` diagnostic output.

## Reproducing the Lean build

```bash
# 1. Install elan (Lean's toolchain manager)
curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh -s -- -y --default-toolchain none
source "$HOME/.elan/env"

# 2. From the repo root, the pinned toolchain in `lean-toolchain`
#    (leanprover/lean4:v4.21.0) will be fetched automatically by elan
#    the first time you invoke `lake`/`lean` in this directory. If your
#    environment blocks release.lean-lang.org but allows github.com, fetch the
#    matching release tarball directly and register it instead:
#      curl -L -o lean.tar.zst \
#        https://github.com/leanprover/lean4/releases/download/v4.21.0/lean-4.21.0-linux.tar.zst
#      tar --zstd -xf lean.tar.zst
#      elan toolchain link lean4-v4.21.0 ./lean-4.21.0-linux

# 3. Clean build
rm -rf .lake
lake build
```

**Current status: the build PASSES.** All 23 theorems in
`ClaimStatusRoutingV82.lean` compile cleanly under the Lean kernel, with axiom
dependencies confined to `propext` or none at all (no `sorryAx`, no
`Classical.choice`, no `Quot.sound`) — see `certs/logs/lake_build_v87.log` and
`certs/logs/axiom_probe_v87.log`. Read `build_certificate_v87.md` for the full
certificate, including the fix log for 9 theorems that previously failed to
compile and have since been corrected.

## Reading the certificate

`build_certificate_v87.md` keeps three checks strictly separate, per the
supplement's own requirement:

1. **Source scan** — grep for `sorry`/`admit`/`axiom`/`native_decide` (passes,
   0 hits — but this is a textual check only).
2. **Kernel verification** — the actual `lake build` result (currently fails).
3. **Axiom report** — `#print axioms` per theorem, which also distinguishes
   genuinely kernel-accepted theorems (axioms ⊆ {`propext`, `Classical.choice`,
   `Quot.sound`}) from theorems that only exist via Lean's `sorryAx`
   error-recovery placeholder.

A theorem is only "kernel-checked" once `lake build` succeeds **and** its
`#print axioms` output is in the standard set. Until then, refer to results as
draft / not yet kernel-checked.

## Version note (v82 vs v87)

The Lean module's internal label (file name `ClaimStatusRoutingV82.lean`,
namespace `ClaimStatusRoutingV82`) is v82, while the paper and this
certificate are v87. Both are recorded explicitly in the certificate; the
module's internal label was not renamed. See "Discrepancies" in
`build_certificate_v87.md`.
