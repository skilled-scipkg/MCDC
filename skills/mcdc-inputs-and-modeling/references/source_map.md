# mcdc source map: Inputs and Modeling

Generated from source roots:
- `mcdc`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `material`
- `surface`
- `cell`
- `region`
- `source`
- `mesh`
- `tally`
- `move`
- `geometry`

## Fast source navigation
- `rg -n "class (Material|MaterialMG|Surface|Cell|Source|MeshUniform|MeshStructured|Tally)" mcdc/object_`
- `rg -n "def (move|make_region|generate_RPN_tokens|list_surfaces)" mcdc/object_/surface.py mcdc/object_/source.py mcdc/object_/cell.py`
- `rg -n "def (inspect_geometry|locate_particle|get_distance|reflect)" mcdc/transport/geometry`

## Suggested source entry points (function-level)
- `mcdc/object_/material.py` | symbols: `Material.__init__`, `MaterialMG.__init__` | behavior checks: verify CE vs MG material construction paths.
- `mcdc/object_/surface.py` | symbols: `Surface.Plane*`, `Surface.Cylinder*`, `Surface.Sphere`, `Surface.move` | behavior checks: verify geometry primitives and moving-surface setup.
- `mcdc/object_/cell.py` | symbols: `make_region`, `generate_RPN_tokens`, `list_surfaces` | behavior checks: debug CSG region-expression parsing and surface membership.
- `mcdc/object_/source.py` | symbols: `Source.__init__`, `Source.move` | behavior checks: verify source distributions, timing, and motion setup.
- `mcdc/object_/mesh.py` | symbols: `MeshUniform.__init__`, `MeshStructured.__init__` | behavior checks: verify mesh axis/bin definitions.
- `mcdc/object_/tally.py` | symbols: `TallyGlobal`, `TallyCell`, `TallySurface`, `TallyMesh`, `decode_score_type` | behavior checks: verify score/filter setup and tally bin shapes.
- `mcdc/object_/simulation.py` | symbols: `set_root_universe` | behavior checks: verify root-universe assignment for hierarchical geometry.
- `mcdc/object_/util.py` | symbols: `move_object` | behavior checks: verify shared motion-time-grid/translation construction.
- `mcdc/transport/geometry/interface.py` | symbols: `inspect_geometry`, `locate_particle`, `surface_crossing` | behavior checks: inspect runtime cell/material location and boundary crossing logic.
- `mcdc/transport/geometry/surface/interface.py` | symbols: `evaluate`, `check_sense`, `get_distance`, `reflect` | behavior checks: inspect sense/distance math for static and moving surfaces.
