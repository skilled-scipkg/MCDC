---
name: mcdc-inputs-and-modeling
description: This skill should be used when users ask about inputs and modeling in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Inputs and Modeling

## High-Signal Playbook
### Route conditions
- Use this skill for geometry/material/source/tally modeling decisions and input-deck structure (`docs/source/pythonapi/index.rst`, `docs/source/theory/geometry.rst`).
- Route execution flags, run control, and output lifecycle to `mcdc-simulation-workflows`.
- Route MPI/GPU launcher details to `mcdc-parallel-hpc`.
- Route method-selection questions (variance reduction, UQ, advanced theory) to `mcdc-theory-and-methods`.

### Triage questions
1. Are you modeling multigroup (`MaterialMG`) or continuous-energy (`Material`) transport?
2. Is this fixed-source, k-eigenvalue, or transient/time-dependent?
3. Which geometry constructs are required (planes/cylinders/spheres, universes, lattices)?
4. Do any surfaces or sources move with time?
5. What tally type is needed (`TallyGlobal`, `TallyCell`, `TallySurface`, `TallyMesh`)?
6. What phase-space bins are required (space, angle, energy, time)?
7. Are CE data libraries and environment variables ready (`MCDC_LIB`)?

### Canonical workflow
1. Choose physics/data mode: CE (`Material`) or MG (`MaterialMG`) (`docs/source/pythonapi/index.rst`, `docs/source/theory/cont_energy.rst`).
2. Define surfaces with explicit boundary conditions for outer boundaries (`docs/source/theory/geometry.rst`).
3. Build regions with `+/-`, `&`, `|`, `~` and create cells from those regions.
4. Add hierarchy via universes/lattices and set root universe when needed (`docs/source/theory/geometry.rst`, `docs/source/examples/fuel_array_packaged.rst`).
5. Define sources and, for transient cases, source/surface motion with `move(...)` (`docs/source/theory/cont_movement.rst`).
6. Define tallies and grids (mesh/time/energy/angle) aligned with the quantity of interest.
7. Set run controls (`N_particle`, mode-specific settings, techniques).
8. Validate geometry with `mcdc.visualize(...)` before large production runs.

### Minimal working example
```python
import numpy as np
import mcdc

fuel = mcdc.MaterialMG(fission=np.array([1.0]), nu_p=np.array([1.2]))
water = mcdc.MaterialMG(scatter=np.array([[1.0]]))

sphere = mcdc.Surface.Sphere(center=[2.0, 2.0, 2.0], radius=1.5)
sx1 = mcdc.Surface.PlaneX(x=0.0, boundary_condition="vacuum")
sx2 = mcdc.Surface.PlaneX(x=4.0, boundary_condition="vacuum")
sy1 = mcdc.Surface.PlaneY(y=0.0, boundary_condition="vacuum")
sy2 = mcdc.Surface.PlaneY(y=4.0, boundary_condition="vacuum")
sz1 = mcdc.Surface.PlaneZ(z=0.0, boundary_condition="vacuum")
sz2 = mcdc.Surface.PlaneZ(z=4.0, boundary_condition="vacuum")

box = +sx1 & -sx2 & +sy1 & -sy2 & +sz1 & -sz2
mcdc.Cell(region=box & ~(-sphere), fill=water)
core = mcdc.Cell(name="fuel", region=-sphere, fill=fuel)

mcdc.Source(x=[0.0, 4.0], y=[0.0, 4.0], z=[0.0, 4.0], isotropic=True, energy_group=0)
mcdc.TallyCell(cell=core, scores=["fission"])
mcdc.settings.N_particle = 1000
mcdc.run()
```

```python
# Moving object pattern for transient models
surface.move(velocities=[[1.0, 0.0, 0.0], [-0.5, 0.0, 0.0]], durations=[5.0, 2.0])
```

### Pitfalls and fixes
- Wrong region syntax (`list` instead of boolean operators): use `+s1 & -s2`, not `[+s1, -s2]` (`docs/source/user/troubleshooting.rst`).
- Relying on default `boundary_condition="none"` for outer boundaries: explicitly set `vacuum` or `reflective` (`docs/source/theory/geometry.rst`).
- Operator precedence confusion in region logic: use parentheses for mixed `&`/`|`/`~` expressions (`docs/source/theory/geometry.rst`).
- CE material setup without library path: set `MCDC_LIB` and provide nuclide names/densities (`docs/source/theory/cont_energy.rst`, `docs/source/user/troubleshooting.rst`).
- Overlapping/underfilled geometry: MC/DC does not auto-prove complete non-overlapping coverage; inspect with visualization (`docs/source/theory/geometry.rst`).
- Transient source/surface movement without sufficient time resolution: add suitable tally time grids (`docs/source/examples/moving_source.rst`, `docs/source/examples/moving_pellet.rst`).
- Large fission transients exhausting active bank defaults: increase `mcdc.settings.active_bank_buffer` as seen in large examples (`examples/c5g7/transient/input.py`).

### Convergence and validation checks
- Run `mcdc.visualize(...)` slices/time snapshots to verify CSG regions and moving geometry paths.
- Confirm each tally grid axis and score matches intended observables before scaling particle count.
- Track both `mean` and `sdev` in output; require uncertainty reduction when increasing particles.
- Check sensitivity of results to mesh/time-bin refinement for transient models.
- Cross-check structure against canonical example families (`pincell`, `moving_source`, `moving_pellet`, `c5g7`).

### Source escalation entry points
- `mcdc/__init__.py`: mapping from doc-facing constructors to implementation classes.
- `mcdc/object_/material.py`: CE and MG material object definitions.
- `mcdc/object_/surface.py`: surface constructors, boundary flags, motion hooks.
- `mcdc/object_/cell.py`: region tree construction and CSG tokenization.
- `mcdc/object_/source.py`: source parameterization and movement APIs.
- `mcdc/object_/mesh.py`: `MeshUniform` and `MeshStructured` implementations.
- `mcdc/object_/tally.py`: tally object types and supported score setup.
- `mcdc/transport/geometry/interface.py`: geometry location/crossing behavior.
- `mcdc/transport/geometry/surface/interface.py`: surface sense/distance/reflect logic.

## Scope
- Handle questions about inputs, system setup, models, and physical parameterization.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/pythonapi/index.rst`
- `docs/source/theory/geometry.rst`
- `docs/source/theory/cont_energy.rst`
- `docs/source/theory/cont_movement.rst`
- `docs/source/examples/pincell.rst`
- `docs/source/examples/sphere_in_cube.rst`
- `docs/source/examples/moving_source.rst`
- `docs/source/examples/moving_pellet.rst`
- `docs/source/examples/fuel_array_packaged.rst`
- `docs/source/examples/c5g7_k_eigenvalue.rst`
- `docs/source/examples/c5g7_transient.rst`

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
- `mcdc/__init__.py` — API exports (`Material`, `Surface`, `Cell`, `Source`, `Tally*`)
- `mcdc/object_/material.py` — CE/MG material construction and data hookups
- `mcdc/object_/surface.py` — primitive definitions, boundary tags, and `move(...)`
- `mcdc/object_/cell.py` — region expression handling and cell fill semantics
- `mcdc/object_/source.py` — source distributions and source motion
- `mcdc/object_/mesh.py` and `mcdc/object_/tally.py` — mesh/tally schema
- `mcdc/transport/geometry/interface.py` — particle localization and crossing logic
- `mcdc/transport/geometry/surface/interface.py` — distance/sense on static and moving surfaces
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
