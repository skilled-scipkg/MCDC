---
name: mcdc-build-and-install
description: This skill should be used when users ask about build and install in mcdc; it prioritizes documentation references and then source inspection only for unresolved details.
---

# mcdc: Build and Install

## High-Signal Playbook
### Route conditions
- Use this skill for environment creation, package installation, dependency checks, and containerized setup (`docs/source/install.rst`, `docs/source/user/container.rst`).
- Route first simulation setup and input construction to `mcdc-getting-started` (`docs/source/user/first_mcdc.rst`).
- Route scheduler/MPI/GPU launch details to `mcdc-parallel-hpc` (`docs/source/user/batch_scripts.rst`, `docs/source/user/gpu.rst`).
- Route runtime failures after installation to `mcdc-troubleshooting` (`docs/source/user/troubleshooting.rst`, `docs/source/user/faq.rst`).

### Triage questions
1. Are you on a local workstation or HPC system?
2. Do you need a stable release (`pip install mcdc`) or editable source install (`pip install -e .`)?
3. Are you CPU-only, MPI CPU, or GPU-targeted?
4. Do you need mpi4py built against a site MPI module?
5. Are you using native install or container workflow (Docker/Podman/Apptainer)?
6. Will you run continuous-energy problems that require `MCDC_LIB`?

### Canonical workflow
1. Pick environment strategy: `venv` for standard installs, `conda` for non-standard/HPC stacks (`docs/source/install.rst`, `docs/source/user/faq.rst`).
2. Activate environment and load site modules first on HPC (`docs/source/install.rst`, `docs/source/user/troubleshooting.rst`).
3. Install MC/DC from PyPI or source clone (`docs/source/install.rst`).
4. If HPC MPI issues are likely, build mpi4py from source (`docs/source/user/troubleshooting.rst`).
5. Verify baseline dependencies (`numba`, `mpi4py`) and import `mcdc`.
6. Run a small CPU test input, then switch to `--mode=numba` and optionally `--caching`.
7. For GPU runs, add `--target=gpu` and choose scheduler strategy (`event` default; `async` Nvidia-only) (`docs/source/user/gpu.rst`).
8. For CE workflows, set `MCDC_LIB` or generate data with `tools/data_library_generator` (`docs/source/install.rst`).

### Minimal working example
```bash
python -m venv mcdc-venv
source mcdc-venv/bin/activate
pip install -e .

python -c "import mcdc; print('MC/DC OK')"
python -c "import numba; print(numba.__version__)"
python -c "from mpi4py import MPI; print(MPI.Get_library_version())"

python input.py --mode=numba --caching
```

### Pitfalls and fixes
- `install.sh` fails outside conda: run it inside an activated conda env (`docs/source/install.rst`).
- `mpi4py` launch/import errors on HPC: rebuild with `CC=mpicc pip install --no-binary mpi4py mpi4py` after loading site MPI module (`docs/source/user/troubleshooting.rst`).
- `ModuleNotFoundError: mcdc`: activate the same environment used for install (`docs/source/user/faq.rst`).
- Numba typing/lowering failures with old versions: enforce `numba>=0.60.0` (`docs/source/user/troubleshooting.rst`).
- CE run fails with `MCDC_LIB is not set`: export `MCDC_LIB=/path/to/library` (`docs/source/user/troubleshooting.rst`).
- Container errors (`lsetxattr`, `HYDU_create_process`): apply documented Podman/Apptainer fixes and `mpirun -launcher fork` when needed (`docs/source/user/troubleshooting.rst`, `docs/source/contribution/container-dev.rst`).
- Numba first run appears “stuck”: initial JIT cost is expected; use `--caching` for subsequent runs (`docs/source/user/faq.rst`).

### Convergence and validation checks
- `python -c "import mcdc"` succeeds in the target environment.
- `mpi4py` reports the expected site MPI library string.
- A small `--mode=numba` run completes and writes `<output_name>.h5`.
- Repeated run with `--caching` shows reduced startup compile time.
- GPU run creates `__harmonize_cache__` and completes without runtime compile errors.

### Source escalation entry points
- `mcdc/config.py`: CLI flags for mode/target/caching/GPU runtime options.
- `mcdc/main.py`: run orchestration and command-line override behavior.
- `mcdc/object_/settings.py`: defaults for output naming, eigenmode, time census, source-file loading.
- `mcdc/code_factory/gpu/program_builder.py`: Harmonize GPU build/runtime plumbing.
- `mcdc/output.py`: output file creation logic and runtime datasets.
- `tools/data_library_generator/generate.py`: ACE-to-HDF5 CE data library generation path.

## Scope
- Handle questions about build, installation, compilation, and environment setup.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `docs/source/install.rst`
- `docs/source/user/faq.rst`
- `docs/source/user/troubleshooting.rst`
- `docs/source/user/container.rst`
- `docs/source/contribution/container-dev.rst`
- `docs/source/user/first_mcdc.rst`
- `docs/README.md`

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
- `mcdc/config.py` — runtime CLI options (`--mode`, `--target`, caching, GPU flags)
- `mcdc/main.py` — startup/preparation/simulation/output orchestration
- `mcdc/object_/settings.py` — install-adjacent runtime defaults and file-driven source loading
- `mcdc/code_factory/gpu/program_builder.py` — GPU compile/runtime setup
- `mcdc/output.py` — HDF5 output file creation and runtime metadata
- `tools/data_library_generator/generate.py` — CE data generation from ACE input
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" mcdc tools/data_library_generator`).
