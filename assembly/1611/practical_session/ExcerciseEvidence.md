---
layout: default
title:  'Exercise Evidence'
---

#Preparing evidence data for annotation

This exercise is meant to get you acquainted with the type of data you would normally encounter in an annotation project. You will get an idea of where to download protein sequences, and also try out some programs that often are used. We will for all exercises use data for the fruit fly, Drosophila melanogaster, as that is one of the currently best annotated organisms and there is plenty of high quality data available.

##1. Obtaining data

<u>**Swissprot:**</u> Uniprot is an excellent source for high quality protein sequences. The main site can be found at [http://www.uniprot.org](http://www.uniprot.org). This is also the place to find Swissprot, a collection of manually curated non-redundant proteins that cover a wide range of organisms while still being manageable in size.

**_Exercise 1_ - Swissprot:**  
Navigate the Uniprot site to find the download location for Swissprot in fasta-format. You do not need to download the file, just find it. In what way does Swissprot differ from Uniref (another excellent source of proteins, also available at the same site)?

<u>**Uniprot:**</u> Even with Swissprot available, you also often want to include protein sequences from organisms closely related to your study organism. An approach we often use is to concatenate Swissprot with a few protein fasta-files from closely related organisms and use this in our annotation pipeline.

**_Exercise 2_ - Uniprot:**  
Use Uniprot to find (not download) all protein sequences for all the complete genomes in the family Drosophilidae. How many complete genomes in Drosophilidae do you find?

<u>**Refseq:**</u> Refseq is another good place to find non-redundant protein sequences to use in your project. The sequences are to some extent sorted by organismal group, but only to very large and inclusive groups. The best way to download large datasets from refseq is using their ftp-server at [ftp://ftp.ncbi.nlm.nih.gov/refseq/](ftp://ftp.ncbi.nlm.nih.gov/refseq/).

**_Exercise 3_ - Refseq:**  
Navigate the Refseq ftp site to find the invertebrate collection of protein sequences. You do not need to download the sequences, just find them. The files are mixed with other types of data, which files include the protein sequences?

<u>**Ensembl:**</u> The European Ensembl project makes data available for a number of genome projects, in particular vertebrate animals, through their excellent webinterface. This is a good place to find annotations for model organisms as well as download protein sequences and other types of data. They also supply the Biomart interface, which is excellent if you want to download data for a specific region, a specific gene, or create easily parsable file with gene names etc.

**_Exercise 4_ - Ensembl Biomart:**  
Go to Biomart at [http://www.ensembl.org/biomart/martview](http://www.ensembl.org/biomart/martview) and use it to download all protein sequences for chromosome 4 in Drosophila melanogaster. Once you have downloaded the file, use some command line magic to figure out how many sequences are included in the file. Please ask the teachers if you are having problems here.

##2. Running an ab initio gene finder

<u>**Setup:**</u> For this exercise you need to be logged in to Uppmax. Follow the [UPPMAX login instructions](LoginInstructions).

Before going into the exercises below, you should create in your home folder a specific folder for this practical session and add a symbolic link to a folder with the course data using:  

*cd ~/*

*mkdir annotation_course*

*cd annotation_course*

*ln -s /proj/g2016007/course\_material* 

*mkdir practical1* 

*cd practical1*  
 

When you are done, you should have a folder called course\_material in your *annotation_course* folder. This course\_material folder is write-proteced, it is only a resource for you to obtain data from, but not where you are writing your own outputs to!  
NOTE! We do not supply full paths in all of the exercises below. You will need to find the files yourself, which will be easy since you are an expert Linux-hacker. :)

Also, we have made a genome browser called Webapollo available for you on the address [http://annotation-prod.scilifelab.se:8080/NBIS_gp1/](http://annotation-prod.scilifelab.se:8080/NBIS_gp1/)  called drosophila\_melanogaster\_course.
This browser can already has a number of tracks preloaded for you, but you can also load data you have generated yourself using the ‘file” menu and then ‘open’ and ‘local files’. First time you go there you need to log in using the email adress provided to register the course and your last name as password (lower case and if more than one last name separated by _ eg: lastname1_lastname2).

<u>**Ab initio gene finders:**</u> These methods have been around for a very long time, and there are many different programs to try. We will in this exercise focus on the gene finder Augustus. These gene finders use likelihoods to find the most likely genes in the genome. They are aware of start and stop codons and splice sites, and will only try to predict genes that follow these rules. The most important factor here is that the gene finder needs to be trained on the organism you are running the program on, otherwise the probabilities for introns, exons, etc. will not be correct. Luckily, these training files are available for Drosophila.

**_Exercise 5_ - Augustus:**

First you need to write the librabries path you will need in .bash_profile to perform the following analyses. 

*/home/__login__/annotation_course/course_material/lib/install_perllib_missing.sh*

*source ~/.bash\_profile*


First load the needed modules using:  
_module load bioinfo-tools_  
_module load augustus_

Run Augustus on your genome file using:  
*augustus -\-species=fly /home/__login__/annotation\_course/course\_material/data/dmel/chromosome\_4/chromosome/4.fa > augustus\_drosophila.gtf*

Take a look at the result file using ‘less augustus\_drosophila.gtf’. What kinds of features have been annotated? Does it tell you anything about UTRs?

The gff-format of Augustus is non-standard (looks like gtf) so to view it in a genome browser you need to convert it. You can do this using genometools which is available on Uppmax.

Do this to convert your Augustus-file:

*module load BioPerl*

*~/annotation\_course/course\_material/git/GAAS/annotation/Tools/Converter/gtf2gff3_universal.pl -gtf augustus_drosophila.gtf -o augustus_drosophila.gff3*

Transfer the augustus\_drosophila.gff3 to your computer using scp:    
*scp __login__@milou.uppmax.uu.se:/home/__login__/annotation\_course/practical1/augustus\_drosophila.gff3 .*  

Load the file in [Webapollo](http://bils-web.imbim.uu.se/drosophila_melanogaster). [Here find the WebApollo instruction](UsingWebapollo)
<br/>Load the Ensembl annotation available in  ~/annotation\_course/course\_material/dmel/chromosome\_4/annotation
How does the Augustus annotation compare with the Ensembl annotation? Are they identical?

**_Exercise 6 -_ Augustus with yeast models:**  
Run augustus on the same genome file but using settings for yeast instead (change species to Saccharomyces).

Load this result file into Webapollo and compare with your earlier results. Can you based on this draw any conclusions about how a typical yeast gene differs from a typical Drosophila gene?

##3. Assembling transcripts based on rna-seq data

Rna-seq data is in general very useful in annotation projects as the data usually comes from the actual organism you study and thus avoids the danger of introducing errors caused by differences in gene structure between your study organism and other species.

The program Cufflinks can be used to assemble transcripts from mapped rna-seq reads. First the reads need to be mapped to the genome, and we prefer using the mapper Tophat2 as it belongs to the same family of programs as Cufflinks and is splice-aware. The result from Tophat2 is a BAM-file, a binary file with the coordinates of all mapped reads. We have in this practical already created such a file for you for chromosome 4 of D. melanogaster, and you can find it in course\_data/dmel/chromosome\_4/bam/.

**_Exercise 7_ - Cufflinks:**  
Load the Cufflinks module using ‘module load cufflinks/2.2.1’. By typing ‘cufflinks’ you will get a list of the parameters you can change and also see the default values for each parameter.

Then run Cufflinks on the supplied BAM-file using:  
*cufflinks -o outdir -p 8 -b /home/__login__/annotation\_course/course\_material/data/dmel/chromosome\_4/chromosome/4.fa -u /home/__login__/annotation\_course/course\_material/data/dmel/chromosome\_4/bam/accepted\_hits.chr4.bam*  

When done you can find your results in the directory ‘outdir’. The file transcripts.gtf includes your assembled transcripts.
As Webapollo doesn't like the gtf format file you should convert it in gff3 format (cf. Exercise 5). Then, transfer the gff3 file to your computer and load it into [Webapollo](http://bils-web.imbim.uu.se/drosophila_melanogaster). How well does it compare with your Augustus results? Looking at your results, are you happy with the default values of Cufflinks (which we used in this exercise) or is there something you would like to change?

##4. Checking the gene space of your assembly.

Cegma is a program that includes sequences of 248 core proteins. These proteins are conserved and should be present in all eukaryotes. Cegma will try to align these proteins to your genomic sequence and report to you the number of proteins that are successfully aligned. This percentage can be used as a measure of how complete your assembly is. 

BUSCO provides measures for quantitative assessment of genome assembly, gene set, and transcriptome completeness. Genes that make up the BUSCO sets for each major lineage are selected from orthologous groups with genes present as single-copy orthologs in at least 90% of the species. It includes 2,675 genes for arthropods, 3,023 for vertebrates, 843 for metazoans, 1,438 for fungi, 429 for eukaryotes and for bacteria 40 universal marker genes.

***Note:*** In a real-world scenario, this step should come first and foremost. Indeed, if the result is under your expectation you might be required to enhance your assembly before to go further. As running Cegma is taking a while, we have chosen to do it at the end of this practical session. You should also open a new tab and launch BUSCO. The results should be available after the lunch.  

**_Exercise 8_ - Cegma -:**  

Here you will try Cegma on Chromosome 4 of Drosophila melanogaster.First, load cegma by typing 'module load cegma'. The problem is that the file ‘4.fa’ has fasta-headers that are only numbers, and Cegma won’t accept that. Can you figure out how to change the fasta header to ‘chr4’ rather than just ‘4’ using the linux command sed? Ask the teachers if you are having problems, or cheat by using the already parsed file 4_parsed.fa. :)

*cegma -g /home/__login__/annotation\_course/course\_material/data/dmel/chromosome\_4/chromosome/4\_parsed.fa -T 8*

When done, check the output.completeness_report. How many proteins are reported as complete? Does this sound reasonable?

**_Exercise 9_ - BUSCO -:**

You will run BUSCO on chromosome 4 of Drosophila melanogaster. We will select the lineage set of arthropoda.

_module load bioinfo-tools_
_module load BUSCO_

*BUSCO -g /home/__login__/annotation\_course/course\_material/data/dmel/chromosome\_4/chromosome/4.fa -o 4\_dmel_busco -c 8 -l /sw/apps/bioinfo/BUSCO/1.1b1/lineage_sets/arthropoda*

When done, check the short\_summary\_4\_dmel\_busco. How many proteins are reported as complete? Does this sound reasonable?
