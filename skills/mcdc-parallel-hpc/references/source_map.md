# mcdc source map: Parallel and HPC

Generated from source roots:
- `mcdc`
- `test/regression`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `mpi`
- `rank`
- `rebalance`
- `gpu_strategy`
- `harmonize`
- `async`
- `event`
- `scheduler`

## Fast source navigation
- `rg -n "gpu_|target|mode|caching|clear_cache" mcdc/config.py`
- `rg -n "def (distribute_work|manage_particle_banks|bank_rebalance|build_gpu_program|build_gpu_progs|gpu_loop_source)" mcdc/transport mcdc/code_factory/gpu`
- `rg -n "--mpiexec|--srun|--target" test/regression/run.py`

## Suggested source entry points (function-level)
- `mcdc/config.py` | symbols: MPI/GPU-adjacent runtime flags (`--target`, `--gpu_strategy`, `--gpu_share_stride`) | behavior checks: validate run-time flag semantics.
- `mcdc/transport/mpi.py` | symbols: `distribute_work` | behavior checks: verify per-rank work partitioning.
- `mcdc/transport/particle_bank.py` | symbols: `manage_particle_banks`, `bank_rebalance`, `total_size`, `normalize_weight` | behavior checks: inspect inter-rank load balancing and source-bank redistribution.
- `mcdc/code_factory/gpu/program_builder.py` | symbols: `build_gpu_program`, `teardown_gpu_program` | behavior checks: verify GPU runtime construction/cleanup across MPI ranks.
- `mcdc/code_factory/gpu/transport/simulation.py` | symbols: `build_gpu_progs`, `gpu_loop_source`, `setup_gpu`, `teardown_gpu` | behavior checks: inspect event/async kernel dispatch behavior.
- `mcdc/code_factory/gpu/transport/particle_bank.py` | symbols: `bank_active_particle`, `report_full_bank`, `report_empty_bank` | behavior checks: inspect GPU-side bank pressure diagnostics.
- `mcdc/object_/gpu_tools.py` | symbols: `GPUMeta` fields | behavior checks: verify state/program pointer storage used by GPU path.
- `mcdc/main.py` | symbols: `run` GPU teardown branch | behavior checks: confirm teardown path executes after GPU runs.
- `test/regression/run.py` | symbols: launcher args (`--mpiexec`, `--srun`, `--target`) | behavior checks: reproducible MPI/GPU regression command patterns.
