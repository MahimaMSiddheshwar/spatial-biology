METHODS
=======

Dataset
-------
Publicly available 10x Genomics Visium spatial transcriptomics data
from human breast cancer fresh-frozen tissue (v1.3.0) was obtained
from the 10x Genomics dataset portal. The dataset comprised
4,869 tissue-covered spots with a median of 9,720 UMIs
and 3,654 genes detected per spot.

Quality Control
---------------
Spots with fewer than 200 detected genes, fewer than 500 total UMI
counts, or greater than 15% mitochondrial gene expression were
excluded. Genes detected in fewer than 3 spots were removed,
yielding 4,869 spots and 21,349 genes.
Hemoglobin gene contamination was negligible (mean HB% = 0.01%).

Normalization and Preprocessing
--------------------------------
Raw counts were preserved in adata.layers['counts']. Library sizes
were normalized to 10,000 counts per spot followed by log1p
transformation. The top 3,000 highly variable genes were selected
using the Seurat v3 method. PCA was performed on scaled HVGs
(50 components) and a k-nearest neighbor graph (k=15, 30 PCs)
was constructed for UMAP embedding.

Clustering and Annotation
--------------------------
Leiden community detection (resolution=0.5) identified 11
transcriptionally distinct spatial clusters. Clusters were annotated
using marker genes from published breast cancer atlases and
CellMarker 2.0. Spatially variable genes were identified using
Moran's I (n_perms=100) with Benjamini-Hochberg FDR correction,
yielding 2,495 significant SVGs (FDR < 0.05).

Cell Type Signature Scoring
----------------------------
Cell type enrichment was estimated using sc.tl.score_genes
(Scanpy v1.12) with 24 curated signatures from
Wu et al. (2021) Nature Genetics breast cancer single-cell atlas.
Signatures covered tumor epithelial, stromal, immune, and special
functional categories including TLS, hypoxia, EMT, and T cell
exhaustion.

Cell-Cell Communication
-----------------------
Ligand-receptor co-expression scores were computed as the geometric
mean of ligand and receptor expression per spot. 39 LR pairs from
CellChatDB and NicheNet covering 8 signaling pathways were analyzed.
Statistical significance was assessed by permutation testing
(n=500) via Squidpy sq.gr.ligrec.

PAM50 and PROGENy Scoring
--------------------------
Molecular subtype scores were computed for all 5 PAM50 subtypes
and spatially mapped across tissue. PROGENy pathway activity was
scored using downstream target gene footprints for 10 cancer
signaling pathways.

Software
--------
Python 3, Scanpy v1.12, Squidpy v1.8.1,
NumPy, Pandas, Matplotlib, leidenalg.

References
----------
1. Wolf et al. (2018) Scanpy. Genome Biology.
2. Palla et al. (2022) Squidpy. Nature Methods.
3. Wu et al. (2021) Breast cancer scRNA atlas. Nature Genetics.
4. Traag et al. (2019) Leiden algorithm. Scientific Reports.
5. Jin et al. (2021) CellChat. Nature Communications.
6. Browaeys et al. (2020) NicheNet. Nature Methods.
7. Schubert et al. (2018) PROGENy. Nature Communications.
