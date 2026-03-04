# mcdc source map: Simulation Workflows

Generated from source roots:
- `mcdc`
- `test/regression`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `fixed source`
- `eigenvalue`
- `time census`
- `source bank`
- `k_cycle`
- `runtime`
- `output`
- `transient`

## Fast source navigation
- `rg -n "def (run|preparation|fixed_source_simulation|eigenvalue_simulation|source_loop|move_to_event)" mcdc/main.py mcdc/transport/simulation.py`
- `rg -n "def (set_eigenmode|set_time_census|set_source_file)" mcdc/object_/settings.py`
- `rg -n "def (generate_output|create_tally_dataset|create_runtime_dataset)" mcdc/output.py`

## Suggested source entry points (function-level)
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: verify stage ordering and CLI override behavior.
- `mcdc/transport/simulation.py` | symbols: `fixed_source_simulation`, `eigenvalue_simulation`, `source_loop`, `move_to_event`, `step_particle` | behavior checks: inspect control flow for fixed-source/eigenvalue/transient events.
- `mcdc/object_/settings.py` | symbols: `set_eigenmode`, `set_time_census`, `set_source_file` | behavior checks: verify cycle/time/source-file controls.
- `mcdc/transport/particle_bank.py` | symbols: `promote_future_particles`, `manage_particle_banks`, `normalize_weight` | behavior checks: inspect census and source-bank transitions.
- `mcdc/output.py` | symbols: `generate_output`, `create_tally_dataset`, `create_runtime_dataset` | behavior checks: verify output dataset creation (`k_cycle`, `tallies/*`, `runtime/*`).
- `mcdc/mcdc_get/simulation.py` | symbols: `k_cycle_all`, `k_cycle_chunk`, `gyration_radius_all` | behavior checks: verify getter views used for diagnostics/output.
- `mcdc/mcdc_set/simulation.py` | symbols: `k_cycle`, `k_cycle_all`, `gyration_radius` | behavior checks: verify setter paths used in transport updates.
- `test/regression/run.py` | symbols: `--mode`, `--target`, `--name`, comparison loop | behavior checks: fast cross-mode validation harness.
