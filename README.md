# Single-Cell RNA-seq Analysis: Pediatric B-ALL

A single-cell RNA-seq analysis pipeline for pediatric B-cell acute lymphoblastic leukemia (ETV6-RUNX1 subtype) vs. healthy pediatric bone marrow, combining traditional bioinformatics methods with deep learning (scVI) for normalization and batch correction, plus independent CNV-based confirmation of malignant cells.

## Data source

Public data from GEO series [GSE132509](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE132509):

> Caron, M., St-Onge, P., Sontag, T. et al. Single-cell analysis of childhood leukemia reveals a link between developmental states and ribosomal protein expression as a source of intra-individual heterogeneity. *Sci Rep* 10, 8079 (2020). https://doi.org/10.1038/s41598-020-64929-x

Samples used: 4 ETV6-RUNX1 leukemia patients (GSM3872434-GSM3872437) and 3 healthy pediatric bone marrow donors (GSM3872442-GSM3872444).

Data is not included in this repository (see `.gitignore`) due to file size and to avoid redistributing third-party data directly. `leukemia_analysis_full_pipeline.ipynb` documents the exact GEO download commands needed to reproduce the dataset locally.

## Pipeline overview

1. **Quality control** - filtering by gene count, total counts, and mitochondrial %
2. **Feature selection** - highly variable genes
3. **Deep learning normalization/batch correction** - scVI variational autoencoder, correcting for sample-of-origin across multiple patients
4. **Clustering and UMAP** - Leiden clustering on the scVI latent space
5. **Marker-gene-based cell type annotation** - B-lineage/blast, erythroid, immune, and mature B cell markers
6. **Differential expression** - Wilcoxon rank-sum test, volcano plots
7. **CNV-based malignancy confirmation** - `infercnvpy`, using confidently-normal cell types as a reference baseline

## Structure

- **Part A** - single-patient pilot (1 leukemia sample vs. 1 healthy sample), validating the pipeline end-to-end
- **Part B** - the main multi-patient analysis (4 leukemia patients vs. 3 healthy donors), giving properly replicated statistical results
- **Part 3** - independent CNV-based confirmation of the marker-gene-based blast classification

## Setup

```bash
mamba env create -f environment.yml
conda activate sc-env
jupyter lab
```

## Key findings

- Leukemic blasts show strong, replicated upregulation of MHC class II genes (`HLA-DRA`, `CD74`, `HLA-DPB1`, etc.) and `DNTT`, consistent with published B-ALL immunophenotyping.
- Findings that appeared strong in the single-patient pilot (`MEF2C`, `DNTT` as top hits) did not fully replicate once pooled across all 4 patients - an illustration of why single-patient conclusions in cancer genomics warrant caution.
- CNV inference independently confirmed the marker-gene-based blast classification (~50% higher mean CNV score in blasts vs. normal cells: 0.00537 vs. 0.00357), with a modest rather than dramatic signal - consistent with ETV6-RUNX1 being a translocation-driven rather than aneuploidy-driven leukemia subtype.
- A patient-specific chromosome 16 CNV signal was observed in one patient (ETV6-RUNX1_1) not clearly present in the other three, suggestive of a possible secondary chromosomal event.

## Notes

This project was built as a learning exercise in single-cell analysis. See inline markdown cells in the notebook for detailed explanations of each step's purpose and interpretation, including known issues encountered (e.g. a bug in `infercnvpy`'s built-in `genomic_position_from_gtf()` function, worked around manually).
