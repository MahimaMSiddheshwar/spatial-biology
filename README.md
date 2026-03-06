# Spatial Transcriptomics of Human Breast Cancer
### 10x Genomics Visium · End-to-End Analysis Pipeline

![Python](https://img.shields.io/badge/Python-3.13-blue?style=flat-square&logo=python)
![Scanpy](https://img.shields.io/badge/Scanpy-1.x-green?style=flat-square)
![Squidpy](https://img.shields.io/badge/Squidpy-1.x-orange?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-darkgreen?style=flat-square)

---

## Overview

This project performs a comprehensive **spatial transcriptomics analysis** of human breast cancer tissue using the **10x Genomics Visium** platform. By combining whole-transcriptome gene expression with spatial coordinates, we map the cellular landscape of the **tumor microenvironment (TME)** directly onto tissue histology.

Unlike bulk RNA-seq, spatial transcriptomics reveals *where* genes are expressed — distinguishing tumor cores from invasive margins, stromal regions, and immune infiltrates — all within a single tissue section.

---

***Project***: *https://spatial-biology.vercel.app/*

---

## Dataset

| Field | Details |
|-------|---------|
| **Dataset** | Visium Human Breast Cancer (Fresh Frozen) |
| **Source** | [10x Genomics Public Datasets](https://www.10xgenomics.com/datasets/human-breast-cancer-visium-fresh-frozen-whole-transcriptome-1-standard) |
| **Version** | 1.3.0 |
| **Species** | *Homo sapiens* |
| **Tissue** | Breast Cancer (Invasive Ductal Carcinoma) |
| **Spots on Tissue** | 4,869 |
| **Genes Detected** | 21,349 (after QC) |
| **Median UMI / Spot** | 9,720 |
| **Median Genes / Spot** | 3,654 |
| **Spot Diameter** | 55 µm (~8 cells per spot) |

---

## Key Findings

- **9 transcriptionally distinct spatial domains** identified at Leiden resolution 0.5
- **ER+ Luminal subtype** confirmed — strong ESR1 expression in tumor zones, minimal KRT5 (basal marker)
- **Invasive Carcinoma Core** (Cluster 3) clearly delineated — EPCAM+, ESR1+, ERBB2+, MKI67+
- **Dense fibrous stroma** (COL1A1+, VIM+) surrounds tumor nests — classic invasive ductal carcinoma pattern
- **Macrophage infiltration** (CD68+) detected in spatially restricted hotspots
- **Near-zero hemoglobin contamination** (HB% = 0.01%) — exceptionally clean tissue preparation
- **Spatially variable genes** identified via Moran's I autocorrelation analysis

---

## Analysis Pipeline

```
01_Data_Setup.ipynb              → Download, extract, load AnnData
02_QC_Preprocessing.ipynb        → Filter, normalize, HVG, PCA, UMAP
03_Clustering.ipynb              → Leiden clustering, marker genes, annotation
04_Spatial_Analysis.ipynb        → Moran's I SVGs, neighborhood enrichment, co-occurrence
05_Gene Signature Scoring.ipynb  → Gene Signature scoring to estimate the cell type  
06_CellComm.ipynb                → LIANA+ ligand-receptor interactions     
07_Advanced.ipynb                → PAM50 scoring, PROGENy pathways
   
```

---

## 📁 Project Structure

```
spatial-transcriptomics-breast-cancer/
│
├── data/
│   ├── raw/
│   │   └── Visium_Human_Breast_Cancer/
│   │       ├── filtered_feature_bc_matrix.h5
│   │       ├── spatial/
│   │       │   ├── tissue_positions_list.csv
│   │       │   ├── scalefactors_json.json
│   │       │   ├── tissue_hires_image.png
│   │       │   └── tissue_lowres_image.png
│   │       └── molecule_info.h5
│   │
│   └── processed/
│       ├── adata_preprocessed.h5ad    ← Post QC + normalization
│       ├── adata_clustered.h5ad       ← Post Leiden clustering
│       ├── adata_annotated.h5ad       ← Post cell type annotation
│       └── adata_spatial.h5ad         ← Post spatial statistics
│
├── figures/
│   ├── qc/                            ← Violin plots, HVG, PCA elbow
│   ├── spatial/                       ← Tissue overlay plots
│   ├── clustering/                    ← UMAP, cluster plots
│   ├── svg/                           ← Spatially variable genes
│   └── neighborhood/                  ← Enrichment, co-occurrence
│
├── notebooks/
│   ├── 01_Data_Setup.ipynb
│   ├── 02_QC_Preprocessing.ipynb
│   ├── 03_Clustering.ipynb
│   ├── 04_Spatial_Analysis.ipynb
│   ├── 05_Gene Signature Scoring.ipynb         
│   ├── 06_CellComm.ipynb             
│   └── 07_Advanced.ipynb             
│
├── README.md
└── requirements.txt
```

---

## ⚙️ Installation & Setup

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/spatial-transcriptomics-breast-cancer.git
cd spatial-transcriptomics-breast-cancer
```

### 2. Create environment
```bash
# Using conda (recommended)
conda create -n st_env python=3.13
conda activate st_env

# Install required packages
pip install scanpy squidpy cell2location liana
pip install jupyter notebook ipykernel
```

### 3. Download the data
Run the first notebook — it downloads everything automatically:
```bash
jupyter notebook notebooks/01_Data_Setup.ipynb
```

Or download manually:
```bash
BASE="https://cf.10xgenomics.com/samples/spatial-exp/1.3.0/Visium_Human_Breast_Cancer"
wget $BASE/Visium_Human_Breast_Cancer_filtered_feature_bc_matrix.h5
wget $BASE/Visium_Human_Breast_Cancer_spatial.tar.gz
wget $BASE/Visium_Human_Breast_Cancer_molecule_info.h5
tar -xzf Visium_Human_Breast_Cancer_spatial.tar.gz
```

### 4. Run notebooks in order
```bash
jupyter notebook
# Run notebooks 01 → 02 → 03 → 04 in sequence
```

---

## Requirements

```
scanpy >= 1.9
squidpy >= 1.4
anndata >= 0.9
numpy
pandas
matplotlib
seaborn
leidenalg
cell2location        # Notebook 05
liana-py             # Notebook 06
decoupler-py         # Notebook 07
```

Generate `requirements.txt`:
```bash
pip freeze > requirements.txt
```

---

## QC Summary

| Metric | Before QC | After QC |
|--------|----------|---------|
| Spots | 4,898 | 4,869 |
| Genes | 36,601 | 21,349 |
| Median UMI/spot | 9,720 | 9,720 |
| MT% (mean) | 3.71% | < 15% |
| HB% (mean) | 0.01% | 0.01% |
| Spots removed | — | 29 |
| Genes removed | — | 15,252 |

**Filtering thresholds applied:**
- Minimum genes per spot: 200
- Minimum counts per spot: 500
- Maximum MT%: 15%
- Minimum cells per gene: 3

---

## Cluster Annotations

| Cluster | Cell Type | Key Markers | Spots |
|---------|-----------|------------|-------|
| 0 | Fibrous Stroma | COL1A1, COL3A1 | — |
| 1 | Mixed Tumor-Stroma | EPCAM, VIM | — |
| 2 | Stromal / CAF-rich | VIM, FAP | — |
| 3 | Invasive Carcinoma Core | EPCAM, ESR1, ERBB2, MKI67 | — |
| 4 | Reactive Stroma | VIM, ACTA2 | — |
| 5 | Macrophage-rich | CD68, CD163 | — |
| 6 | Tumor-Stroma Interface | EPCAM, COL1A1 | — |
| 7 | Adipose / Normal Epithelium | ADIPOQ, KRT18 | — |
| 8 | Endothelial / Vascular | PECAM1, VWF | — |

> Fill in the Spots column after running `adata.obs['leiden'].value_counts()`

---

## Methods Summary

### Quality Control
Spots with fewer than 200 genes, fewer than 500 UMIs, or more than 15% mitochondrial reads were excluded. Genes detected in fewer than 3 spots were removed.

### Normalization
Total counts per spot were normalized to 10,000 (CPM-like) followed by log1p transformation. Raw counts were preserved in `adata.layers['counts']` and `adata.raw` for downstream analyses.

### Dimensionality Reduction
Top 3,000 highly variable genes were selected using the Seurat v3 method. PCA was computed on scaled HVGs (50 components). A k-nearest neighbor graph (k=15, 30 PCs) was used for UMAP embedding.

### Clustering
Leiden community detection was applied at resolutions 0.3, 0.5, 0.6, and 1.0. Resolution 0.5 (9 clusters) was selected based on spatial coherence and biological interpretability.

### Spatial Statistics
A Visium hexagonal spatial neighbors graph (6 neighbors) was constructed. Moran's I was computed for all genes to identify spatially variable genes (SVGs). Neighborhood enrichment, co-occurrence, and Ripley's L statistics were computed using Squidpy.

---

## References

1. **Scanpy** — Wolf et al. (2018) *Genome Biology* [doi:10.1186/s13059-017-1382-0](https://doi.org/10.1186/s13059-017-1382-0)
2. **Squidpy** — Palla et al. (2022) *Nature Methods* [doi:10.1038/s41592-021-01358-2](https://doi.org/10.1038/s41592-021-01358-2)
3. **10x Genomics Visium** — [Spatial Gene Expression](https://www.10xgenomics.com/spatial-transcriptomics/)
4. **Cell2Location** — Kleshchevnikov et al. (2022) *Nature Biotechnology*
5. **Leiden Algorithm** — Traag et al. (2019) *Scientific Reports*
6. **Breast Cancer scRNA-seq Reference** — Wu et al. (2021) *Nature Genetics*

---

## Author

**Mahima M Siddheshwar**
- Data Scientist | Computational Biologist | Bioinformatician

---

## License

This project is licensed under the MIT License.
The dataset is publicly available from 10x Genomics and is subject to their
[terms of use](https://www.10xgenomics.com/legal/end-user-software-license-agreement).

---

## Acknowledgements

- 10x Genomics for making the Visium breast cancer dataset publicly available
- The Scanpy and Squidpy development teams
- The broader open-source single-cell and spatial transcriptomics community

---
