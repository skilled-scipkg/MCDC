# mcdc source map: Troubleshooting

Generated from source roots:
- `mcdc`
- `test/regression`
- `test/unit`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `error`
- `exception`
- `lost particle`
- `mpi`
- `numba`
- `cache`
- `output`
- `regression`

## Fast source navigation
- `rg -n "print_error|print_warning|numba_debug|clear_cache|gpu_strategy" mcdc/config.py mcdc/print_.py`
- `rg -n "def (run|preparation|report_lost_particle|report_full_bank|report_empty_bank|generate_output)" mcdc/main.py mcdc/transport mcdc/output.py`
- `rg -n "--mode|--target|--mpiexec|--srun|--name" test/regression/run.py`

## Suggested source entry points (function-level)
- `mcdc/config.py` | symbols: parser args (`--mode`, `--target`, `--caching`, `--clear_cache`, GPU options) | behavior checks: verify runtime flags and cache-clearing conditions.
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: verify CLI overrides and stage transitions (`preparation`, `simulation`, `output`).
- `mcdc/print_.py` | symbols: `print_error`, `print_warning`, `print_progress*` | behavior checks: verify fatal/diagnostic output behavior.
- `mcdc/transport/geometry/interface.py` | symbols: `inspect_geometry`, `check_cell`, `report_lost_particle` | behavior checks: diagnose geometry/lost-particle failures.
- `mcdc/transport/particle_bank.py` | symbols: `report_full_bank`, `report_empty_bank`, `manage_particle_banks`, `bank_rebalance` | behavior checks: diagnose bank overflow/underflow and MPI rebalancing issues.
- `mcdc/output.py` | symbols: `generate_output`, `create_tally_dataset`, `create_runtime_datasets` | behavior checks: diagnose missing/partial HDF5 outputs.
- `mcdc/transport/simulation.py` | symbols: `fixed_source_simulation`, `eigenvalue_simulation`, `move_to_event` | behavior checks: inspect failure location in transport loop.
- `test/regression/run.py` | symbols: CLI args and output comparison loop | behavior checks: reproduce failures and confirm fixes.
- `test/unit/run.py` | symbols: unit test discovery/dispatch loop | behavior checks: quick API-level sanity after patching.
