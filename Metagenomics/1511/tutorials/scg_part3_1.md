---
layout: default
title:  Part 3: Single cell genome assembly using SPAdes
---

# Part 3: Single cell genome assembly

## 3.1. Organzing working folder

The following set of commands are to be typed in your compute node (for example mXX - look up using ```jobinfo -u username``` command). 
Make sure you are typing them in the compute node and not log in node. Go back to Part 1 to check how to log in to your compute node.  
Before starting the exercises, you should make a folder named *single_cell_exercises* in your home directory where the exercises will be run.
Then create 2 folders *dataset1* and *dataset2* where the raw data will be linked

```sh
mkdir ~/single_cell_exercises
cd ~/single_cell_exercises/
mkdir dataset1 dataset2
```

Next, make symbolic links of sequences in those folders:

```sh
ln -s /proj/g2015028/nobackup/single_cell_exercises/sequences/dataset1/ dataset1/
ln -s /proj/g2015028/nobackup/single_cell_exercises/sequences/dataset2/ dataset2/
```
**Please, do not modify those files**

Check that the data is present in those 2 datasets folders

```sh
ls dataset1
ls dataset2
```

You should now see 2 files per dataset, a forward fastq file ```_R1_001.fastq``` and its reverse ```_R2_001.fastq```  

Later in some commands we use the variables *sample* and *trim*, the following commands set those variables. 
We will also load the softwares we need to work, if you are interested check modules_load file for details.  
**In case you loose your connection, you will need to redo this step again.**  

#### For *Hiseq* data without trimming:
```sh
sample=Hiseq
trim=''
cd ~/single_cell_exercises/dataset1
source /proj/g2015028/nobackup/single_cell_exercises/modules_load
```

#### For *Hiseq* data with trimming:
```sh
sample=Hiseq
trim=Trimmomatic_
cd ~/single_cell_exercises/dataset1
source /proj/g2015028/nobackup/single_cell_exercises/modules_load
```

#### For *Miseq* data without trimming:
```sh
sample=Miseq
trim=''
cd ~/single_cell_exercises/dataset2
source /proj/g2015028/nobackup/single_cell_exercises/modules_load
```

#### For *Miseq* data with merging:
```trimming
sample=Miseq
trim=Trimmomatic_
cd ~/single_cell_exercises/dataset2
source /proj/g2015028/nobackup/single_cell_exercises/modules_load
```