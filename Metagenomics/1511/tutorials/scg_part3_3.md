---
layout: default
title:  Part 3: Single cell genome assembly using SPAdes
---

# Part 3: Single cell genome assembly

We will run three different assemblers on the data. You will choose either HiSeq or MiSeq dataset and trim it or use raw reads depending on how you organize your group. Make sure your variables (sample and trimming) are set properly. You will use one dataset so you can have one assembly folder and create three subfolders for the Ray, IDBA and Spades results. 

## 3.3a. Assemble your data Using SPAdes

Spades is a prokaryotic genome assembler that was specifically designed to be able to handle uneven coverage in single cell datasets. It works with Ion Torrent, PacBio and Illumina paired-end, mate-pairs and single reads. Spades is based on a de Bruijn graph and involves removing graph structures that result from erroneus reads. It uses multiple kmer to optimize assembly of different regions. You can read up about Spades at the [website](http://bioinf.spbau.ru/spades). Spades offer excellent user support.

Make a folder for SPAdes assemblies: 

```sh
mkdir spades_assemblies
#cd spades_assemblies
```

* if you work with the **raw** reads  
>```sh
>time spades.py --sc --careful -t 8 -m 24 \
>-1 G5_${sample}_R1_001.fastq \
>-2 G5_${sample}_R2_001.fastq \
>-o spades_assemblies/G5_${sample}_sc_careful
>```

* if you work with the **trimmed** reads  
>```sh
>time spades.py --sc --careful -t 8 -m 24  \
>-1 merged_reads/G5_${sample}_merge_1.fastq.gz \
>-2 merged_reads/G5_${sample}_merge_2.fastq.gz \
>-s G5_${sample}_merged.fastq.gz \
>-o spades_assemblies/G5_${sample}_SeqPrep_sc_careful
>```


*Notice: Those previous commands will launch SPAdes assembly but also check how long the assembly takes. After the assembly has completed, check the time it took for the program to run. You should look at the 'real' time. 
Record the time in the spreadsheet table.
In general, typing the command ```time``` before other commands will help you check how long the computation took.*

**Optional steps** 

If you have time you can investigate the influence of the flags on the assembly time and results. '--careful' flag uses *'bowtie'* tool to map the reads back to the contigs and check for errors due to bad quality sequences and correct these errors. This results in longer assembly times but should improve the results, especially for reads that were not pre-processed. SPAdes can handle single-cell genomic data that is known to be highly biased in terms of sequence coverage along the length of the genome by using the '--sc' flag.


## 3.3b. Assemble your data Using IDBA-UD

IDBA offers a collection of assemblers of which IDBA-UD was designed to handle data with uneven coverage depth. It is also a de Bruijn graph based assembler that iterates over mutliple kmers. You can find the website [here](http://i.cs.hku.hk/~alse/hkubrg/projects/idba_ud/) and although manual is rather limited, there is a google group offering support. 

IDBA-UD does not take the quality files and in the first step you have to convert the fastq file into fasta format compatible with the assembler.

>```sh
fq2fa --merge G5_Hiseq_trimmed_1P.fq G5_Hiseq_trimmed_2P.fq G5_Hiseq_idba.fa
fq2fa --merge ../raw/G5_Hiseq_R1_001.fq ../raw/G5_Hiseq_R2_001.fq ../raw/G5_Hiseq_idba.fa

time idba_ud -r raw/G5_Hiseq_idba.fa -o raw/idba_test --maxk 124 
time idba_ud -r trimmed/G5_Hiseq_idba.fa -o trimmed/idba_test --maxk 124
>``

## 3.3c. Assemble your data Using Ray

Ray is an assembler that can be highly parallelized and can therefore be a good option in terms of running times, especially if you have access to large number of CPUs on a computer cluster. Memory can be another limiting factor when running assemblies for large datasets. We did not do a comparison here to check memory usage for these prepared datasets but that is another thing to keep in mind for your future datasets. Ray also has a version for metagenomes but not for the single cell genomes characterized by the extreme coverage differences, so in this case it surves the purpose of a regular assembler used for comparison with the two specialized assemblers.


>```sh
time mpirun -n 8 Ray -k 31 -p raw/G5_Hiseq_R1_001.fq raw/G5_Hiseq_R2_001.fq -o raw/ray_k31 &> raw/ray.log & 
time mpirun -n 8 Ray -k 31 -p trimmed/G5_Hiseq_trimmed_1P.fq trimmed/G5_Hiseq_trimmed_2P.fq -o trimmed/ray_k31 &> trimmed/ray.log & 

>``


**Optional steps** 

If you have time you can investigate the influence of various kmer lengths on the assembly results. Try for example using kmers increasing in steps of 10 from 30 to 64, which is the hard-coded limit. Check Ray log output file to make sure about the kmer actually used. If number larger than the threshold is given Ray changes it to the maximum allowed, makes a not of it in the log and proceeds. 













