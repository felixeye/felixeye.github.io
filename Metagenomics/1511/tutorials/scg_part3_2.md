---
layout: default
title:  'Part 3: Single cell genome assembly using SPAdes'
---

# Part 3: Single cell genome assembly

## 3.2. Pre-processing

Before assembly you can do several pre-processing steps. Here we will focus on quality trimming of the reads with the software _Trimmomatic_ . Keep in mind which dataset you will make assemblies for, perhaps you do not need to trim your dataset.

**Trimming reads with Trimmomatic** 

*Trimmomatic* is a collection of tools for reads pre-treatment. We will use it to cut the Illumina adaptor with ```ILLUMINACLIP``` giving the location of the sequences to be removed, as well as some parameters describing for example how many mismatches shold be tolerated. ```LEADING``` and ```TRAILING``` removes bases below the given threshold from the beginning and end of the read. ```SLIDINGWINDOW``` performs a sliding window quality trimming in a given window length (4) using given quality threshold (15). Finally, ```MINLEN``` specifies a length threshold for reads to be kept. In case after all of the pre-processing steps the reads get shorter than this threshold (36) they are discarded. You can read up on the details of these commands on the [Trimmomatic website](http://www.usadellab.org/cms/?page=trimmomatic) 

We first created a folder to store the trimmed files:

```sh
mkdir trimmed
```

Make sure before you run this command that 'trim' variable is set correctly.

```sh
java -jar $TRIMMOMATIC_HOME/trimmomatic.jar PE -phred33 \
-basein G5_${sample}_R1_001.fastq -baseout trimmed/G5_${sample}${trim}.fastq \
ILLUMINACLIP:$TRIMMOMATIC_HOME/adapters/NexteraPE-PE.fa:2:30:10 \
LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

The previous command create 4 different files in the folder *trimmed*:  
* *G5_${sample}_Trimmomatic_1P.fastq*: paired forward reads  
* *G5_${sample}_Trimmomatic_2P.fastq*: paired forward reads  
* *G5_${sample}_Trimmomatic_1U.fastq*: unpaired forward reads  
* *G5_${sample}_Trimmomatic_2U.fastq*: unpaired reverse reads  

We will merge both unpaired files into a single one: 

```sh
cat trimmed/G5_${sample}_Trimmomatic_1U.fastq trimmed/G5_${sample}_Trimmomatic_2U.fastq > trimmed/G5_${sample}_Trimmomatic_U.fastq
#rm trimmed/G5_${sample}_Trimmomatic_1U.fastq trimmed/G5_${sample}_Trimmomatic_2U.fastq
```

<!---
**Optional steps** 

If you have time you can come back here and check the following extra steps.

To visualize the quality of the reads you can use [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) which provide plots for several checks together with some guidelines on which results might be suspicious. You can see for example a plot of qualities along the read length, look at the duplication level, and so on.

Another step you can do if your library setup is such that sequencing reads should be overlapping, is merging them. An example of how to do that is described [here](scg_part3_merging). Considering that all of the assemblers we use can take in paired reads, and some of them (Spades) actually do not recommend using the qualities that the merging result in, we skip this for the main assembly comparison. It can still be a useful step for other purposes.
-->
