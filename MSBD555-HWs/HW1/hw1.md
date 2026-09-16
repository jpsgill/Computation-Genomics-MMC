# Homework: Exploring human gene annotation with Unix

*Adapted from Aaron Quinlan's Applied Computational Genomics Homework #1 (University of Utah).*

## What this is about

Every gene, transcript, exon and coding segment that we "know" in the human genome is written down in an annotation file. The standard format is **GTF** (Gene Transfer Format): one line per feature, nine tab-separated columns, and it is the file that turned a coordinate into a consequence in class (ITPA, P32T). In this homework you will use the Unix commands from class — `ls`, `wc`, `head`, `grep`, `cut`, `sort`, `uniq`, `awk` and the pipe `|` — to ask real questions of Ensembl's annotation of **chromosome 20**. The whole-genome file is a gigabyte; the chromosome 20 slice is 3 MB and has exactly the same format, so everything you learn here works unchanged on the full file.

You need a Unix shell: Terminal on a Mac, any Linux, or WSL on Windows. No bioinformatics software is required.

For every question, give **the command you ran** and **the answer it printed**. Where a question asks you to explain, two to four sentences is enough.

### The GTF format in one table

| Column | Name | Example |
|---|---|---|
| 1 | chromosome | `20` |
| 2 | source | `ensembl_havana` |
| 3 | feature type | `gene`, `transcript`, `exon`, `CDS`, `five_prime_utr`, … |
| 4 | start (1-based) | `3208868` |
| 5 | end | `3227446` |
| 6 | score | `.` |
| 7 | strand | `+` or `-` |
| 8 | frame | `0`, `1`, `2` or `.` |
| 9 | attributes | `gene_id "ENSG…"; gene_name "ITPA"; gene_biotype "protein_coding"; …` |

Column 9 is a semicolon-separated list of `key "value"` pairs. Lines beginning with `#` are header lines, not data.

---

## Setup

### Question 1

Create a directory called `homework-gtf` inside your home directory and move into it. Provide the command(s).

### Question 2

Download the chromosome 20 GTF from the course site into that directory and decompress it. The file is `chr20.genes.gtf.gz` (Ensembl release 116, GRCh38). Then report the size of the decompressed file in human-readable units.

Hints: `curl -O <url>` downloads a file keeping its name; `gzip -d file.gz` decompresses it and drops the `.gz`; `ls -lh` lists sizes in K/M/G.

> If you prefer to build the file yourself from Ensembl (135 MB download), this one line does exactly what we did:
> `curl -s https://ftp.ensembl.org/pub/release-116/gtf/homo_sapiens/Homo_sapiens.GRCh38.116.gtf.gz | gzip -dc | awk -F'\t' '/^#/ || $1=="20"' > chr20.genes.gtf`

---

## Counting things

### Question 3

How many lines in `chr20.genes.gtf` are header lines (beginning with `#`), and how many are data lines? Show the command for each. Check that the two numbers add up to what `wc -l` reports for the whole file.

### Question 4

Column 3 says what kind of feature each line describes. Using a single pipeline of `cut`, `sort` and `uniq`, produce a table of every feature type in the file and how many lines there are of each, sorted from most to least common. Provide the command and the table.

### Question 5

How many data lines belong to **protein-coding genes**? Such lines contain the exact text `gene_biotype "protein_coding"` in column 9. Provide the single command. (Mind the quotes: the double quotes are part of the text you are searching for, so wrap the whole pattern in single quotes.)

### Question 6

How many of those protein-coding lines are **exons** (column 3 is exactly `exon`)? Provide the single command. Note that `grep exon` alone is not good enough — think about what else in the file contains the letters "exon".

### Question 7

How many **CDS** lines (column 3 is exactly `CDS`) from protein-coding genes are on the `+` strand and how many on the `-` strand (column 7)? Provide the command and both numbers.

---

## Genes versus lines

### Question 8

Question 5 counted *lines*, not *genes*: every gene contributes one `gene` line, one `transcript` line per isoform, and one line per exon, CDS, UTR, start and stop codon. So how many **distinct protein-coding genes** are there on chromosome 20? Answer it two independent ways and show that they agree:

(a) Count the lines whose feature type is `gene` and whose biotype is protein coding.

(b) Extract the `gene_id "ENSG…"` value from every protein-coding line, keep each value once, and count them. Hint: `grep -o 'gene_id "[^"]*"'` prints only the matching part of each line.

Then explain in two or three sentences why (a) and (b) agree here, and describe one situation in which counting distinct `gene_id` values would be the safer of the two.

### Question 9

Which protein-coding gene on chromosome 20 has the most annotated transcripts (isoforms)? Give the top five gene names with their transcript counts, and the command. Hint: `transcript` lines, then `grep -o 'gene_name "[^"]*"'`, then count.

### Question 10 — back to class

In class, HG00096 carried a heterozygous C→A at chromosome 20 position **3,213,196**, which ClinVar reports as a drug-response variant in **ITPA**.

(a) Using only the GTF, report the start, end and strand of the ITPA gene.

(b) Write one `awk` command that prints the CDS lines of ITPA which contain position 3,213,196 — that is, lines with `CDS` in column 3, start ≤ 3,213,196 and end ≥ 3,213,196. How many transcripts of ITPA place that position inside coding sequence, and in which exon of ITPA does it fall? (Look at the `exon_number` attribute.)

(c) In one or two sentences: this tells you the variant is in a coding exon. What does the GTF *not* tell you that the annotator in class did?

---

## Bonus

Using `grep -o`, a regular expression, `sort -u` and `wc`, count how many distinct gene names on chromosome 20 are exactly five characters long (letters, digits or hyphen). Provide the command.

---

## What to hand in

One text or Markdown file with, for each question, the command(s) and the output, plus your written explanations for Questions 8(c), 8 explanation, and 10(c). Commands that work are more important than pretty formatting; if a command did not work, say what you tried and what happened.
