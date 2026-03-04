---
name: mcdc-parallel-hpc
description: This skill should be used when users ask about parallel and hpc in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Parallel and HPC

## High-Signal Playbook
### Route conditions
- Use this skill for MPI/GPU execution, scheduler launch recipes, module/environment compatibility, and performance-oriented run setup (`docs/source/user/batch_scripts.rst`, `docs/source/user/gpu.rst`, `docs/source/theory/ana.rst`).
- Route package install and dependency build issues to `mcdc-build-and-install`.
- Route model geometry/material definition to `mcdc-inputs-and-modeling`.
- Route run-control and output-interpretation questions to `mcdc-simulation-workflows`.

### Triage questions
1. Which scheduler/launcher is in use (`srun`, `flux run`, `jsrun`, `mpiexec`)?
2. Which hardware backend is targeted (CPU, Nvidia GPU, AMD GPU)?
3. How many ranks, nodes, and GPUs per rank/task are requested?
4. Which environment path is used (venv, conda, container)?
5. Is mpi4py linked to the same system MPI as the launcher?
6. Are you using `--caching` / `--clear_cache` intentionally?
7. Which GPU strategy is required (`event` default, `async` Nvidia-only)?

### Canonical workflow
1. Load scheduler/site modules and activate the intended Python environment (`docs/source/user/batch_scripts.rst`, `docs/source/user/troubleshooting.rst`).
2. Verify `numba` and `mpi4py` in that environment before launching jobs.
3. Run a small CPU Numba command in interactive allocation.
4. Run a small MPI CPU job to validate launcher+mpi4py compatibility.
5. Run a single-GPU test with `--target=gpu` and default event strategy.
6. Scale to multi-node templates (Slurm/Flux/LSF) using documented scripts.
7. Enable `--caching`; clear cache when changing code/runtime configuration.
8. Capture runtime metrics and scale gradually (ranks, particles, GPU arena settings).

### Minimal working example
```bash
# Slurm GPU example
srun python input.py --mode=numba --target=gpu --gpu_strategy=event --caching

# Flux MI300A example
flux run -N 2 -n 8 -g 1 --queue=mi300a \
  python input.py --mode=numba --target=gpu \
  --gpu_arena_size=100000000 --gpu_strategy=event

# LSF Nvidia example
jsrun -n 4 -r 4 -a 1 -g 1 \
  python input.py --mode=numba --target=gpu --gpu_strategy=async
```

### Pitfalls and fixes
- `mpi4py` is built against wrong MPI: rebuild from source with site compiler wrapper (`CC=mpicc ...`) (`docs/source/user/troubleshooting.rst`).
- Incorrect module ordering (compiler/MPI vs CUDA/ROCm): follow site order from batch guides (`docs/source/user/batch_scripts.rst`).
- `--gpu_strategy=async` used on unsupported backend: keep `event` unless Nvidia async path is intended (`docs/source/user/gpu.rst`).
- Long startup on first run interpreted as failure: first JIT is expected; keep `--caching` for repeats (`docs/source/user/faq.rst`).
- Stale cache after source updates: run once with `--clear_cache --caching` (`docs/source/user/batch_scripts.rst`).
- Rank/core oversubscription reduces throughput: align launcher ranks with allocated cores/GPUs.
- Container-specific launch errors (`lsetxattr`, `HYDU_create_process`): apply documented Podman/Apptainer/MPI fixes (`docs/source/user/troubleshooting.rst`).
- Missing custom toolchain paths: use `--gpu_cuda_path` / `--gpu_rocm_path` when site paths are non-standard (`mcdc/config.py`).

### Convergence and validation checks
- Compare small serial vs MPI vs GPU runs for statistical consistency in core tallies.
- Confirm second `--caching` run has lower startup latency than first run.
- Track runtime components to identify whether prep, simulation, or bank management dominates.
- Verify rank-to-device mapping behavior when using multi-rank GPU runs.
- Scale one knob at a time (ranks, nodes, particle count) and record throughput changes.

### Source escalation entry points
- `mcdc/config.py`: CLI definitions for MPI-adjacent run options and GPU controls.
- `mcdc/transport/mpi.py`: workload partitioning across ranks.
- `mcdc/transport/particle_bank.py`: source-bank rebalance and distributed bank operations.
- `mcdc/code_factory/gpu/program_builder.py`: Harmonize runtime build and device selection.
- `mcdc/code_factory/gpu/transport/simulation.py`: GPU event/async source loop and MPI-GPU coordination hooks.
- `mcdc/object_/gpu_tools.py`: GPU metadata/state containers used by runtime.

## Scope
- Handle questions about MPI/OpenMP/GPU execution, scaling, and batch systems.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/user/batch_scripts.rst`
- `docs/source/user/gpu.rst`
- `docs/source/user/cpu.rst`
- `docs/source/user/troubleshooting.rst`
- `docs/source/install.rst`
- `docs/source/theory/gpu.rst`
- `docs/source/theory/ana.rst`
- `docs/source/user/container.rst`

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
- `mcdc/config.py` — mode/target/caching/GPU runtime flags and defaults
- `mcdc/transport/mpi.py` — rank work distribution
- `mcdc/transport/particle_bank.py` — distributed bank balancing and MPI collectives
- `mcdc/code_factory/gpu/program_builder.py` — Harmonize program build and GPU setup/teardown
- `mcdc/code_factory/gpu/transport/simulation.py` — GPU event/async execution path
- `mcdc/object_/gpu_tools.py` — GPU metadata fields consumed by runtime
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
