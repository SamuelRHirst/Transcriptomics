# 🧬 Transcriptome Mapping Pipeline

This walkthrough covers the first step in processing short-read RNA-seq data: **quality checking, trimming, and mapping** to a reference genome.

We assume you're working with **paired-end Illumina reads** from RNA-seq and want to generate **sorted BAM files** for downstream analyses (e.g., annotation support, expression estimation).

---

##  Step 1: Quality Check & Trimming

Before mapping reads, we need to:
1. Check raw read quality
2. Trim adapters and low-quality ends

We'll use:
- [`FastQC`](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) for quality assessment
- [`Trim Galore`](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/) for trimming and auto-running FastQC

---

 Input Files
sample_R1.fastq.gz — forward reads

sample_R2.fastq.gz — reverse reads

 Run Trimming + QC

```bash
trim_galore --paired sample_R1.fastq.gz sample_R2.fastq.gz --fastqc -o trimmed_reads/
```

What this does:

Automatically trims adapters and low-quality bases

Produces trimmed read files

Runs FastQC on both raw and trimmed reads

Sends all output to the trimmed_reads/ directory

 Output Files
In trimmed_reads/, you'll get:

sample_R1_val_1.fq.gz — trimmed forward reads

sample_R2_val_2.fq.gz — trimmed reverse reads

FastQC reports (*.html) for both raw and trimmed reads

Log files describing what was trimmed

 These trimmed FASTQs will be used as input for mapping in the next step.

##  Step 2: Mapping Reads to the Genome with HISAT2

After trimming, the next step is to **map the cleaned RNA-seq reads** to your genome assembly.

For this step, we’ll use:
- [`HISAT2`](https://daehwankimlab.github.io/hisat2/) — a fast and splice-aware aligner
- [`Samtools`](http://www.htslib.org/) — for sorting and indexing the output

>  In this step, we're just generating **sorted BAM files** to use in genome annotation (e.g., with GeMoMa) or downstream visualization. No transcript quantification yet.

---

 Input Files
sample_R1_val_1.fq.gz — trimmed forward reads (from Trim Galore)

sample_R2_val_2.fq.gz — trimmed reverse reads

A HISAT2 genome index (must be pre-built)

 Build HISAT2 Index (if needed)
Only need to do this once per genome:

```bash
hisat2-build your_genome.fasta your_genome_index
```
 Run Mapping
```
hisat2 -p 16 \
  -x your_genome_index \
  -1 sample_R1_val_1.fq.gz \
  -2 sample_R2_val_2.fq.gz \
  -S sample.sam
```
Explanation:

-p 16 → number of threads

-x → base name of the HISAT2 index

-1/-2 → paired-end reads

-S → output SAM file

 Convert to Sorted BAM

```BASH
samtools view -b sample.sam > sample.bam
samtools sort sample.bam -o sample.sorted.bam
samtools index sample.sorted.bam
```
I typically delete the .sam file at this point

 Output Files
sample.sorted.bam — sorted alignments

sample.sorted.bam.bai — BAM index

 This BAM file is now ready for use in annotation pipelines, visualization in genome browsers, or expression quantification.

