---
name: mcdc-simulation-workflows
description: This skill should be used when users ask about simulation workflows in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Simulation Workflows

## High-Signal Playbook
### Route conditions
- Use this skill for run control, execution mode selection, cycle/time workflow setup, output interpretation, and repeatable run loops (`docs/source/user/cpu.rst`, `docs/source/user/gpu.rst`, `docs/source/theory/k_eigenvalue.rst`).
- Route geometry/material/input construction problems to `mcdc-inputs-and-modeling`.
- Route scheduler-level HPC launch details to `mcdc-parallel-hpc`.
- Route installation/environment failures to `mcdc-build-and-install`.

### Triage questions
1. Is this fixed-source, k-eigenvalue, or transient execution?
2. Which execution path is needed: `--mode=python`, `--mode=numba`, or `--mode=numba_debug`?
3. Are you running CPU or GPU target (`--target=cpu/gpu`)?
4. Is this serial or MPI (`srun`, `mpiexec`, `jsrun`, `flux run`)?
5. Do you need cycle controls (`N_inactive/N_active`) or time-census controls?
6. Do you need source-file initialization (`set_source_file`) for a continuation workflow?
7. What output diagnostics matter most (`k_cycle`, tallies, runtime, gyration radius)?

### Canonical workflow
1. Set baseline controls: `N_particle`, `N_batch`, and optional `output_name` (`docs/source/pythonapi/index.rst`).
2. Select execution mode and target; run once in the simplest mode that validates model logic (`docs/source/user/cpu.rst`).
3. For k-eigenvalue, configure `set_eigenmode(N_inactive, N_active, k_init, ...)` (`docs/source/theory/k_eigenvalue.rst`).
4. For transient workloads, set tally time grids and optional `set_time_census(...)` (`docs/source/pythonapi/index.rst`).
5. Add technique toggles as needed (`implicit_capture`, `population_control`, etc.).
6. Launch serial smoke run, then scale to MPI and/or GPU.
7. Validate output HDF5 content and progress diagnostics before production scaling.
8. Use regression harness (`test/regression/run.py`) for behavior checks across modes.

### Minimal working example
```python
import mcdc

mcdc.settings.N_particle = 5000
mcdc.settings.N_batch = 2
mcdc.settings.output_name = "run_demo"
mcdc.settings.set_eigenmode(N_inactive=10, N_active=50, k_init=1.0, gyration_radius="all")

mcdc.simulation.population_control(active=True)

# Optional continuation path if a source-particle file exists:
# mcdc.settings.set_source_file("source_particles.h5")

mcdc.run()
```

```bash
python input.py --mode=numba --caching
srun -n 36 python input.py --mode=numba
python input.py --mode=numba --target=gpu --gpu_strategy=event --caching
```

### Pitfalls and fixes
- First Numba run takes long: expected JIT compile cost; use `--caching` on repeats (`docs/source/user/cpu.rst`, `docs/source/user/faq.rst`).
- Too few inactive eigenvalue cycles biases tallies: increase `N_inactive` and inspect convergence diagnostics (`docs/source/theory/k_eigenvalue.rst`).
- Low particle counts produce noisy outputs: scale `N_particle`/cycles for production (`docs/source/theory/k_eigenvalue.rst`, `docs/source/examples/pincell.rst`).
- Debugging opaque compilation errors in normal Numba mode: switch to `--mode=numba_debug` (`docs/source/user/cpu.rst`).
- MPI launch failures from environment mismatch: validate mpi4py linkage and launcher choice (`docs/source/user/troubleshooting.rst`).
- Assuming continuation works without source file setup: use `set_source_file(...)` only with compatible particle datasets (`docs/source/pythonapi/index.rst`, `mcdc/object_/settings.py`).
- Reusing stale compiled cache after runtime-code changes: run with `--clear_cache --caching` (`docs/source/user/batch_scripts.rst`, `docs/source/contribution/index.rst`).

### Convergence and validation checks
- Confirm output HDF5 contains expected groups/datasets (`tallies/*`, `k_cycle`, `runtime/*` as applicable).
- For eigenvalue: check `k_cycle` stabilization and reduced uncertainty over active cycles.
- For transient: verify key tallies are stable to time-grid refinement.
- Compare serial vs MPI vs GPU results statistically (not bitwise) for the same model.
- Track runtime split (`runtime/preparation`, `runtime/simulation`, `runtime/bank_management`) to identify bottlenecks.

### Source escalation entry points
- `mcdc/main.py`: top-level run pipeline and CLI override behavior.
- `mcdc/transport/simulation.py`: fixed-source/eigenvalue loops and event stepping.
- `mcdc/object_/settings.py`: eigenmode, time census, source-file configuration logic.
- `mcdc/config.py`: mode/target/caching/GPU command-line semantics.
- `mcdc/output.py`: output dataset creation (`k_cycle`, tallies, runtime datasets).
- `mcdc/transport/particle_bank.py`: bank normalization, population control handoff, MPI rebalance.
- `mcdc/mcdc_get/simulation.py` and `mcdc/mcdc_set/simulation.py`: cycle-wise diagnostic state access.

## Scope
- Handle questions about simulation setup, execution flow, and runtime controls.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/user/cpu.rst`
- `docs/source/user/gpu.rst`
- `docs/source/theory/k_eigenvalue.rst`
- `docs/source/user/first_mcdc.rst`
- `docs/source/pythonapi/index.rst`
- `test/regression/README.md`
- `docs/source/examples/c5g7_k_eigenvalue.rst`
- `docs/source/examples/c5g7_transient.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tutorials/examples as executable usage patterns when available.
- Use tests as behavior or regression references when available.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the ranked source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `examples`
- `docs/source/examples`

## Test references
- `test`

## Optional deeper inspection
- `mcdc`
- `tools/data_library_generator`

## Source entry points for unresolved issues
- `mcdc/main.py` — run orchestration and settings override from CLI args
- `mcdc/transport/simulation.py` — fixed-source/eigenvalue loops and event progression
- `mcdc/object_/settings.py` — `set_eigenmode`, `set_time_census`, `set_source_file`
- `mcdc/config.py` — execution-mode and GPU/CPU runtime flags
- `mcdc/output.py` — HDF5 writing logic and runtime dataset creation
- `mcdc/transport/particle_bank.py` — bank management, rebalance, and normalization
- `mcdc/mcdc_get/simulation.py` / `mcdc/mcdc_set/simulation.py` — cycle diagnostics storage access
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
