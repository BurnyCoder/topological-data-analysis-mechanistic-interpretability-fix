# Topological Data Analysis and Mechanistic Interpretability — SAE Lens v6 fix

This repository preserves a compatibility-fixed version of the Colab notebook associated with the LessWrong post [“Topological Data Analysis and Mechanistic Interpretability”](https://www.lesswrong.com/posts/6oF6pRr2FgjTmiHus/topological-data-analysis-and-mechanistic-interpretability). It keeps the original notebook methodology while updating the SAE loading call for SAE Lens v6.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BurnyCoder/topological-data-analysis-mechanistic-interpretability-fix/blob/main/Exploring_SAE_features_with_TDA_graphs.ipynb)

## Methodology and data flow

The notebook loads a 32k-feature TopK sparse autoencoder trained on the layer-8 residual stream of GPT-2 Small. It uses the SAE decoder vectors as feature representations, builds multiresolution Mapper graphs with cosine distance, and combines those graphs with Neuronpedia feature descriptions in an interactive explorer. It also demonstrates coloring by feature sparsity and rebuilding the graph for the most frequently active 10% of features.

```mermaid
flowchart LR
    A["SAE Lens: load GPT-2 Small layer-8 SAE"] --> B["Decoder vectors: W_dec"]
    C["Neuronpedia feature descriptions"] --> D["Feature metadata"]
    B --> E["Cobalt Mapper graph with cosine distance"]
    E --> F["Multiresolution TDA graph"]
    D --> G["Interactive FeatureExplorer"]
    F --> G
    G --> H["Selection, search, coloring, and subset exploration"]
    B --> I["feature_vecs.npy"]
    A --> J["sparsity.npy"]
```

## Compatibility change

SAE Lens v6 changed `SAE.from_pretrained()` to return only the SAE object. The notebook needs the configuration and sparsity tensor as well, so its single source-code change is:

```python
# Original SAE Lens <=5 call
sae, cfg_dict, sparsity = sae_lens.SAE.from_pretrained(...)

# SAE Lens v6-compatible call
sae, cfg_dict, sparsity = sae_lens.SAE.from_pretrained_with_cfg_and_sparsity(...)
```

The new tuple-returning loader is defined in the [SAE Lens source](https://github.com/decoderesearch/SAELens/blob/main/sae_lens/saes/sae.py). All other notebook source cells match the upstream Colab notebook.

## Run the notebook

### Google Colab

Use the badge above, then run the cells in order. The notebook installs its packages, downloads the SAE and public Neuronpedia descriptions, constructs the graphs, and displays the interactive explorer. Internet access and enough memory for the 32k-feature graph are required.

### Local JupyterLab with uv

Python 3.12 and the working package versions are locked in `uv.lock`.

```bash
git clone https://github.com/BurnyCoder/topological-data-analysis-mechanistic-interpretability-fix.git
cd topological-data-analysis-mechanistic-interpretability-fix
uv sync
uv run jupyter lab
```

Cobalt requires separate registration under its own license. Follow the [official Cobalt setup instructions](https://docs.cobalt.bluelightai.com/setup.html). You can register interactively, or copy `.env.example` to `.env`, add `COBALT_LICENSE_KEY`, and launch with:

```bash
uv run --env-file .env jupyter lab
```

Never commit `.env`; it is ignored by Git.

## Repository artifacts

| Artifact | Purpose |
| --- | --- |
| `Exploring_SAE_features_with_TDA_graphs.ipynb` | Canonical small notebook for reading, diffing, and running. |
| `artifacts/Exploring_SAE_features_with_TDA_graphs.executed.ipynb` | Preserved 98.9 MiB successful Colab execution, including widget state and outputs. |

The executed snapshot is stored with Git LFS. Run `git lfs pull` after cloning if it was not downloaded automatically. GitHub may not preview such a large notebook; use the canonical notebook for browsing.

Running the notebook writes `explanations.json`, `feature_vecs.npy`, and `sparsity.npy` in the working directory. These reproducible outputs are intentionally ignored rather than committed.

## Validation and provenance

The preserved execution completed all 19 code cells without an error output using Python 3.12.8, SAE Lens 6.47.0, Cobalt 0.4.2, and TransformerLens 3.6.0. Structural, dependency, size, LFS, and credential checks are recorded in the [validation report](reports/2026-08-03-validation.md).

The notebook was linked by Jakob Hansen in the discussion below Gunnar Carlsson’s LessWrong post. Exact upstream links, hashes, attribution details, and the modification boundary are documented in [provenance](docs/provenance.md).

No repository-level license is asserted over the upstream notebook because the upstream artifact does not state one. Dependencies, including Cobalt, remain subject to their own licenses.
