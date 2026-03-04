---
name: mcdc-index
description: This skill should be used when users ask how to use mcdc and the correct generated documentation skill must be selected before going deeper into source code.
---

# mcdc Skills Index

## Route the request
- Classify the request into one topic skill first.
- Keep docs-first behavior: only inspect source after the selected topic skill's docs are insufficient.
- If the user asks to "get something running" quickly, route first to `mcdc-getting-started`, then hand off to `mcdc-simulation-workflows` or `mcdc-parallel-hpc` as needed.

## Generated topic skills
- `mcdc-inputs-and-modeling`: Inputs and Modeling (geometry/material/source/tally setup and physical parameterization)
- `mcdc-build-and-install`: Build and Install (environment setup, package installation, dependency/toolchain issues)
- `mcdc-theory-and-methods`: Theory and Methods (algorithm and technique selection)
- `mcdc-getting-started`: Getting Started (first-run onboarding and baseline workflow)
- `mcdc-simulation-workflows`: Simulation Workflows (run control, execution flow, output checks)
- `mcdc-parallel-hpc`: Parallel and HPC (MPI/GPU execution and scheduler workflows)
- `mcdc-examples-and-tutorials`: Examples and Tutorials (runnable examples and cookbook patterns)
- `mcdc-api-and-scripting`: API and Scripting (Python API behavior and programmatic interfaces)
- `mcdc-troubleshooting`: Troubleshooting (diagnostics and failure triage)
- `mcdc-advanced-topics`: Advanced Topics (paper/docs metadata and RTD requirements)

## Shared roots
- `docs`
- `examples`
- `docs/source/examples`
- `test`
- `mcdc`
- `tools/data_library_generator`

## Escalation sequence
1. Start from the selected topic skill's `SKILL.md` primary references.
2. If needed, inspect that same skill's doc map in its `references/` folder.
3. If ambiguity remains, inspect that same skill's source map in its `references/` folder.
4. Use targeted symbol search while inspecting source (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
