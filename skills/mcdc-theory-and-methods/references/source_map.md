# mcdc source map: Theory and Methods

Generated from source roots:
- `mcdc`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `implicit_capture`
- `weighted_emission`
- `weight_roulette`
- `population_control`
- `eigenmode`
- `movement`
- `variance`
- `convergence`

## Fast source navigation
- `rg -n "class (ImplicitCapture|WeightedEmission|WeightRoulette|PopulationControl)" mcdc/object_/technique.py`
- `rg -n "def (weight_roulette|population_control|eigenvalue_simulation|move_to_event)" mcdc/transport/technique.py mcdc/transport/simulation.py`
- `rg -n "def (set_eigenmode|set_time_census|move)" mcdc/object_/settings.py mcdc/object_/surface.py mcdc/object_/source.py`

## Suggested source entry points (function-level)
- `mcdc/object_/technique.py` | symbols: `ImplicitCapture.__call__`, `WeightedEmission.__call__`, `WeightRoulette.__call__`, `PopulationControl.__call__` | behavior checks: validate technique toggles and guards (for example threshold/target constraints).
- `mcdc/transport/technique.py` | symbols: `weight_roulette`, `population_control` | behavior checks: inspect runtime split/roulette implementation details.
- `mcdc/object_/settings.py` | symbols: `set_eigenmode`, `set_time_census` | behavior checks: verify theory-facing run-control semantics.
- `mcdc/transport/simulation.py` | symbols: `eigenvalue_simulation`, `move_to_event` | behavior checks: inspect cycle updates and event sequencing used by theory methods.
- `mcdc/object_/surface.py` | symbols: `Surface.move` | behavior checks: inspect continuous-motion model setup for transient theory use.
- `mcdc/object_/source.py` | symbols: `Source.move` | behavior checks: inspect moving-source setup path.
- `mcdc/object_/util.py` | symbols: `move_object` | behavior checks: verify shared move grid/translation construction.
- `mcdc/transport/geometry/surface/interface.py` | symbols: `check_sense`, `get_distance`, `_get_distance_moving` | behavior checks: inspect moving-surface distance/sense behavior used by transient methods.
- `mcdc/output.py` | symbols: `generate_output` (`k_cycle`, `gyration_radius`) | behavior checks: verify convergence diagnostic fields emitted for eigenvalue studies.
