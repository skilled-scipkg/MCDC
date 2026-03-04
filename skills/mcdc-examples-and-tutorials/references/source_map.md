# mcdc source map: Examples and Tutorials

Generated from source roots:
- `mcdc`
- `examples`
- `test/regression`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `example`
- `input.py`
- `process-output`
- `transient`
- `eigenvalue`
- `moving`
- `c5g7`
- `pincell`

## Fast source navigation
- `rg -n "mcdc\.|set_eigenmode|set_time_census|move\(" examples`
- `rg -n "def (run|generate_output|create_tally_dataset)" mcdc/main.py mcdc/output.py`
- `rg -n "--mode|--target|--mpiexec|--srun" test/regression/run.py`

## Suggested source entry points (function-level)
- `examples/sphere_in_cube/input.py` | symbols: `MaterialMG`, `Surface.Sphere`, `TallyCell`, `mcdc.run` | behavior checks: verify baseline MG geometry+tally flow.
- `examples/moving_source/input.py` | symbols: `Source.move`, `TallyMesh(time=...)` | behavior checks: verify moving-source time-bin setup.
- `examples/moving_pellet/input.py` | symbols: `Surface.move`, `TallyMesh(time=...)` | behavior checks: verify moving-surface transient setup.
- `examples/pincell/input.py` | symbols: `Material`, `set_eigenmode` | behavior checks: verify CE/eigenvalue setup path.
- `examples/c5g7/k-eigenvalue/input.py` | symbols: `Lattice`, `set_root_universe`, `set_eigenmode` | behavior checks: verify full-core lattice and eigenvalue controls.
- `examples/c5g7/transient/input.py` | symbols: moving control-rod surfaces (`move`), transient tallies | behavior checks: verify large transient workflow.
- `mcdc/object_/surface.py` | symbols: `Surface.Plane*`, `Surface.Cylinder*`, `Surface.Sphere`, `Surface.move` | behavior checks: validate geometry constructor semantics used by examples.
- `mcdc/object_/settings.py` | symbols: `set_eigenmode`, `set_time_census` | behavior checks: confirm run-control API behavior used by examples.
- `mcdc/output.py` | symbols: `create_tally_dataset`, `create_runtime_dataset` | behavior checks: confirm expected HDF5 schema for process scripts.
- `test/regression/run.py` | symbols: CLI args `--mode`, `--target`, `--mpiexec`, `--srun`, `--name` | behavior checks: verify reproducible validation harness usage.
