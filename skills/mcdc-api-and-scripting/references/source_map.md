# mcdc source map: API and Scripting

Generated from source roots:
- `mcdc`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `api`
- `constructor`
- `settings`
- `tally`
- `run`
- `pythonapi`
- `singleton`
- `move`

## Fast source navigation
- `rg -n "class (Settings|Source|Surface|Cell|Tally|Simulation)|def (run|set_eigenmode|set_time_census|set_source_file)" mcdc/object_ mcdc/main.py`
- `rg -n "def (generate_output|create_tally_dataset|create_runtime_dataset)" mcdc/output.py`
- `rg -n "^from mcdc\.object_" mcdc/__init__.py`

## Suggested source entry points (function-level)
- `mcdc/__init__.py` | symbols: `settings`, `run`, `visualize`, `Material`, `Cell`, `Source`, `Tally*` | behavior checks: verify public API names exposed to user scripts.
- `mcdc/object_/simulation.py` | symbols: `Simulation.__init__`, `set_root_universe` | behavior checks: confirm singleton state and root-universe wiring.
- `mcdc/object_/settings.py` | symbols: `set_eigenmode`, `set_time_census`, `set_source_file` | behavior checks: verify mode toggles and derived fields (`N_cycle`, census arrays, source-file particle count).
- `mcdc/object_/surface.py` | symbols: `Surface.PlaneX`, `Surface.Sphere`, `Surface.move` | behavior checks: confirm constructor coefficients and moving-surface metadata.
- `mcdc/object_/cell.py` | symbols: `make_region`, `generate_RPN_tokens`, `list_surfaces` | behavior checks: verify CSG boolean expression parsing.
- `mcdc/object_/source.py` | symbols: `Source.__init__`, `Source.move` | behavior checks: confirm direction/energy/time defaults and motion setup.
- `mcdc/object_/tally.py` | symbols: `TallyGlobal`, `TallyMesh`, `decode_score_type` | behavior checks: validate score names and tally bin layout.
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: check CLI overrides and simulation handoff.
- `mcdc/output.py` | symbols: `generate_output`, `create_tally_dataset`, `create_runtime_dataset` | behavior checks: verify output schema (`tallies/*`, `runtime/*`, `k_cycle`).
- `tools/data_library_generator/util.py` | symbols: `decode_ace_name`, `load_energy_distribution` | behavior checks: verify CE library helper behavior when API questions include CE data prep.
