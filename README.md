# GOBLET (Goner-Orchestrated Buffering via Lethality and Expression in Tumours)

A computational pipeline for identifying synthetic lethality candidates in pancreatic cancer through transcriptional buffering analysis.

---

## Overview

This pipeline identifies genes whose expression positively correlates with loss-of-function (LOF) mutation burden in *Goner* genes — genes that are common-essential across cancer cell lines. Candidates are then filtered against curated synthetic lethality databases to reveal potential therapeutic targets.

The concept of Goners was first defined by CIE Smith and R Zain. The pipeline was written by mab, with inspiration from similar pipelines referenced in the paper and with assistance from paper co-authors. Code cleanup and commenting were assisted by Claude Sonnet 4.6 (Anthropic).

---

## Paper

> **Unbiased Goner-Associated Synthetic Lethality Screens Reveal Metabolic Vulnerabilities in Pancreatic Cancer**
> *(manuscript in preparation)*

---

## Pipeline summary

1. Download Goner list from DepMap (Chronos Combined, common-essential genes)
2. Build a binary LOF matrix from the TCGA PAAD mutation annotation file
3. Align with TCGA PAAD RNA-seq expression data
4. Compute Spearman correlation between per-sample LOF burden and gene expression, with Benjamini–Hochberg FDR correction
5. Filter out housekeeping genes (HOUNKPE / HRT Atlas; Hsiao sets)
6. Map gene identifiers from Entrez to HGNC via biorosetta
7. Intersect with a curated synthetic lethality database
8. GO Biological Process enrichment analysis and visualisation

---

## Requirements

No local installation is needed. The notebook runs entirely in Google Colab.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/d3dd/skunk-lm/blob/master/GonerFull24Feb2026.ipynb)

Dependencies are installed automatically in the first cell:
`pandas`, `openpyxl`, `scipy`, `statsmodels`, `lifelines`, `requests`, `gseapy==1.1.3`, `biorosetta`

---

## Input data

The pipeline requires two input files which must be uploaded when prompted:

| File | Description | Source |
|------|-------------|--------|
| `paad_expr_matrix.xlsx` | TCGA PAAD RNA-seq expression matrix (genes × samples) | [cBioPortal](https://www.cbioportal.org/study/summary?id=paad_tcga) |
| `paad_maaf.xlsx` | TCGA PAAD mutation annotation file | [cBioPortal](https://www.cbioportal.org/study/summary?id=paad_tcga) |

Two additional housekeeping gene files are required:

| File | Description | Source |
|------|-------------|--------|
| `Human_Mouse_Common.csv` | HOUNKPE housekeeping genes | [housekeeping.unicamp.br](https://housekeeping.unicamp.br) |
| `HSIAO_Cleaned_paired_genes.csv` | Hsiao housekeeping genes (paired Entrez ID + symbol) | [MSigDB](https://www.gsea-msigdb.org/gsea/msigdb/human/geneset/HSIAO_HOUSEKEEPING_GENES.html) |

And the curated synthetic lethality database:

| File | Description |
|------|-------------|
| `gene_sl_gene.csv` | Curated SL gene pairs (columns: `x_name`, `y_name`) — see paper for source |

File was generated from DepMap Public 25Q3 dataset at depmap.org To regenerate from a later release, delete this file and re-run cell 2, which will download and cache the current version automatically. We acknowledge Broad and their details are described in their paper:
Arafeh, R., Shibue, T., Dempster, J.M. et al. The present and future of the Cancer Dependency Map. Nat Rev Cancer 25, 59-73 (2025). https://doi.org/10.1038/s41568-024-00763-x

---

## How to run

1. Click the **Open in Colab** badge above
2. Run all cells in order (**Runtime → Run all**)
3. Upload files when prompted (each file is only requested once per session)
4. All outputs are downloaded automatically at the end

---

## Outputs

| File | Description |
|------|-------------|
| `goner_list.txt` | DepMap common-essential gene list |
| `tcga_goner_lof.xlsx` | Binary LOF matrix (samples × genes) |
| `LOF_expression_correlations.csv` | Full Spearman correlation results |
| `LOF_expression_correlations_significant.csv` | Significant hits (ρ > 0.3, BH-FDR < 0.05) |
| `corr_mapped_entrez_to_hgnc.csv` | Entrez → HGNC symbol mapping |
| `buffering_candidates_curatedSL.csv` | Final SL-overlap buffering candidates |
| `SL_overlap_enrichment_results.csv` | GO enrichment results |
| `enrichment_barplot.png` | Bar plot of top GO terms |
| `enrichment_dotplot.png` | Dot plot of top GO terms |
| `em_nodes.csv`, `em_edges.csv` | Enrichment map network data |
| `go_enrichment_table.tex` | LaTeX table for manuscript |

---

## Licence

This project is licensed under the [GNU General Public License v3.0](LICENSE).
