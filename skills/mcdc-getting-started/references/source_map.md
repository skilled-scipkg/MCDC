# mcdc source map: Getting Started

Generated from source roots:
- `mcdc`
- `examples`
- `test/regression`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `first run`
- `settings`
- `output`
- `surface`
- `cell`
- `source`
- `tally`
- `visualize`

## Fast source navigation
- `rg -n "^from mcdc\.object_|from mcdc\.main import run" mcdc/__init__.py`
- `rg -n "def (set_eigenmode|set_time_census|set_source_file|run|preparation|generate_output|visualize)" mcdc/object_/settings.py mcdc/main.py mcdc/output.py mcdc/visualize.py`
- `rg -n "mcdc\.settings|mcdc\.run|mcdc\.Tally" examples/sphere_in_cube/input.py`

## Suggested source entry points (function-level)
- `mcdc/__init__.py` | symbols: API exports (`Material*`, `Surface`, `Cell`, `Source`, `Tally*`, `run`) | behavior checks: verify what starter scripts can import directly.
- `mcdc/object_/settings.py` | symbols: `set_eigenmode`, `set_time_census`, `set_source_file` | behavior checks: verify starter run controls and continuation settings.
- `mcdc/object_/surface.py` | symbols: `Surface.PlaneX`, `Surface.PlaneY`, `Surface.PlaneZ`, `Surface.Sphere` | behavior checks: confirm baseline geometry constructor semantics.
- `mcdc/object_/cell.py` | symbols: region operators and `generate_RPN_tokens` | behavior checks: debug common region-expression mistakes.
- `mcdc/object_/material.py` | symbols: `Material`, `MaterialMG` constructors | behavior checks: verify MG vs CE model setup.
- `mcdc/object_/source.py` | symbols: `Source.__init__` | behavior checks: verify default source distributions and time setup.
- `mcdc/object_/mesh.py` | symbols: `MeshUniform`, `MeshStructured` constructors | behavior checks: verify tally mesh axis setup.
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: inspect run pipeline and CLI overrides.
- `mcdc/output.py` | symbols: `generate_output`, `create_tally_dataset`, `create_runtime_dataset` | behavior checks: verify starter output schema (`tallies/*`, `runtime/*`).
- `mcdc/visualize.py` | symbols: `visualize` | behavior checks: geometry sanity-check path before long runs.
