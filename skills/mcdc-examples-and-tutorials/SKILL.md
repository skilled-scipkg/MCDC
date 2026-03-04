---
name: mcdc-examples-and-tutorials
description: This skill should be used when users ask about examples and tutorials in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Examples and Tutorials

## High-Signal Playbook
### Route conditions
- Use this skill for runnable example selection, adapting example input decks, and validating outputs against known scripts (`docs/source/examples/index.rst`, `examples/*/input.py`).
- Route installation/environment blockers to `mcdc-build-and-install`.
- Route deep API semantics to `mcdc-api-and-scripting`.
- Route algorithm/method tradeoffs to `mcdc-theory-and-methods`.

### Triage questions
1. Which workflow is needed: fixed-source, eigenvalue, transient, moving geometry/source, or full-core benchmark?
2. Is the user asking for CPU-only smoke tests or MPI/GPU variants?
3. Do they need multigroup-only examples or continuous-energy examples (`MCDC_LIB` required)?
4. Do they need example output post-processing scripts as well?

### Canonical workflow
1. Select the closest documented example page in `docs/source/examples/index.rst`.
2. Run the matching in-tree script from `examples/<case>/input.py`.
3. If provided, run the companion post-processing script.
4. Apply only one change at a time (particles, batches, mesh/time bins, boundary settings).
5. Validate against known output structure before scaling.

### Minimal runnable commands
```bash
python examples/sphere_in_cube/input.py --mode=numba --caching
python examples/moving_source/input.py --mode=numba --caching
python examples/c5g7/k-eigenvalue/input.py --mode=numba --caching
```

```bash
python examples/sphere_in_cube/process-output.py
python examples/moving_source/process-output.py
python examples/c5g7/k-eigenvalue/process-output.py
```

### Validation checkpoints
- Run produces an HDF5 output file with `tallies` and `runtime` groups.
- Requested scores contain both `mean` and `sdev` datasets.
- For eigenvalue examples, `k_cycle` exists and trends toward stability.
- For moving-source/pellet examples, time-binned tallies change smoothly with time.

## Scope
- Handle questions about worked examples, tutorials, and cookbook usage.
- Keep responses practical and example-driven.

## Primary documentation references
- `docs/source/examples/index.rst`
- `docs/source/examples/pincell.rst`
- `docs/source/examples/moving_source.rst`
- `docs/source/examples/moving_pellet.rst`
- `docs/source/examples/c5g7_k_eigenvalue.rst`
- `docs/source/examples/c5g7_transient.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use example scripts as executable truth for commands/inputs.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the listed source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `examples`
- `docs/source/examples`

## Test references
- `test/regression/README.md`
- `test/regression/run.py`

## Optional deeper inspection
- `mcdc`
- `tools/data_library_generator`

## Source entry points for unresolved issues
- `examples/sphere_in_cube/input.py` — compact MG fixed-source setup
- `examples/moving_source/input.py` — source motion and transient tally pattern
- `examples/moving_pellet/input.py` — moving surfaces plus transient tallies
- `examples/pincell/input.py` — CE eigenvalue setup with `set_eigenmode`
- `examples/c5g7/k-eigenvalue/input.py` — large lattice/eigenvalue workflow
- `examples/c5g7/transient/input.py` — large transient + moving control rods
- `mcdc/object_/surface.py` — constructor/motion behavior used by examples
- `mcdc/object_/settings.py` — run-control methods used by examples
- `mcdc/output.py` — output layout consumed by process scripts
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" examples mcdc test/regression`).
