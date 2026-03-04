---
name: mcdc-theory-and-methods
description: This skill should be used when users ask about theory and methods in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Theory and Methods

## High-Signal Playbook
### Route conditions
- Use this skill for algorithmic method selection, variance-reduction choices, convergence diagnostics, transient movement theory, and UQ interpretation (`docs/source/theory/index.rst`, `docs/source/theory/variance_reduction.rst`, `docs/source/theory/uq.rst`).
- Route concrete input-deck syntax and geometry construction to `mcdc-inputs-and-modeling`.
- Route runtime/scheduler execution controls to `mcdc-simulation-workflows` or `mcdc-parallel-hpc`.
- Route install/toolchain issues to `mcdc-build-and-install`.

### Triage questions
1. Is the target problem fixed-source, k-eigenvalue, or transient?
2. Is variance/noise the primary bottleneck, or wall-time/throughput?
3. Are you using moving surfaces/sources and needing continuous-movement fidelity?
4. Do you need parametric UQ with Monte Carlo noise separation?
5. Which diagnostics are required (`k_cycle`, entropy/gyration, tally uncertainty)?
6. Are any selected techniques experimental/under active development?

### Canonical workflow
1. Start from the relevant theory page for the governing mode (`mc_basics`, `k_eigenvalue`, `cont_movement`, `variance_reduction`, `uq`).
2. Build a baseline analog run with conservative settings.
3. Add k-eigenvalue cycle controls or transient census controls as needed.
4. Activate variance-reduction techniques incrementally (implicit capture, roulette, weighted emission, population control).
5. For moving geometry/source problems, apply `move(...)` with piecewise-constant velocity segments.
6. For UQ studies, separate parametric variance from transport noise per deconvolution guidance.
7. Validate with cycle/history diagnostics before increasing model complexity.
8. Promote only stable method combinations to production runs.

### Minimal working example
```python
import mcdc

# k-eigenvalue method controls
mcdc.settings.set_eigenmode(N_inactive=50, N_active=200, k_init=1.0, gyration_radius="all")

# Variance-reduction stack
mcdc.simulation.implicit_capture(active=True)
mcdc.simulation.weight_roulette(weight_threshold=0.25, weight_target=1.0)
mcdc.simulation.weighted_emission(active=True, weight_target=1.0)
mcdc.simulation.population_control(active=True)

mcdc.run()
```

```python
# Continuous movement setup pattern
surface.move(velocities=[[0.0, 0.0, -2.0], [0.0, 0.0, 4.0]], durations=[5.0, 2.0])
source.move(velocities=[[1.0, 0.0, 0.0]], durations=[7.0])
```

### Pitfalls and fixes
- Weight roulette configured with `weight_threshold > weight_target`: invalid; keep threshold lower (`docs/source/theory/variance_reduction.rst`, `mcdc/object_/technique.py`).
- Implicit capture without a low-weight cleanup strategy can inflate memory/time costs: pair with roulette or windows (`docs/source/theory/variance_reduction.rst`).
- Too few inactive cycles in eigenvalue problems biases tallies and `k_eff`: increase inactive cycles and verify convergence (`docs/source/theory/k_eigenvalue.rst`).
- Interpreting early-cycle `k_eff` as converged: monitor cycle trends and diagnostics before trusting statistics (`docs/source/theory/k_eigenvalue.rst`).
- Treating domain decomposition as fully mature production feature: docs explicitly mark active development (`docs/source/theory/domain_decomp.rst`).
- Assuming compressed sensing is fully integrated into all workflows: docs position it as active research (`docs/source/theory/compressed_sensing.rst`).
- Ignoring MC noise in UQ variance estimation: apply variance deconvolution framing (`docs/source/theory/uq.rst`).
- Using stepwise geometry approximations when continuous movement is available: prefer continuous movement for transient fidelity (`docs/source/theory/cont_movement.rst`).

### Convergence and validation checks
- For eigenvalue runs: verify `k_cycle` plateau and reduced active-cycle uncertainty.
- Use Shannon entropy/gyration-radius diagnostics where enabled to assess source convergence.
- Confirm uncertainty reduction trends with particle/cycle increases for key tallies.
- For transient movement: test time-grid refinement and confirm stable macroscopic observables.
- For UQ: verify deconvolved variance estimates stabilize with additional realizations.

### Source escalation entry points
- `mcdc/object_/technique.py`: user-facing technique toggles and parameter guards.
- `mcdc/transport/technique.py`: operational implementation of roulette and population control.
- `mcdc/object_/settings.py`: eigenmode/time-census controls used by theoretical workflows.
- `mcdc/transport/simulation.py`: where cycle loops and event sequencing are executed.
- `mcdc/transport/geometry/surface/interface.py`: moving-surface distance/sense behavior.
- `mcdc/object_/source.py`: source movement state and user API.
- `mcdc/mcdc_set/population_control.py` and `mcdc/mcdc_get/population_control.py`: generated accessors for population-control state.

## Scope
- Handle questions about theoretical background and algorithmic methods.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/theory/index.rst`
- `docs/source/theory/mc_basics.rst`
- `docs/source/theory/k_eigenvalue.rst`
- `docs/source/theory/variance_reduction.rst`
- `docs/source/theory/cont_movement.rst`
- `docs/source/theory/uq.rst`
- `docs/source/theory/domain_decomp.rst`
- `docs/source/theory/compressed_sensing.rst`
- `docs/source/theory/ana.rst`

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
- `mcdc/object_/technique.py` — high-level technique controls
- `mcdc/transport/technique.py` — runtime behavior of roulette/population control
- `mcdc/object_/settings.py` — eigenmode/time-census settings state
- `mcdc/transport/simulation.py` — iteration loops and event handling
- `mcdc/transport/geometry/surface/interface.py` — moving-surface math in transport
- `mcdc/object_/source.py` — source kinematics API
- `mcdc/mcdc_set/population_control.py` and `mcdc/mcdc_get/population_control.py` — generated state setters/getters
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
