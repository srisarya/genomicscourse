---
layout: page
---

## Part 2: Genome assembly

You need to have gone through [Part 1: Read cleaning](read-cleaning) before starting this practical.

### Brief assembly example / concepts

Many different pieces of software exist for genome assembly. We will be using [SPAdes](https://cab.spbu.ru/software/spades/).

Create a new main directory for today's practical (e.g., `2021-09-xx-assembly`) and the `input`, `tmp`, and `results` subdirectories. Link the output (cleaned reads) from yesterday's practical into `input` subdirectory:

```
cd ~/2021-09-xx-assembly
cd input
ln -s ~/2021-09-xx-read_cleaning/results/reads.pe*.clean.fq .
cd ..
```

Did you note the use of `*` in the above command? What do you think it means? (hint: it is called 'globbing')

To assemble our cleaned reads with SPAdes, run the following line. *THIS WILL TAKE ABOUT 10 MINUTES*

```bash
spades.py -o tmp -1 input/reads.pe1.clean.fq -2 input/reads.pe2.clean.fq
```

Like any other assembler, SPAdes creates many files, including a `scaffolds.fasta` file that is likely to be used for follow-up analyses[.](../../data/reference_assembly/output/scaffolds.fasta.gz?raw=true) Copy this file to your results directory:

```bash
cp tmp/scaffolds.fasta results/
```

Take a look at the contents of this file (e.g., `less results/scaffolds.fasta`). Does it contain a lot of NNNN sequences? What do you think might be the reason for that? (Do not worry if your assembly does not contain NNNN sequences.)

There are many other genome assembly approaches. While waiting for everyone to make it to this stage, try to understand some of the challenges of *de novo* genome assembly and the approaches used to overcome them via the following papers:

 * [Genetic variation and the *de novo* assembly of human genomes. Chaisson  et al 2015 NRG](https://www.nature.com/articles/nrg3933) (to overcome the paywall, login via your university, email the authors, or try [scihub](http://en.wikipedia.org/wiki/Sci-Hub)).
 * The now slightly outdated (2013) [Assemblathon paper](http://gigascience.biomedcentral.com/articles/10.1186/2047-217X-2-10).
 * [Metassembler: merging and optimizing *de novo* genome assemblies. Wences & Schatz (2015)](http://genomebiology.biomedcentral.com/articles/10.1186/s13059-015-0764-4).
 * [A hybrid approach for *de novo* human genome sequence assembly and phasing. Mostovoy et al (2016)](https://www.nature.com/articles/nmeth.3865).


### Quality assessment

How do we know if our genome is good?

> *"... the performance of different *de novo* genome assembly algorithms can vary greatly on the same dataset, although it has been repeatedly demonstrated that no single assembler is optimal in every possible quality metric [6, 7, 8]. The most widely used metrics for evaluating an assembly include 1) contiguity statistics such as scaffold and contig N50 size, 2) accuracy statistics such as the number of structural errors found when compared with an available reference genome (GAGE (Genome Assembly Gold Standard Evaluation) evaluation tool [8]), 3) presence of core eukaryotic genes (CEGMA (Core Eukaryotic Genes Mapping Approach) [9]) or, if available, transcript mapping rates, and 4) the concordance of the sequence with remapped paired-end and mate-pair reads (REAPR (Recognizing Errors in Assemblies using Paired Reads) [10], assembly validation [11], or assembly likelihood [12])."* -  [Wences & Schatz (2015)](http://genomebiology.biomedcentral.com/articles/10.1186/s13059-015-0764-4)



#### Simple metrics

An assembly software will generally provide some statistics about what it did. But the output formats differ between assemblers. [Quast](http://quast.sourceforge.net/quast), the *Quality Assessment Tool for Genome Assemblies* creates a standardized report. Run Quast (`quast.py`) on the `scaffolds.fasta` file. No special options - just the simple scenario to get some statistics.

Have a look at the report (pdf or html) Quast generated (hint: copy over quast's output directory to `~/www/tmp`).

What do the values in the table mean? For which ones is higher better, and for which ones is smaller better? Why do you think Quast uses the word "contig"?

Perhaps we have prior knowledge about the %GC content to expect, the number of chromosomes to expect, and the total genome size – these can inform comparisons with output statistics present in Quast's report.


#### Biologically meaningful measures

Unfortunately, with many of the simple metrics, it is difficult to determine whether the assembler did things correctly, or just haphazardly stuck lots of reads together!

We probably have other prior information about what to expect in this genome. For example:
 1. If we have a reference assembly from a not-too-distant relative, we could expect large parts of genome to be organised in the same order, i.e., synteny.
 2. If we independently created a transcriptome assembly, we can expect  the exons making up each transcript to map sequentially onto the genome (see [TGNet](http://github.com/ksanao/TGNet) for an implementation).
 3. We can expect the different scaffolds in the genome to have a unimodal distribution in sequence read coverage. Similarly, one can expect GC% to be unimodally distributed among scaffolds. Using this idea, the [Blobology](https://github.com/sujaikumar/assemblage) approach determined that evidence of foreign sequences in Tardigrades is largely due to extensive contamination rather than extensive horizontal gene transfer [Koutsovoulos et al 2016](http://www.pnas.org/content/113/18/5053).
 4. We can expect different patterns of gene content and structure between eukaryotes and prokaryotes.
 5. Pushing this idea further, we can expect a genome to contain a single copy of each of the "house-keeping" genes found in related species. This is applied in BUSCO (Benchmarking Universal Single-Copy Orthologs). Note that:
    * BUSCO is a refined, modernized implementation of [CEGMA]("http://korflab.ucdavis.edu/Datasets/cegma/") (Core Eukaryotic Genes Mapping Approach). CEGMA examines a eukaryotic genome assembly for presence and completeness of 248 "core eukaryotic genes".
    * QUAST also includes a "quick and dirty" method of finding genes.

It is *very important* to understand the concepts underlying these different approaches.

### In your own time

Try to figure out what are the [tradeoffs between `de bruijn` graph and `overlap-layout-consensus` assembly approaches](https://www.nature.com/articles/nrg3367).
