---
layout: default
title:  'Exercise - Running Maker without ab-initio predictions'
---

# Running Maker without ab-initio predictions
## Overview

The first run of Maker will be done without ab-initio predictions. What are your expectations for the resulting gene build? In essence, we are attempting a purely evidence-based annotation, where the best protein- and est-alignments are chosen to build the most likely gene models. The purpose of an evidence-based annotation is simple. Basically, you may try to annotate an organism where no usable ab-initio model is available. The evidence-based annotation can then be used to create a set of genes on which a new model could be trained on (using e.g. Snap or Augustus). Selection of genes for training can be based on the annotation edit distance (AED score), which says something about how great the distance between a gene model and the evidence alignments is. A score of 0.0 would essentially say that the final model is in perfect agreement with the evidence.

Let's do this step-by-step:
## Prepare the input data

Create a new folder for the first Maker run:

*cd ~/practical2/*  
<i>mkdir maker\_dmel\_no\_abinitio</i>  
<i>cd maker\_dmel\_no\_abinitio</i>

If you have previously loaded genometools, you may have to unload it first since it can cause conflicts with some of Makers' dependencies.

Now link the raw computes you want to use into your folder. The files you will need are:

- repeatmasker.chr4.gff (coordinates of repeatmasked regions)

In addition, you will also need the genome sequence. Sym-link it from the the data directory created earlier:

*ln -s /path/to/chromosome\_4/chromosome/4.fa*

To finish, you will also need EST set file and a protein set file:  
* ln -s ~/annotation\_course/course\_material/data/dmel/chromosome\_4/evidence/est.chr4.fa  
* ln -s ~/annotation\_course/course\_material/data/dmel/chromosome\_4/evidence/protein.chr4.fa  

You should now have 1 raw compute, 1 EST file, 1 protein file and the genome sequence in the working directory. For Maker to use this information, we need create the three config files, as discussed above (maker -CTL). You can leave the two files controlling external software behavious untouched. In the actual maker options file, we need to provide:

- name of the genome sequence (genome=)
- name of the 'EST' set file(s) (est=)
- name of the 'Protein' set file(s) (protein=)
- name of the repeatmasker and repeatrunner files (rm_gff=) 

You can list multiple files in one field by separating their names by a **comma** ','.

Why are we not providing you with pre-computed protein alignments? Well, since we want Maker to build gene models off of protein evidence, we actually need more than just the coordinates of protein alignments. First of all, Maker can't be sure how the protein alignments were generated - maybe they weren't done in a splice-aware manner. That, together with some additional data that can be generated when actually aligning proteins for real, is the reason why building gene models from GFF protein\_matches doesn't work.

This time, we do not specify a reference species to be used by augustus, which will disable ab-initio gene finding. Instead we set:

  <i>protein2genome=1</i>  
  <i>est2genome=1</i>

This will enable gene building directly from the evidence alignments.
## Run Maker

If your maker\_opts.ctl is configured correctly, you should be able to run maker as you did before:

*maker -c 8*

This will take a little while and process a lot of output to the screen. Luckily, much of the heavy lifting - such as EST alignments - are already done, so the total running time is quite manageable, even on a small number of cores.
## Compile the output

Once Maker is finished, compile the annotation:

*~/annotation\_course/course\_material/git/GAAS/annotation/Tools/Maker/maker\_merge\_outputs\_from\_datastore.pl --output maker\_no\_abinitio*

We have specified a name for the output directory - unlike before - since we will be creating more than one annotation and need to be able to tell them apart.  

This should create a "maker\_no\_abinitio" directory containing a maker annotation file together with the matching protein predictions file and a sub-directory containing different annotation files including the **maker.gff** which is the result to keep from this analysis. 

=> You could sym-link the maker.gff file to another folder called e.g. dmel\_results, so everything is in the same place in the end. Just make sure to call the link something other than maker.gff, since any maker output will be called that.
