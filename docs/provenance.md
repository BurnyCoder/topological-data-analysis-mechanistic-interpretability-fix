# Notebook provenance

## Upstream context

- Article: Gunnar Carlsson, [“Topological Data Analysis and Mechanistic Interpretability”](https://www.lesswrong.com/posts/6oF6pRr2FgjTmiHus/topological-data-analysis-and-mechanistic-interpretability), published 24 February 2025.
- Notebook: [the Google Colab notebook](https://colab.research.google.com/drive/1LAoHlqz0w6jy4kSq-puJ5VLqNM7lgptN) linked by Jakob Hansen in the article’s discussion.
- Upstream notebook retrieved on 3 August 2026 through Google Drive’s public download endpoint.

The upstream download had SHA-256 `42ec8d90b10c5ef9897e1325d6d656d2472d05ea500daeef213c96c3e38a3e62` and contained 26 cells.

## Modification boundary

The canonical notebook preserves the upstream cell order, cell types, and source text except for one compatibility change in cell 5:

```python
sae_lens.SAE.from_pretrained(
```

became:

```python
sae_lens.SAE.from_pretrained_with_cfg_and_sparsity(
```

SAE Lens v6 made `SAE.from_pretrained()` return only the SAE object. The replacement method retains the earlier `(sae, cfg_dict, sparsity)` result needed by later notebook cells. See the [SAE Lens v6 migration guide](https://github.com/decoderesearch/SAELens/blob/main/docs/migrating.md) and [loader implementation](https://github.com/decoderesearch/SAELens/blob/main/sae_lens/saes/sae.py).

The resulting canonical notebook has SHA-256 `020184b9ae0df2718bfd1d0439dfbf75731e2be6fa7846a41b984dfae09dc004`.

## Executed snapshot

The executed artifact is the user-provided successful Colab run, preserved byte-for-byte before reconstructing the canonical notebook. Its SHA-256 is `aa991982f03c51b76fd8bfe49e784de1b5a2729bdf685f2a1383178fa3aa3cd9` and its size is 103,731,130 bytes.

Its 26 cell types and sources match the canonical notebook. The size difference comes from saved outputs and approximately 98 MiB of Jupyter widget state for the interactive graphs.

## Attribution and licensing

This repository is a compatibility-preserving redistribution, not a claim of authorship over the original methodology or notebook. No explicit license was present in the downloaded upstream notebook, so this repository does not add a license covering that upstream material.

Third-party packages and downloaded data retain their own terms. In particular, Cobalt has separate registration and licensing requirements described in its [official documentation](https://docs.cobalt.bluelightai.com/setup.html), and SAE Lens is maintained in its [upstream repository](https://github.com/decoderesearch/SAELens).
