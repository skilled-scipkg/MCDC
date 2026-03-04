---
name: mcdc-troubleshooting
description: This skill should be used when users ask about troubleshooting in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Troubleshooting

## High-Signal Playbook
### Route conditions
- Use this skill for install/runtime errors, MPI/GPU launch failures, Numba compile failures, and output validation mismatches (`docs/source/user/troubleshooting.rst`, `docs/source/user/faq.rst`).
- Route full install workflows to `mcdc-build-and-install`.
- Route scheduler scaling strategy to `mcdc-parallel-hpc`.
- Route model-construction mistakes to `mcdc-inputs-and-modeling`.

### Triage questions
1. What is the exact error text and which command produced it?
2. Are you running Python mode, Numba CPU, or Numba GPU?
3. Are you launching serial, `mpiexec`, or `srun`?
4. Which Python/Numba/mpi4py versions and MPI library are active?
5. Is this a local environment, container, or HPC module stack?

### Canonical workflow
1. Reproduce with the smallest failing input and capture stderr/stdout.
2. Check environment + dependency state (`python`, `numba`, `mpi4py`, `mcdc` import).
3. Retry in `--mode=numba_debug` when Numba trace depth is needed.
4. For MPI failures, verify mpi4py linkage to launcher MPI.
5. For CE failures, verify `MCDC_LIB` and data library path.
6. If a model-level error is suspected, isolate geometry/source/tally setup in a tiny deck.
7. Validate fix with a quick regression case before returning to production input.

### Minimal diagnostics commands
```bash
python -c "import mcdc; print('mcdc import ok')"
python -c "import numba; print(numba.__version__)"
python -c "from mpi4py import MPI; print(MPI.Get_library_version())"

python input.py --mode=numba_debug --no-progress_bar
python input.py --mode=numba --caching --clear_cache
```

```bash
cd test/regression
python run.py --name=sphere_in_cube --mode=numba
```

### Validation checkpoints
- The failing command is reproducible and reduced to a minimal input.
- Environment checks succeed (`import mcdc`, `numba`, `mpi4py`).
- Regression smoke test passes after the fix.
- Output file contains expected `runtime/*` and requested `tallies/*` datasets.

## Scope
- Handle known issues, diagnostics, and debugging patterns.
- Keep answers practical and action-oriented.

## Primary documentation references
- `docs/source/user/troubleshooting.rst`
- `docs/source/user/faq.rst`
- `docs/source/user/batch_scripts.rst`
- `docs/source/user/cpu.rst`
- `docs/source/user/gpu.rst`
- `docs/source/theory/iqmc.rst`
- `test/regression/README.md`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tests as executable behavior checks.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the listed source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `examples`
- `docs/source/examples`

## Test references
- `test/regression/README.md`
- `test/regression/run.py`
- `test/unit/run.py`

## Optional deeper inspection
- `mcdc`
- `tools/data_library_generator`

## Source entry points for unresolved issues
- `mcdc/config.py` — CLI mode/target/caching/debug switches
- `mcdc/main.py` — run pipeline and CLI override application
- `mcdc/transport/geometry/interface.py` — lost-particle and cell/surface diagnostics
- `mcdc/transport/particle_bank.py` — bank overflow/underflow and MPI rebalance failures
- `mcdc/output.py` — output write-time failures and runtime dataset schema
- `mcdc/print_.py` — fatal/warning print paths and progress behavior
- `test/regression/run.py` — deterministic regression reproduction harness
- Prefer targeted source search (for example: `rg -n "<error_token_or_symbol>" mcdc test/regression`).
