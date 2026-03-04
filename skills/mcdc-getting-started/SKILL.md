---
name: mcdc-getting-started
description: This skill should be used when users ask about getting started in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Getting Started

## High-Signal Playbook
### Route conditions
- Use this skill for first-run onboarding, initial input construction, and basic output reading (`docs/source/user/first_mcdc.rst`, `docs/source/user/index.rst`).
- Route installation/environment blockers to `mcdc-build-and-install` (`docs/source/install.rst`).
- Route deep geometry/material modeling to `mcdc-inputs-and-modeling` (`docs/source/theory/geometry.rst`, `docs/source/pythonapi/index.rst`).
- Route multi-rank/GPU scheduler workflows to `mcdc-parallel-hpc` (`docs/source/user/cpu.rst`, `docs/source/user/gpu.rst`, `docs/source/user/batch_scripts.rst`).

### Triage questions
1. Is MC/DC already importable in your active environment?
2. Are you running fixed-source, k-eigenvalue, or transient?
3. Are you using multigroup inputs (`MaterialMG`) or continuous-energy inputs (`Material` + `MCDC_LIB`)?
4. Do you need CPU python mode, CPU numba mode, or GPU target?
5. Are you running serial, MPI, or both?
6. What output do you need first (`flux`, `k_eff`, time-dependent tally)?

### Canonical workflow
1. Start from the `input -> run -> post-process` flow in the first simulation guide (`docs/source/user/first_mcdc.rst`).
2. Build a minimal input deck: materials, surfaces, cells, source, tally, settings.
3. Set `mcdc.settings.N_particle` first; then add eigenmode/time options only if needed.
4. Run serial python mode once to confirm model setup.
5. Switch to `--mode=numba` (or `--mode=numba_debug` when debugging) (`docs/source/user/cpu.rst`).
6. Inspect `<output_name>.h5` tallies, including standard deviation datasets.
7. Increase particles/cycles after baseline correctness is confirmed.

### Minimal working example
```python
import numpy as np
import mcdc

m1 = mcdc.MaterialMG(capture=np.array([1.0]))
m2 = mcdc.MaterialMG(capture=np.array([1.5]))

s1 = mcdc.Surface.PlaneZ(z=0.0, boundary_condition="vacuum")
s2 = mcdc.Surface.PlaneZ(z=2.0)
s3 = mcdc.Surface.PlaneZ(z=4.0, boundary_condition="vacuum")

mcdc.Cell(region=+s1 & -s2, fill=m1)
mcdc.Cell(region=+s2 & -s3, fill=m2)

mcdc.Source(z=[0.0, 4.0], isotropic=True, energy_group=0)

mesh = mcdc.MeshStructured(z=np.linspace(0.0, 4.0, 41))
mcdc.TallyMesh(mesh=mesh, scores=["flux", "collision"])

mcdc.settings.N_particle = 1000
mcdc.run()
```

```bash
python input.py
python input.py --mode=numba --caching
```

### Pitfalls and fixes
- Cell region passed as Python list instead of boolean region operators: use `+s1 & -s2` and related CSG operators (`docs/source/user/troubleshooting.rst`).
- Outer surfaces left with default boundary (`none`): explicitly set `vacuum` or `reflective` where needed (`docs/source/theory/geometry.rst`).
- CE examples fail because `MCDC_LIB` is unset: export the CE library path before run (`docs/source/user/troubleshooting.rst`).
- Running from notebooks with MPI/Numba causes avoidable launch issues: run from terminal (`docs/source/user/cpu.rst`).
- Treating Monte Carlo means without uncertainty: always report tally standard deviation (`docs/source/user/first_mcdc.rst`).
- First Numba run is slow: expected JIT overhead, then use `--caching` (`docs/source/user/faq.rst`).

### Convergence and validation checks
- Output file exists and includes `tallies/*/grid` and score datasets.
- `mean` and `sdev` arrays exist for target scores and have matching shapes.
- Increasing `N_particle` reduces relative noise in the same observable.
- For eigenvalue mode, `k_cycle` trends toward a stable plateau after inactive cycles.
- Optional geometry sanity check with `mcdc.visualize(...)` before large runs.

### Source escalation entry points
- `mcdc/__init__.py`: public API objects used directly in input scripts.
- `mcdc/object_/cell.py`: region operators and cell construction behavior.
- `mcdc/object_/surface.py`: supported surface constructors and move hooks.
- `mcdc/object_/source.py`: source definitions and source motion interface.
- `mcdc/object_/settings.py`: run controls (`N_particle`, eigenmode, time census, source-file).
- `mcdc/main.py`: run path from preparation through output.
- `mcdc/output.py`: HDF5 structure for tallies and runtime records.

## Scope
- Handle questions about initial setup, quickstarts, and core concepts.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/user/first_mcdc.rst`
- `docs/source/user/index.rst`
- `docs/source/install.rst`
- `docs/source/user/cpu.rst`
- `docs/source/user/gpu.rst`
- `docs/source/user/faq.rst`
- `docs/source/user/container.rst`

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
- `mcdc/__init__.py` — public API names used in most starter input decks
- `mcdc/object_/cell.py` — CSG region expression parsing and cell creation
- `mcdc/object_/surface.py` — geometric primitives and boundary conditions
- `mcdc/object_/source.py` — source definitions and source motion
- `mcdc/object_/settings.py` — simulation knobs (`N_particle`, eigenmode, census)
- `mcdc/main.py` — run-time orchestration and argument overrides
- `mcdc/output.py` — output dataset generation
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
