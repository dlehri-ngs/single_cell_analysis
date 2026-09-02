# Single-Cell RNA-seq Analysis: PBMC and Pediatric B-ALL

Two single-cell RNA-seq analysis pipelines, combining traditional bioinformatics methods with deep learning (scVI) for normalization and batch correction.

## Contents

- **`pbmc_analysis.ipynb`** — end-to-end pipeline on the public 10x Genomics PBMC3k dataset (2,700 healthy peripheral blood cells): QC → deep learning normalization (scVI) → clustering → UMAP → cell type annotation → differential expression / volcano plot.
- **`leukemia_analysis.ipynb`** — pediatric B-cell acute lymphoblastic leukemia (ETV6-RUNX1 subtype) vs. healthy pediatric bone marrow, using public data from [GSE132509](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE132509) (Caron et al., *Scientific Reports* 2020, [doi:10.1038/s41598-020-64929-x](https://doi.org/10.1038/s41598-020-64929-x)). Includes a single-patient pilot, a multi-patient replicated analysis (4 leukemia patients vs. 3 healthy donors), and CNV-based molecular confirmation of malignant cells (`infercnvpy`).

## Pipeline overview

1. Quality control (gene count, total counts, mitochondrial %)
2. Feature selection (highly variable genes)
3. Deep learning normalization/batch correction (scVI variational autoencoder)
4. Clustering (Leiden) and UMAP visualization
5. Marker-gene-based cell type annotation
6. Differential expression (Wilcoxon rank-sum test) and volcano plots
7. *(Leukemia only)* CNV inference for independent malignancy confirmation

## Setup

```bash
mamba env create -f environment.yml
conda activate sc-env
jupyter lab
```

Data is not included in this repository (see `.gitignore`) due to file size. Each notebook's first section documents how to download the required public data.

## Key findings (leukemia analysis)

- Leukemic blasts (B-cell precursor lineage) show strong, replicated upregulation of MHC class II genes (`HLA-DRA`, `CD74`, etc.) and `DNTT`, consistent with published B-ALL immunophenotyping.
- Findings that appeared strong in a single-patient pilot (`MEF2C`, `DNTT` as top hits) did not fully replicate once pooled across 4 patients — a useful illustration of why single-patient conclusions in cancer genomics should be treated cautiously.
- CNV inference independently confirmed marker-gene-based blast classification (~50% higher mean CNV score in blasts vs. normal cells), with a modest rather than dramatic signal — consistent with ETV6-RUNX1 being a translocation-driven rather than aneuploidy-driven leukemia subtype.

## Notes

This project was built as a learning exercise in single-cell analysis, combining traditional and deep-learning-based methods. See inline markdown cells in each notebook for detailed explanations of each step's purpose and interpretation.
