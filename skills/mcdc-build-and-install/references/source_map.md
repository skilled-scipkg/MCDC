# mcdc source map: Build and Install

Generated from source roots:
- `mcdc`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `install`
- `mode`
- `target`
- `cache`
- `gpu`
- `mpi4py`
- `runtime output`
- `MCDC_LIB`

## Fast source navigation
- `rg -n "parser.add_argument|clear_cache|caching|gpu_" mcdc/config.py`
- `rg -n "def (run|preparation|build_gpu_program|teardown_gpu_program|generate_output|create_runtime_datasets)" mcdc/main.py mcdc/code_factory/gpu/program_builder.py mcdc/output.py`
- `rg -n "MCDC_LIB|MCDC_ACELIB|ACEtk" tools/data_library_generator/generate.py`

## Suggested source entry points (function-level)
- `mcdc/config.py` | symbols: CLI args (`--mode`, `--target`, `--caching`, `--clear_cache`, GPU path flags) | behavior checks: verify startup configuration and cache policy.
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: verify command-line overrides and preparation stages.
- `mcdc/code_factory/gpu/program_builder.py` | symbols: `build_gpu_program`, `teardown_gpu_program` | behavior checks: verify GPU runtime build/teardown plumbing.
- `mcdc/code_factory/gpu/transport/simulation.py` | symbols: `build_gpu_progs`, `gpu_loop_source`, `setup_gpu`, `teardown_gpu` | behavior checks: inspect GPU execution-strategy behavior (`event` vs `async`).
- `mcdc/output.py` | symbols: `generate_output`, `create_runtime_datasets`, `create_runtime_dataset` | behavior checks: verify output and runtime records after installation validation runs.
- `tools/data_library_generator/generate.py` | symbols: environment checks for `MCDC_LIB` / `MCDC_ACELIB`, ACE-to-HDF5 conversion loop | behavior checks: diagnose CE library generation failures.
- `tools/data_library_generator/util.py` | symbols: `decode_ace_name`, `load_fission_multiplicity`, `load_energy_distribution` | behavior checks: debug malformed ACE-data conversion behavior.
