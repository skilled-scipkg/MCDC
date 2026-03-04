---
name: mcdc-api-and-scripting
description: This skill should be used when users ask about api and scripting in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: API and Scripting

## High-Signal Playbook
### Route conditions
- Use this skill for Python API usage, object constructor behavior, settings/technique calls, and script-level run orchestration (`docs/source/pythonapi/index.rst`).
- Route environment/build issues to `mcdc-build-and-install`.
- Route modeling design questions to `mcdc-inputs-and-modeling`.
- Route runtime control and output interpretation to `mcdc-simulation-workflows`.

### Triage questions
1. Is the user asking about object construction (`Material`, `Surface`, `Cell`, `Source`, `Tally*`) or runtime control (`mcdc.settings`, `mcdc.run`)?
2. Do they need minimal usage syntax, or behavior verification against source implementation?
3. Are they running fixed-source, eigenvalue, or transient workflows?
4. Do they need API behavior in Python mode only, or also Numba/MPI/GPU execution?

### Canonical workflow
1. Start from `docs/source/pythonapi/index.rst` for API surface and method signatures.
2. Validate object wiring in a minimal input script (`mcdc.MaterialMG`, `mcdc.Surface.*`, `mcdc.Cell`, `mcdc.Source`, `mcdc.Tally*`).
3. Set run controls through `mcdc.settings` (`N_particle`, optional `set_eigenmode` / `set_time_census`).
4. Run `mcdc.run()` in Python mode first, then Numba mode.
5. Validate output datasets and API side effects before scaling problem size.

### Minimal API smoke test
```python
import numpy as np
import mcdc

mat = mcdc.MaterialMG(capture=np.array([1.0]))
s1 = mcdc.Surface.PlaneZ(z=0.0, boundary_condition="vacuum")
s2 = mcdc.Surface.PlaneZ(z=1.0, boundary_condition="vacuum")
mcdc.Cell(region=+s1 & -s2, fill=mat)
mcdc.Source(z=[0.0, 1.0], isotropic=True, energy_group=0)
mcdc.TallyGlobal(scores=["flux"])

mcdc.settings.N_particle = 200
mcdc.run()
```

```bash
python input.py
python input.py --mode=numba --caching
```

### Validation checkpoints
- `output.h5` is created and contains `tallies` and `runtime` groups.
- Requested tally scores have both `mean` and `sdev` datasets.
- Switching from Python mode to Numba mode preserves statistical consistency.

## Scope
- Handle questions about language bindings, APIs, and programmatic interfaces.
- Keep responses architectural unless explicit function-level detail is requested.

## Primary documentation references
- `docs/source/pythonapi/index.rst`
- `docs/source/user/first_mcdc.rst`
- `docs/source/_templates/omcclass.rst`
- `docs/source/_templates/omcfunction.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use examples/tests as executable API behavior checks.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the listed source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `examples`
- `docs/source/examples`

## Test references
- `test/unit/run.py`
- `test/regression/run.py`

## Optional deeper inspection
- `mcdc`
- `tools/data_library_generator`

## Source entry points for unresolved issues
- `mcdc/__init__.py` — top-level API exports
- `mcdc/object_/simulation.py` — global simulation singleton and `set_root_universe`
- `mcdc/object_/settings.py` — `set_eigenmode`, `set_time_census`, `set_source_file`
- `mcdc/object_/surface.py` — constructors and `move(...)`
- `mcdc/object_/cell.py` — region expression and RPN generation
- `mcdc/object_/source.py` — source distributions and source motion
- `mcdc/object_/tally.py` — tally constructors and score decoding
- `mcdc/main.py` — `run()` orchestration
- `mcdc/output.py` — output dataset schema
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
