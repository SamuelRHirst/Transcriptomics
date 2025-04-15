## 📊  Estimating Gene Expression (HTSeq-count & StringTie2)

Now that we’ve mapped our transcriptomes to the genome, we can begin estimating **gene expression levels**.

There are many tools available, but here we’ll focus on two widely used ones:

- **HTSeq-count** — generates **raw counts per gene** for statistical comparison
- **StringTie2** — produces **TPM/FPKM values** and transcript-level expression

Each tool is suited to different downstream goals.

---

### 🔍 HTSeq-count vs. StringTie2: What's the Difference?

| Tool         | Output Type        | Best For                                      |
|--------------|--------------------|-----------------------------------------------|
| HTSeq-count  | Raw gene counts     | **Differential expression** (e.g., DESeq2, edgeR) |
| StringTie2   | TPM, FPKM, transcript structures | **Visualization**, **relative expression**, and isoform-level summaries |

> 💡 Use **HTSeq-count** when you want to compare gene expression across conditions or replicates (e.g. between samples). 
> Use **StringTie2** when you want normalized expression (e.g., TPM) or when visualizing how much a gene is expressed overall (e.g. within a single sample).

---

## 📥 Input Files

Both tools take:
- A **sorted BAM file** (`sample.sorted.bam`)
- A **GFF or GTF** file describing gene models

---

### 🧬 A. HTSeq-count (Raw Counts for DE Analysis)

HTSeq-count is used to count how many reads map to each gene in a GFF3 file.

```bash
htseq-count \
  --nprocesses=50 \
  --format=bam \
  --type=gene \
  --idattr=Name \
  --stranded=no \
  --order=pos \
  --nonunique=all \
  -a=10 \
  --counts_output=sample_counts.csv \
  sample.sorted.bam \
  your_annotation.gff3
```
Explanation:

--nprocesses=50 → number of threads

--type=gene → count reads overlapping features of type "gene"

--idattr=Name → uses the Name= attribute in your GFF3 to identify genes

--nonunique=all → count reads mapping to multiple genes

-a=10 → minimum alignment quality

--counts_output= → where to write the final count table

sample.sorted.bam → sorted BAM file

your_annotation.gff3 → GFF3 with gene models

🎯 Use this count table as input to DESeq2, edgeR, etc.

### 📊 B. StringTie2 (TPM/FPKM for Visualization)
StringTie2 estimates transcript-level and gene-level expression values using your annotation.

```bash
stringtie \
  -p 50 \
  -G your_annotation.gff3 \
  -e \
  -o sample_transcripts.gtf \
  -B \
  -A sample_abundance.tab \
  sample.sorted.bam
```
Explanation:

-p 50 → number of threads

-G → your reference GFF3 file

-e → only estimate known genes (no new assembly)

-o → transcript-level GTF output

-A → gene-level abundance table (includes TPM/FPKM)

-B → prepares ballgown/ directory if needed

Output:

sample_abundance.tab → table with raw counts, FPKM, and TPM per gene

sample_transcripts.gtf → transcript-level annotations with expression

🎯 Use TPM values from the abundance table for plotting pie charts, heatmaps, or exploring expression patterns across genes.
