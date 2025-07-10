#  Transcriptomics Pipeline

This repository contains a streamlined pipeline for processing and analyzing **Illumina short-read transcriptome data** from non-model organisms. It supports basic **quality control**, **read mapping**, and **expression analysis**, and is designed to integrate smoothly with genome annotation workflows.

---

##  Overview

This pipeline is structured into two primary stages:

###  Mapping Transcriptomes

Takes raw Illumina RNA-seq reads and:
- Performs quality checks
- Trims adapters and low-quality bases
- Aligns reads to a reference genome
- Outputs **sorted BAM files** for downstream use

These BAMs are especially useful for:
- Supplying transcript evidence to annotation tools like GeMoMa or BRAKER
- Visualizing gene expression in genome browsers

---

###  Gene Expression Estimation

Quantifies expression using your **gene annotation file** (e.g., GFF3 or GTF) and the mapped reads.  
You can:
- Estimate expression per gene or transcript
- Generate count tables for downstream tools (e.g., DESeq2, edgeR)
- Focus on specific genes of interest (e.g., venom genes, tissue-specific markers)

---


