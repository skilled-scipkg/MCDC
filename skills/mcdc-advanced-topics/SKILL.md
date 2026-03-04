---
name: mcdc-advanced-topics
description: Consolidated low-signal advanced topics for mcdc (paper metadata, docs landing/index metadata, and RTD requirements) with docs-first routing to core workflow skills when questions are operational.
---

# mcdc: Advanced Topics

## Scope
- Handle low-frequency documentation meta topics consolidated from one-doc clusters.
- Current consolidated themes:
  - Publication/paper metadata (`docs/paper.md`)
  - Documentation landing/index metadata (`docs/source/index.rst`)
  - ReadTheDocs requirements manifest (`docs/requirements-rtd.txt`)

## Route the request
- If the request is about install/build/runtime behavior, route to `mcdc-build-and-install`.
- If the request is about first-run workflows, route to `mcdc-getting-started`.
- If the request is about input construction or geometry/material/source setup, route to `mcdc-inputs-and-modeling`.
- If the request is about simulation execution controls, route to `mcdc-simulation-workflows`.
- If the request is about HPC/MPI/GPU launches, route to `mcdc-parallel-hpc`.
- If the request is about algorithmic/theory methods, route to `mcdc-theory-and-methods`.

## Primary documentation references
- `docs/paper.md`
- `docs/source/index.rst`
- `docs/requirements-rtd.txt`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for full inventory.
- Escalate to `references/source_map.md` only when behavioral details are required and docs are insufficient.
- Cite exact file paths in responses.

## Source entry points for unresolved issues
- `README.md` — top-level project positioning and canonical external links.
- `CITATION.cff` — formal citation metadata and publication fields.
- `pyproject.toml` — package/dependency declarations relevant to RTD requirements context.
- `docs/source/index.rst` — Sphinx navigation root and top-level docs organization.
- `mcdc/__init__.py` — package public surface to map high-level docs concepts to implementation namespace.
- Prefer targeted search (for example: `rg -n "<symbol_or_keyword>" README.md CITATION.cff pyproject.toml docs/source mcdc`).
