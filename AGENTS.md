# Repository maintenance guidance

## Purpose and modification boundary

This repository exists to preserve the original TDA/SAE exploration notebook while applying the minimum SAE Lens v6 compatibility fix. Keep the canonical and executed notebooks’ cell types and source text identical. Do not refactor or reformat upstream notebook code unless a requested compatibility fix makes it necessary and the provenance document is updated.

## Environment and credentials

- Use Python 3.12 and `uv` for dependency changes. Update both `pyproject.toml` and `uv.lock` together.
- Keep Cobalt license keys and all other credentials in local environment state such as `.env`; never commit them.
- The notebook itself makes no runtime LLM prompt calls. Do not add a prompt-logging subsystem unless future code actually introduces such calls.

## Notebook artifacts

- Keep the small canonical notebook at the repository root.
- Track executed notebooks under `artifacts/` with Git LFS. Confirm tracking with `git lfs ls-files` before publishing.
- Do not commit generated `explanations.json`, `feature_vecs.npy`, `sparsity.npy`, notebook checkpoints, virtual environments, or operating-system metadata.

## Validation and documentation

- Parse both notebooks as nbformat JSON and compare their cell types and sources.
- Confirm the canonical notebook contains one `SAE.from_pretrained_with_cfg_and_sparsity` call and no tuple-unpacked legacy loader call.
- Confirm the executed snapshot has all code cells executed and no error outputs.
- Run `uv lock --check`, synchronize the environment, and smoke-test every direct import plus loader availability.
- Scan staged content for secrets and local paths before pushing.
- Update `README.md`, `docs/provenance.md`, and the timestamped validation report whenever artifacts, dependencies, or claims change.
- Do not add a repository-wide license for the upstream notebook without verified licensing authority.
