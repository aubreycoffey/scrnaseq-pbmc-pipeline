# scrnaseq-pbmc-pipeline

A reproducible computational pipeline for single-cell RNA sequencing (scRNA-seq) analysis, with T-cell receptor (TCR) integration. Applied to a 10x Genomics PBMC dataset and built to run end-to-end in Google Colab.

---

## Overview

This project demonstrates a full single-cell transcriptomics workflow — from raw count data through to multimodal TCR integration — using industry-standard Python tools. It is structured as a series of modular, sequential notebooks, each building on the last.

| Step | Notebook | Description |
|------|----------|-------------|
| 1 | `01_preprocessing` | QC filtering, normalization, highly variable gene selection |
| 2 | `02_clustering_annotation` | PCA, UMAP, Leiden clustering, cell type annotation |
| 3 | `03_differential_expression` | Marker gene identification per cluster (Wilcoxon rank-sum) |
| 4 | `04_trajectory_inference` | PAGA graph abstraction, diffusion pseudotime |
| 5 | `05_tcr_integration` | TCR loading, clonotype assignment, multimodal visualization |

---

## Quick start (Google Colab)

Each notebook has an **Open in Colab** badge at the top. Click to launch — no local setup required. Notebooks mount Google Drive to persist intermediate `.h5ad` files across sessions, so run them in order (01 → 05).

| Notebook | Colab |
|----------|-------|
| 01 · Preprocessing | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/aubreycoffey/single-cell-analysis-pipeline/blob/main/notebooks/01_preprocessing.ipynb) |
| 02 · Clustering & Annotation | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/aubreycoffey/single-cell-analysis-pipeline/blob/main/notebooks/02_clustering_annotation.ipynb) |
| 03 · Differential Expression | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/aubreycoffey/single-cell-analysis-pipeline/blob/main/notebooks/03_differential_expression.ipynb) |
| 04 · Trajectory Inference | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/aubreycoffey/single-cell-analysis-pipeline/blob/main/notebooks/04_trajectory_inference.ipynb) |
| 05 · TCR Integration | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/aubreycoffey/single-cell-analysis-pipeline/blob/main/notebooks/05_tcr_integration.ipynb) |

---

## Dataset

**10x Genomics PBMC 3k** — 2,700 peripheral blood mononuclear cells (PBMCs) sequenced on the Chromium platform. Loaded directly via `scanpy.datasets.pbmc3k()`.

For the TCR integration notebook, paired TCR sequence data is sourced from Wu et al. 2020, available via `scirpy.datasets.wu2020_3k()`.

---

## Methods

**Preprocessing**
Raw UMI counts are filtered by number of detected genes (200–2500), total counts, and mitochondrial gene fraction (<5%). Counts are library-size normalized to 10,000 counts per cell and log1p transformed. The top 2,000 highly variable genes are selected for downstream analysis.

**Clustering and annotation**
PCA is applied to the HVG matrix, followed by k-nearest neighbor graph construction and UMAP for 2D visualization. Leiden clustering partitions cells into communities, which are then annotated using canonical marker genes (e.g. CD14/LYZ for monocytes, MS4A1 for B cells, CD8A for cytotoxic T cells).

**Differential expression**
Marker genes for each cluster are identified using the Wilcoxon rank-sum test via `sc.tl.rank_genes_groups`, run on raw log-normalized counts. Results are visualized as dot plots and heatmaps.

**Trajectory inference**
PAGA (partition-based graph abstraction) computes a connectivity graph between cell populations. Diffusion pseudotime (DPT) is initialized from a naive CD4+ T cell root to model T cell state transitions along a naive → memory → effector axis.

**TCR integration**
TCR alpha and beta chain sequences are loaded and QC'd with Scirpy. Clonotypes are defined by identical CDR3 amino acid sequences on both chains. Clonal expansion is quantified per cell and overlaid on the UMAP embedding, enabling joint interpretation of transcriptomic state and receptor identity.

---

## Local setup

**With conda (recommended):**
```bash
conda env create -f environment.yml
conda activate scrnaseq-pipeline
jupyter notebook
```

**With pip:**
```bash
pip install -r requirements.txt
jupyter notebook
```

---

## Project structure

```
single-cell-analysis-pipeline/
├── README.md
├── requirements.txt
├── environment.yml
└── notebooks/
    ├── 01_preprocessing.ipynb
    ├── 02_clustering_annotation.ipynb
    ├── 03_differential_expression.ipynb
    ├── 04_trajectory_inference.ipynb
    └── 05_tcr_integration.ipynb
```

---

## References

- Wolf et al. (2018) *SCANPY: large-scale single-cell gene expression data analysis.* Genome Biology. https://doi.org/10.1186/s13059-017-1382-0
- Sturm et al. (2020) *Scirpy: a Scanpy extension for analysing single-cell T-cell receptor sequencing data.* Bioinformatics. https://doi.org/10.1093/bioinformatics/btaa611
- Traag et al. (2019) *From Louvain to Leiden: guaranteeing well-connected communities.* Scientific Reports. https://doi.org/10.1038/s41598-019-41695-z
- Wolf et al. (2019) *PAGA: graph abstraction reconciles clustering with trajectory inference.* Genome Biology. https://doi.org/10.1186/s13059-019-1663-x

---

## Author

Aubrey Coffey · [github.com/aubreycoffey](https://github.com/aubreycoffey)
