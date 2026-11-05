# Validation report — 3 August 2026

Generated at `2026-08-03T21:54:12+02:00` in Europe/Berlin.

## Question and hypothesis

Question: can the upstream TDA notebook be made compatible with SAE Lens v6 through only the loader-name change while preserving a verifiable successful execution?

Hypothesis: replacing `SAE.from_pretrained` with `SAE.from_pretrained_with_cfg_and_sparsity` will return the `(sae, cfg_dict, sparsity)` tuple, whose `sae` and `sparsity` values are used by later cells, and no other source edit is required.

## Evidence and method

The public upstream Colab notebook was downloaded independently and parsed alongside the canonical and executed artifacts. Validation compared cell counts, cell types, and normalized cell source strings rather than relying on the notebooks’ serialized formatting or output metadata. Both repository notebooks were also validated with `nbformat`.

The executed artifact was inspected as evidence of the complete pipeline run. A fresh local `uv` environment was then synchronized from the lockfile for dependency resolution, direct-import checks, and loader signature inspection. The 32k-feature graph was not recomputed because the byte-preserved Colab artifact already records a complete run with the intended dependency versions.

## Results

| Check | Result |
| --- | --- |
| Upstream, canonical, and executed notebooks parse as nbformat v4 | Pass |
| All artifacts contain 26 cells | Pass |
| Canonical and executed cell types and sources are identical | Pass |
| Upstream source differs only in cell 5’s (zero-based index) loader name | Pass |
| Canonical notebook contains the v6 loader exactly once and no tuple-unpacked legacy loader call | Pass |
| Executed snapshot has execution counts for all 19 code cells | Pass |
| Executed snapshot contains zero Jupyter error outputs | Pass |
| Output-cleared canonical notebook converts to HTML with current nbconvert | Pass |
| Locked environment resolves, synchronizes, and imports every notebook dependency | Pass |
| SAE loader exposes `release` and `sae_id` and returns the documented tuple type | Pass |
| Notebook artifacts contain no recognized API-token, bearer-token, private-key, or local-path patterns | Pass |
| Executed snapshot is selected by the Git LFS filter | Pass |

The saved execution’s `pip` output records Python 3.12 (`/usr/local/lib/python3.12/dist-packages`), `cobalt-ai==0.4.2`, `sae-lens==6.47.0`, and `transformer-lens==3.6.0`. Its `language_info` metadata says 3.12.8, but that field is identical to the upstream notebook’s, whose outputs were produced under Python 3.11, so it does not establish the patch version. The run’s execution counts are 13 to 30 in cell order and every package was already installed, so the snapshot comes from a Colab session that had executed earlier cells, not from a fresh kernel. The lockfile matches the run only for the direct dependencies: 53 of the 176 packages listed in both the run’s `pip` output and `uv.lock` have different versions, including `torch` (2.11.0+cpu in the run, 2.13.0 in the lock). The fresh import smoke test used Linux/WSL2, Python 3.12.3, uv 0.11.27, and the same three pinned package versions.

The relevant loader signature shape observed in the fresh environment was:

```text
(release: str, sae_id: str, device: str = 'cpu', dtype: str = 'float32', force_download: bool = False, converter: PretrainedSaeHuggingfaceLoader | None = None) -> tuple[T_SAE, dict[str, Any], torch.Tensor | None]
```

## Artifact integrity

| Artifact | Bytes | SHA-256 |
| --- | ---: | --- |
| Upstream download | 76,756 | `42ec8d90b10c5ef9897e1325d6d656d2472d05ea500daeef213c96c3e38a3e62` |
| Canonical fixed notebook | 22,583 | `affc3ac707d136c3221f53348e4131ef2c59513272415ed3d3813f97ab49a175` |
| Executed snapshot | 103,731,130 | `aa991982f03c51b76fd8bfe49e784de1b5a2729bdf685f2a1383178fa3aa3cd9` |

The executed snapshot is 1,126,470 bytes below GitHub’s 100 MiB regular-file ceiling, but it is stored through Git LFS to avoid placing a roughly 99 MiB blob in normal Git history.

## Experiment notes and learnings

- The user-provided notebook’s source already differed from upstream in exactly the intended loader name. Its large size came from approximately 98 MiB of saved Jupyter widget state, not additional source changes.
- A first HTML-render check exposed nonstandard widget metadata in the upstream Colab download: current nbconvert raised `KeyError: 'state'`. Clearing canonical outputs, execution counts, and widget state fixed rendering while preserving every cell type and source string. The executed artifact remains byte-identical to the user-provided run.
- The first smoke-test version report attempted to read `transformer_lens.__version__`. TransformerLens does not expose that module attribute, so the check was corrected to use the standard `importlib.metadata.version()` API. Imports and loader inspection were unaffected.
- Importing Cobalt without a registered local license succeeds but prints its registration reminder. The repository documents registration and provides an optional empty `.env.example`; no credential was present or added.

## Conclusion

The evidence supports the hypothesis: the SAE Lens v6 loader-name update is the only required source modification, both distributed notebook variants preserve the same code, and the existing full execution completed without notebook errors under the pinned core versions.

## Corrections

On 5 November 2026 a fact-check corrected the Python patch-version claim, clarified the zero-based cell index and which tuple values later cells use, and added the execution-count and lockfile-drift notes above. Evidence: the `pip` output and metadata of the executed snapshot, the independently downloaded upstream notebook, and `uv.lock`.
