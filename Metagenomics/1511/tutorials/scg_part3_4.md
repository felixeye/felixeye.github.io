---
layout: default
title:  Part 3: Single cell genome assembly using SPAdes
---

# Part 3: Single cell genome assembly

## 3.4. Assessing assembly quality using Quast


After assembling the reads into contigs, you will use this tool called 'Quast' to calculate the basic metrics such as the length of largest contig, N50, etc.  
To run 'Quast', first we will create a link to the different contig files in the assembly folder. 
Use the following commands to help yourself:  

```sh
cd spades_assemblies
ln -s `pwd`/G5_${sample}_${merge}sc_careful/contigs.fasta G5_${sample}_${merge}sc_careful.fasta
```

then run quast on all assemblies at once:

```sh
#module load quast/2.3
quast.py -o assembly_metrics *.fasta
cd ..
```

Results from 'Quast' will be found in the 'assembly_metrics' folder. Take a look at the files produced by the program. 
The summary file containing the stats is 'report.txt'. You should see the assembly metrics such as N50, G+C%, largest contig, total length (i.e., total length of all contigs added).  

Report the results in the spreadsheet.