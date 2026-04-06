# Spatial Transcriptomics of Human Breast Cancer
### 10x Genomics Visium · End-to-End Analysis Pipeline

![Python](https://img.shields.io/badge/Python-3.13-blue?style=flat-square&logo=python)
![Scanpy](https://img.shields.io/badge/Scanpy-1.x-green?style=flat-square)
![Squidpy](https://img.shields.io/badge/Squidpy-1.x-orange?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-darkgreen?style=flat-square)

---

## Overview

This project performs a comprehensive **spatial transcriptomics analysis** of human breast cancer tissue using the **10x Genomics Visium** platform. By combining whole-transcriptome gene expression with spatial coordinates, I mapped the cellular landscape of the **tumor microenvironment (TME)** directly onto tissue histology — revealing not just which cells are present, but *where* they are and how they communicate.

Unlike bulk RNA-seq, spatial transcriptomics reveals *where* genes are expressed — distinguishing tumor cores from invasive margins, stromal barriers, and immune infiltrates — all within a single tissue section.

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

## Key Biological Discoveries

### THE DISCOVERY: "Mosaic of Resistance" — An Immune-Excluded Tumor

This analysis reveals that the tumor microenvironment is not simply "cold" (immune-depleted) but rather **actively fortified** against immune attack through a sophisticated **Dual Barrier System**.

#### 1. The Dual Barrier System

| Barrier Type | Components | Mechanism |
|--------------|------------|-----------|
| **Physical Wall** | MGP+, COL1A1+, FN1 | TGF-β driven fibrosis creates dense stromal encapsulation |
| **Chemical Shield** | LAG3, PD-1, PD-L1 | Checkpoint exhaustion paralyzes T-cells at the border |

#### 2. Key Evidence

- **MGP** (top SVG, Moran's I = 0.876) forms continuous stromal rings encapsulating tumor nests
- **IGLC2+ TLS** (Tertiary Lymphoid Structures) organizes active immune hotspots **exclusively outside** the barrier
- All cytotoxic lineages (CD8+, CD4+, NK cells) are recruited but trapped at the **Macrophage/CD74+ interface**
- High LAG3→MHC-II scores at the boundary confirm active T-cell exhaustion
- High TGF-β→TGFBR scores in stroma confirm fibrotic barrier construction

#### 3. Intratumor Heterogeneity

The ER+ Invasive Carcinoma is not uniform — high variance in IGKC (7.49) vs MGP (7.19) proves patchy defense:
- **"Hard" regions**: Fibrotic zones protected by MGP-driven stiffness
- **"Cold" regions**: Immune-exhausted zones maintained by JAK-STAT/Macrophage signaling

---

## Molecular Subtyping

### PAM50 Classification

| Subtype | Spots | Distribution |
|---------|-------|--------------|
| **Luminal A** | 2,815 | Dominant — ER+/PR+ with low proliferation |
| Normal-like | 672 | Stromal regions |
| Basal-like | 493 | Minor population |
| Luminal B | 468 | Higher proliferation |
| HER2-enriched | 421 | HER2+ signaling |

**Conclusion**: This is a **Luminal A (ER+) breast carcinoma** — explaining its typical resistance to immunotherapy and sensitivity to hormone therapy.

---

## Analysis Pipeline

```
01_Data_Setup.ipynb              → Download, extract, load Visium data
02_QC_Preprocessing.ipynb        → Filter, normalize, HVG selection, PCA, UMAP
03_Clustering.ipynb              → Leiden clustering, marker genes, cell type annotation
04_Spatial_Analysis.ipynb        → Moran's I SVGs, neighborhood enrichment, Ripley's L
05_Gene_Signature_Scoring.ipynb  → 24 gene signatures (Tumor/Stroma/Immune/Special)
06_Cell_Communication.ipynb      → 39 ligand-receptor pairs, 8 signaling pathways
07_Advanced.ipynb                → PAM50 subtyping, PROGENy pathways, heterogeneity analysis
```

---

## Cluster Annotations

| Cluster | Cell Type | Key Markers | Spots |
|---------|-----------|------------|-------|
| 0 | Plasma Cells / TLS | IGLC2, IGHG3, IGKC | 910 |
| 1 | Proliferating Luminal Tumour | CCND1, MUC1, KRT8 | 899 |
| 2 | CXCL14+ Tumour-Stroma | CXCL14, KRT8 | 590 |
| 3 | Luminal Epithelium | SCGB2A2, SCGB1D2 | 500 |
| 4 | Stromal Barrier | MGP, ACTG1 | 445 |
| 5 | Interferon-activated Immune | IFI27, MCCD1 | 445 |
| 6 | ER+ Invasive Carcinoma | GATA3, ESR1 | 401 |
| 7 | Macrophages / APC | CD74, HLA-DRA | 372 |
| 8 | Metabolically Active Stroma | MT-CYB, MT-CO1 | 157 |
| 9 | Secretory Epithelium | CRISP3, SLITRK6 | 77 |
| 10 | Adipose Tissue | FABP4, ADH1B | 73 |

---

## Gene Signature Analysis

### 24 Signatures Scored Across 4 Compartments

| Compartment | Signatures |
|------------|------------|
| **Tumor** | Tumor_Epithelial, Luminal_A, HER2_Enriched, Basal_Like, Proliferating |
| **Stroma** | CAFs, Myofibroblasts, Endothelial, Adipocytes |
| **Immune** | T_Cells, CD8_T_Cells, CD4_T_Cells, Tregs, B_Cells, Plasma_Cells, Macrophages_M1/M2, NK_Cells, Mast_Cells, Dendritic_Cells |
| **Special** | TLS_signature, Hypoxia, EMT, Exhausted_T |

### Top Spatially Variable Genes (Moran's I)

| Gene | Moran's I | Biological Role |
|------|-----------|----------------|
| MGP | 0.876 | Stromal barrier protein |
| IGLC2 | 0.815 | Antibody production (TLS) |
| COX6C | 0.803 | Mitochondrial function |
| IGHG3 | 0.793 | Immune activation |
| CXCL14 | 0.786 | Chemokine recruitment |

---

## Cell-Cell Communication Analysis

### Key Ligand-Receptor Interactions

| Rank | LR Pair | Biological Role | Pathway |
|------|---------|----------------|---------|
| 1 | FN1 → ITGB1 | Physical anchor | ECM |
| 2 | HLA-DRA → CD4 | Antigen presentation | Immune |
| 3 | FN1 → ITGA5 | Stromal stiffness | ECM |
| 4 | POSTN → ITGAV | Tissue remodeling | ECM |
| 5 | LAG3 → MHC-II | T-cell exhaustion | Checkpoint |
| 6 | HLA-A → CD8A | Cytotoxic signaling | Immune |
| 7 | CXCL12 → CXCR4 | Immune recruitment | Chemokine |
| 8 | TGFβ1 → TGFBR2 | Fibrosis | TGF-β |

### Pathway Activity at Tumor Border

| Pathway | Most Active Cluster | Score |
|---------|---------------------|-------|
| Immune Checkpoint | Macrophages / APC | 1.00 |
| TGF-β / Stroma | Macrophages / APC | 1.00 |
| Angiogenesis | CXCL14+ Tumour-Stroma | 1.00 |
| Immune Recruitment | Macrophages / APC | 1.00 |
| ECM / Adhesion | CXCL14+ Tumour-Stroma | 1.00 |

---

## Clinical Implications

### Why Standard Therapy Fails

This tumor is **NOT simply "cold"** — it actively recruits immune cells only to trap them at the border. Standard anti-PD-1 monotherapy fails because:

1. **Physical Wall** (TGF-β fibrosis) blocks immune cell migration into tumor nests
2. **Chemical Shield** (LAG3 exhaustion) deactivates T-cells precisely at the barrier
3. **Hypoxic Core** (VEGF signaling) maintains pro-survival environment

### Recommended Combination Approach

Based on spatial evidence, a triple-combination strategy may be required:
1. **Anti-PD-1/PD-L1** — Break checkpoint exhaustion
2. **TGF-β inhibitor** — Dissolve fibrotic barrier
3. **VEGFR blockade** — Counter hypoxia and angiogenesis

---

## Methods Summary

### Quality Control
- Minimum genes per spot: 200
- Minimum counts per spot: 500
- Maximum mitochondrial %: 15%
- Minimum cells per gene: 3

### Normalization
- Total counts normalized to 10,000 per spot (CPM-like)
- Log1p transformation applied
- Raw counts preserved in `adata.layers['counts']`

### Dimensionality Reduction
- 3,000 highly variable genes (Seurat v3 method)
- PCA: 50 components
- k-NN graph: k=15 neighbors, 30 PCs

### Clustering
- Leiden community detection at resolutions 0.3, 0.5, 0.6, 1.0
- Resolution 0.5 selected for final analysis (11 clusters)

### Spatial Statistics
- Moran's I spatial autocorrelation
- Neighborhood enrichment (permutation-based)
- Ripley's L spatial clustering
- Ligand-receptor interaction analysis (500 permutations)

### Gene Set Enrichment
- PAM50 intrinsic subtyping signatures
- PROGENy pathway activity (10 fundamental circuits)
- Custom TME signatures (Wu et al. 2021)

---

## Project Structure

```
spatial_biology_project/
│
├── data/
│   ├── raw/
│   │   └── Visium_Human_Breast_Cancer/
│   │       ├── filtered_feature_bc_matrix.h5
│   │       └── spatial/
│   └── processed/
│       ├── adata_preprocessed.h5ad    ← Post QC + normalization
│       ├── adata_annotated.h5ad        ← Post cell type annotation
│       ├── adata_spatial.h5ad          ← Post spatial statistics
│       ├── adata_scored.h5ad          ← Post gene signature scoring
│       └── adata_cellcomm.h5ad         ← Post cell communication
│
├── figures/
│   ├── qc/                    ← Violin plots, HVG, PCA elbow
│   ├── spatial/               ← Tissue overlay plots
│   ├── clustering/            ← UMAP, cluster plots
│   ├── svg/                  ← Spatially variable genes
│   ├── neighborhood/          ← Enrichment, Ripley's L
│   ├── gene_signature/       ← Signature scoring plots
│   ├── cellcomm/             ← Ligand-receptor analysis
│   ├── advanced/             ← PAM50, PROGENy
│   └── report/               ← Summary figures
│
├── scripts/
│   ├── 01_Data_Setup.ipynb
│   ├── 02_QC_Preprocessing.ipynb
│   ├── 03_Clustering.ipynb
│   ├── 04_Spatial_Analysis.ipynb
│   ├── 05_Gene_Signature_Scoring.ipynb
│   ├── 06_Cell_Communication.ipynb
│   └── 07_Advanced.ipynb
│
├── document/
│
├── Presentation/
│
├── README.md
└── requirements.txt
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

---

## References

1. **Scanpy** — Wolf et al. (2018) *Genome Biology* [doi:10.1186/s13059-017-1382-0](https://doi.org/10.1186/s13059-017-1382-0)
2. **Squidpy** — Palla et al. (2022) *Nature Methods* [doi:10.1038/s41592-021-01358-2](https://doi.org/10.1038/s41592-021-01358-2)
3. **10x Genomics Visium** — [Spatial Gene Expression](https://www.10xgenomics.com/spatial-transcriptomics/)
4. **Leiden Algorithm** — Traag et al. (2019) *Scientific Reports*
5. **Breast Cancer scRNA-seq Reference** — Wu et al. (2021) *Nature Genetics*
6. **PROGENy** — Schubert et al. (2018) *Nature Communications*
7. **PAM50** — Parker et al. (2009) *J Clinical Oncology*

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
