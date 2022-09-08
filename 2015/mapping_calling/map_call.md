# BIO721P Genome-Bioinformatics practical: read mapping and variant calling

Roddy Pracana and Yannick Wurm

## Introduction

In the previous practical, we created a genome assembly from a single individual. But studying the genome of a single individual is rarely interesting. In most medical and evolutionary genetics studies, we actually want to study the genomes of many individuals. By studying the differences between the genomes, we can start understanding the genetic basis of phenotypic variation (such as the genetic basis of complex traits and diseases) or we can infer the evolutionary processes that have affected the population we're studying (such as understanding population structure, speciation, selection, etc.).

Thus, we need a method of finding genetic variants between individuals in a sensitive and accurate way, a process normally known as variant calling.

There are several types of variants. Commonly, people look at single nucleotide polymorphisms (SNPs, sometimes also known as single nucleotide variants, SNVs). Other classes include small insertions and deletions (known collectively as indels), as well as larger structural variants, such as large insertions, deletions, inversions and translocations.

There are several approaches to variant calling from short pair-end reads. We are going to use one of them. First, we are going to map the reads from each individual to a reference assembly similar to the one created in the previous practical. Then we are going to find the positions where at least some of the individuals differ from the reference (and each other).

## The data

We will be analysing subsets of whole-genome sequences of several fire ant individuals. The fire ant, *Solenopsis invicta*, is notable for being dimorphic in terms of colony organisation, with some colonies having one queen and other colonies having multiple queens. Interestingly, this trait is genetically determined. In this practical, we are going to try to find the genetic difference between ants from single queen and multiple queen colonies.

We will be using a subset of the reads from whole-genome sequencing of 14 male fire ants. Samples 1B to 7B are from single-queen colonies, samples 1b to 7b are from multiple-queen colonies. Ants are haplodiploid, which means that they have haploid males.

We will align the reads to a subset of the reference genome assembly of the species (the same regions we tried to assemble earlier) using the aligner `bowtie2`. We will try to find positions that differ between each individual and the reference with the software `samtools` and `bcftools`.

## Before we start the analysis

### Connect to the cluster and load the modules we will be using

```bash
# First, login to Apocrita
ssh abc123@login.hpc.qmul.ac.uk

# From there, log into the MSc machine:
ssh dn36 # or ssh dn42
```

Now type into the console:

```bash
module load samtools/1.1 bcftools/1.1 HTSlib/1.1
```

This loads the right version of the tools we are using in this practical. You need to type this if you restart a new session on the cluster, otherwise you will be using the wrong version of the tools and the practical will not work!

To find if you've loaded the correct version of the modules, type:

```bash
which samtools
```

If you don't get version `1.1`, then you don't have the right module loaded.

### Create a directory for your analysis

To create a directory for the analysis in this practical, type, in your home directory:

```bash
mkdir 2015-10-map-call-practical
```

Use `cd` to go into that directory.

There, use `mkdir` to create a directory called `data` and one called `2015-10-07-analysis`.

### Create soft links for the data in the new directory

The input data is stored in a folder called:

```bash
/data/SBCS-MSc-BioInf/2015-10-practical/map_call
```

In it, you can find a file with the reference assembly (`reference.fa`) and a directory with the reads  in the `.fq` format (`reads`). Make a soft link to those files from the new `data/` directory:

```bash
cd ~/2015-10-map-call-practical/data
ln -sf /data/SBCS-MSc-BioInf/2015-10-practical/map_call/reference.fa .
ln -sf /data/SBCS-MSc-BioInf/2015-10-practical/map_call/reads/* .
```

By doing this, you create a soft link to the data instead of copying it, so you don't multiply the amount of space you are using up. Never use the command `rm` on soft links, as it will also delete the original data. Instead, use the command `unlink`.

You should see the reads for 14 samples.

#### Q. How many scaffolds are there in the assembly? (Try using `grep`)

#### Q. (important!) Why does each sample have two sets of reads?

#### Q. What is each line of the `.fq` file?

#### Q. How many reads do we have in individual f1_B?

#### Q. What's the size of each read (all reads have equal size)?

#### Q. Knowing that each scaffold is 200kb, what is the expected coverage per base pair of individual f1_B?

### Create soft links from the data

```bash
## Go back to the original directory:
cd ~/2015-10-map-call-practical/2015-10-07-analysis
ln -sf ../data/reference.fa .
ln -sf ../data/*.fq .
```

Note that the star `*` refers to all the files in the `../data/reads/` directory. The `.` is the current directory.

## Aligning reads to a reference assembly

The first step in our pipeline is to align the paired end reads to the reference genome. We are using the software `bowtie2`, which was created to align short read sequences to long sequences such as the scaffolds in a reference assembly. `bowtie2`, like most aligners, works in two steps. In the first step, the scaffold sequence (sometimes known as the database) is indexed, in this case using the Burrows-Wheeler Transform, which allows for memory efficient alignment. The second step is the alignment itself.

Let's start by creating the reference index (make sure you are using `bowtie2` and not the earlier version, bowtie):

```bash
bowtie2-build reference.fa reference_index
```

Now the alignment step:

```bash
bowtie2 -x reference_index -1 f1_B.1.fq -2 f1_B.2.fq > f1_B.sam
```

#### Q. What do the parameter `-x`, `-1` and `-2` mean?

The command produced a SAM file (Sequence Alignment/Map file), which is the standard file used to store sequence alignments. Have a quick look at the file by typing `less f1.sam`. The file includes a header (lines starting with the `@` symbol), and a line for every read aligned to the reference assembly. For each read, we are given a mapping quality values, the position of both pairs, the actual sequence and its quality by base pair, and a series of flags with additional measures of mapping quality.

We now need to run bowtie2 for all the other samples. We could do this by typing the same command another 13 times (changing the sample name), or we can use the `GNU parallel` tool:

```bash
## Create a file with all sample names
ls *fq | cut -d '.' -f 1 | sort | uniq > names.txt

## Run bowtie with each sample (will take a few minutes)
parallel "bowtie2 -x reference_index -1 {}.1.fq -2 {}.2.fq > {}.sam" :::: names.txt
```

Because SAM files include a lot of information, they tend to occupy a lot of space (even in our case). Therefore, SAM files are generally compressed into BAM files (Binary sAM). Most tools that use aligned reads requires BAM files that have been sorted and indexed by genomic position. This is done using `samtools`, a set tools create to manipulate SAM/BAM files. To compress and sort a SAM file for a given sample, we type:

```bash
## SAM to BAM.
# samtools view: compresses the SAM to BAM
# samtools sort: sorts by scaffold position (creates f1_B.sorted.bam)
samtools view -Sb f1_B.sam | samtools sort - f1_B.sorted
## This creates a file (f1_B.sorted.bam), which we then index
samtools index f1_B.sorted.bam   # creates f1_B.sorted.bam.bai
```

#### Q. What does the dash (`-`) in the `samtools sort` command mean?

Again, we can use parallel to run this step for all the samples:

```bash
parallel "samtools view -Sb {}.sam | samtools sort - {}.sorted" :::: names.txt
parallel "samtools index {}.sorted.bam" :::: names.txt
```

## Variant calling

There are several approaches to call variants. The simplest approach is to look for positions where the mapped reads consistently have a different base than the reference assembly (the consensus approach). We need to run two steps, `samtools mpileup`, which looks for inconsistencies between the reference and the aligned reads, and `bcftools call`, which interprets them as variants.

```bash
# Step 1: samtools mpileup
## Create index of the reference (different from that used by bowtie2)
samtools faidx reference.fa

# Run samtools mpileup
samtools mpileup -uf reference.fa *sorted.bam > raw_calls.bcf
```

#### Q. What does the symbol`*` mean here?

If you type `bcftools` in the console, you will see that this programme has a series of tools to manipulate VCF/BCF files. The tool we want to use is `bcftools call`, which does SNP and indel calling. As well as the consensus caller (option `-c`), which we are using, bcftools includes the multiallelic caller (option `-m`). Because we have a relatively small number of samples and low coverage for each of the sample, the consensus caller will do. Before we carry on, we need to remember that we are analysing male ants, which have haploid genomes! Because of this, we need to use option -S, which allows the user to add a list with two columns, the first with the name of the samples (i.e. that name of the `bam` files), the second with their ploidy (1 or 2).

#### Q. Make a file (called ploidy_per_sample.txt) with a column with the bam file names (e.g. f1_B.sorted.bam) and a column with the ploidy level (i.e. 1).

Now we run the bcftools call command and save the result into a VCF file.

```bash
bcftools call -c raw_calls.bcf -v -S ploidy_per_sample.txt > variant_calls.vcf
```

#### Q. What does the parameter `-v` do? Under what situation would you leave it out?

The file produced a VCF (Variant Call Format) format telling the position, nature and quality of the called variants. The VCF format is the standard format used

Let's take a look at the VCF file produced by typing `less -S variant_calls.vcf`. The file is composed of a header and of and rows for all the variant positions. Have a look at the different columns and check what each is (the header includes labels). Notice that some columns include several fields.

#### Q. Use `less` to look at the VCF file. Where does the Header start and end?

#### Q. How is the genotype of each sample coded?

#### Q. How many variants were identified?

#### Q. Can you tell the difference between SNPs and indels? How many of each have been identified?

## Quality filtering of variant calls

Not all variants that we called are necessarily of good quality, so it is essential to have a quality filter step. The VCF includes several fields with quality information. The most obvious is the column QUAL, which gives us a Phred-scale quality score.

#### Q. What does a Phred-scale quality score of 30 mean?

We will filter the VCF using `bcftools filter`. We can remove anything with quality call smaller than 30:

```bash
bcftools filter --exclude 'QUAL < 30' variant_calls.vcf > filtered_variant_calls.vcf
```

In more serious analysis, it may be important to filter by other parameters.

#### Q. Can you find any other parameters indicating the quality of the site?

#### Q. Can you find any other parameters indicating the quality of the call for a given individual on a given site?

## Viewing the results using IGV (Integrative Genome Viewer)

In this part of the practical, we are going to use the software IGV on our local computer to visualise the alignments we created and check some of the positions where variants were called.

First, download the IGV software (you will need to register your email address). Unzip it to your desktop (or wherever you want it), and click it to open. If you can't click it, type, on a new terminal window:

```bash
cd ~/Desktop/IGV_2_3_35/
./igv.sh
```

Doing this should open IGV.

We then need to get a copy the reference assembly fasta and some of the alignments (and their index files) from the cluster to your PC. Make sure you transfer BAMs from the two different colony types.

To run IGV, you need to define a genome file, which you have to create from the fasta alignment (Genome > Genomes from file, then choose the assembly fasta file.

You can loads some of the BAMS and the VCF file you produced.

#### Q. Has bcftools/mpileup recovered the same positions as IGV?

#### Q. Do you think our filtering was effective?

## Simple analysis of the variants

In this section we are going to analyse the genotypes we identified. We are going to use the software MeV to create a heat map of the genotypes and to run Principal Component Analysis (PCA). MeV does not use the standard VCF. Instead, it requires a variant matrix in a particular, non-standard format: one column for the scaffold name, one column with the variant position, and a column for each sample with the genotype.

Run the following script. It will read in the VCF and transform it to the genotype matrix in the required format:

```bash
# Create a soft link to the script
ln -sf /data/SBCS-MSc-BioInf/2015-10-practical/map_call/vcf_to_mev.sh .
# Run script to create variant matrix
./vcf_to_mev.sh filtered_variant_calls.vcf > mev_var.txt
```

#### Bonus Q (do at home if you have time). One of the most common (and tedious) jobs for Bioinformaticians is to transform data from one format to another. Can you write your own script to create the variant matrix for MeV?

Now download the variant matrix from the cluster to your PC.

If you don't have MEV installed, download it [here](http://sourceforge.net/projects/mev-tm4/files/).

Open the application and import the data (under File). A heat map is produced automatically.

#### Q. Analyse the variant calls:

In MeV, perform hierarchical clustering of the data and do a PCA. Can you find any consistent difference between B and b samples in our data?
