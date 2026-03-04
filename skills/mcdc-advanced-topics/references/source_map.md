# mcdc source map: Advanced Topics

Generated from source roots:
- `mcdc`
- `tools/data_library_generator`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `citation`
- `paper`
- `requirements-rtd`
- `docs index`
- `dependency`
- `version`

## Fast source navigation
- `rg -n "version|dependencies|project|requires" pyproject.toml docs/requirements-rtd.txt`
- `rg -n "def (run|preparation|create_runtime_datasets)" mcdc/main.py mcdc/output.py`
- `rg -n "parser.add_argument" mcdc/config.py`

## Suggested source entry points (function-level)
- `README.md` | symbols: project overview links | behavior checks: verify top-level positioning and external references used by docs metadata.
- `CITATION.cff` | symbols: citation metadata fields | behavior checks: verify publication/citation identifiers.
- `pyproject.toml` | symbols: package metadata and dependency declarations | behavior checks: verify installation and RTD dependency context.
- `docs/source/index.rst` | symbols: top-level toctree | behavior checks: verify docs landing and topic routing.
- `docs/requirements-rtd.txt` | symbols: RTD dependency pins | behavior checks: verify doc-build package expectations.
- `mcdc/config.py` | symbols: runtime CLI declarations | behavior checks: verify docs statements about run flags against implementation.
- `mcdc/main.py` | symbols: `run`, `preparation` | behavior checks: verify high-level runtime behavior when docs claims need code confirmation.
- `mcdc/output.py` | symbols: `create_runtime_datasets` | behavior checks: verify runtime metadata claims in documentation.
