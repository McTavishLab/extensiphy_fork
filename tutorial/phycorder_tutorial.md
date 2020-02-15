---

title: Phycorder Tutorial

author: Jasper Toscani Field

output: html_document

---
# Phycorder tutorial

Hello!

This tutorial will walk you through installing and using **Phycorder**, a program for rapidly updating phylogenies and multiple sequence alignments.

## Description

Phycorder is a tool for updating an existing multiple sequence alignment and a phylogeny inferred from that alignment. Say you built a phylogeny for a group of bacteria during an outbreak and then received some new sequencing data that you wish to quickly incorporate into the phylogeny. Phycorder makes it convenient to do this while also ensuring you can easily do this again any time you acquire new data.

### Use cases and required files

You can use Phycorder in multiple ways. Depending on the input and output you wish to receive, you will need different data in different formats.Here are some example use cases:

##### You want an alignment with new taxa added and a tree from that alignment
You need:
* Alignment file (fasta format)
* Directory of paired-end read files
* (Optional) a tree generated from input alignment

##### You want multiple single locus files and a tree from all of those alignment files
You need:
* Multiple seperate locus multiple sequence alignment files (fasta format) **OR**
  * A single concatenated multiple sequence alignment file 
  * A CSV file illustrating locus lengths and positions
* Directory of paired-end read files
* (Optional) a tree generated from combining all the loci


### Dependencies

Unfortunately, Phycorder requires some dependencies. You know what they say about not reinventing the wheel. We'll walk through the basics of installation and adding the installed programs to your path so that Phycorder can use them.

Using Phycorder is limited to Linux at the moment. Using Ubuntu will ensure the smoothest performance. If you want to use another distro, you'll have to make sure you install analogous one-liners and all that. You have been warned.

Dependencies (Separate programs you'll need to install):

1. [Python 3](https://www.python.org/)
2. [Hisat2](https://ccb.jhu.edu/software/hisat2/index.shtml)
3. [RAxMLHPC](https://github.com/stamatak/standard-RAxML)
4. [Seqtk](https://github.com/lh3/seqtk)
5. [Samtools](http://www.htslib.org/)
6. [Bcftools](http://www.htslib.org/)
7. [Fastx](http://hannonlab.cshl.edu/fastx_toolkit/download.html)
8. [Dendropy](https://dendropy.org/)

Additionally, Phycorder comes with an additional pipeline for generating a phylogenetic tree from scratch: **Gon\_phyling**. These programs are not required for running Phycorder itself but Gon\ling can be useful if you have a lot of data and aren't interested in hand selecting the loci/genes you include in your alignment. Gon\_phyling's dependencies are as follows:

1. [PARSNP](https://harvest.readthedocs.io/en/latest/content/parsnp.html)
2. [Spades](https://github.com/ablab/spades)
3. [BBmap](https://jgi.doe.gov/data-and-tools/bbtools/bb-tools-user-guide/bbmap-guide/)
4. [RAxMLHPC](https://github.com/stamatak/standard-RAxML)


### Quick dependency install
Use conda for fastest dependency install.

Add appropriate channels to your conda install:

```bash
conda config --prepend channels conda-forge
conda config --prepend channels bioconda
```

Run this command to add the necessary dependencies to your conda environment:

```bash
conda create -n phycorder samtools hisat2 seqtk bcftools fastx-toolkit dendropy raxml
```

Activate your installation

```bash
conda activate phycorder
```

Conda install recipe on the way.



### Pathing

Phycorder will need to automatically look for these programs in your computers PATH. If you're new to the inner workings of computers, think of your PATH as a set of programs or locations on your computer that your computer automatically knows the location of. The following is a basic tutorial on adding programs to your PATH.

First you'll need to find the ```.bash_profile``` file on your computer. This file lives in your home directory when you first open a terminal. Here's how you find it:

```bash
open terminal
$ls -a
.bash_profile
other_files_in_your_home_directory

$echo $PATH
home/your_name/bin:home/your_name/other_folders/in_your/path:

$vim .bash_profile
```
At this point you'll need to add the programs you've downloaded and unpacked to your .bash_profile in the following format. Add all of this as one line, filling in the appropriate absolute pathing. If you don't see this file when using `ls -a` then you need to create it.

```bash

export PATH="/home/your_name/path/to/program:$PATH"

```

An example of what I see is:

```bash

export PATH="/home/jasper/src/SPAdes-3.13.0-Linux:$PATH"

```

Once you've added all your programs to your PATH, close the terminal window and reopen it. Then type use the `which` command to see if your computer knows where the program is located

```bash

$which hisat2

/home/your_name/hisat2_folder/hisat2

```

I installed hisat2 in a bin folder so I see:

```bash

/home/jasper/bin/hisat2

```



### Running Phycorder

Phycorder (on branch overhaul_dev) takes command line arguments to update a phylogenetic tree with new taxa sequences. Lets look at the options used by Phycorder. Phycorder use revolves around calling the 

```bash
multi_map.sh
```

command followed by flags (dashes next to a letter corresponding the command you wish to use or input).

```bash
multi_map.sh -h
```

has the following output:

```

 alignment in fasta format (-a),
 alignment type (SINGLE_LOCUS_FILES, PARSNP_XMFA or CONCAT_MSA) (-m),
 directory name to hold results (-o),
 tree in Newick format or specify NONE to perform new inference (-t),
 number of taxa to process in parallel (-p),
 number of threads per taxon being processes (-c),
 suffix (ex: R1.fasta or R2.fasta) for both sets of paired end files (-1, -2),
 directory of paired end fastq read files for all query taxa (-d),
 output format (CONCAT_MSA) (-g),
 if using single locus MSA files as input, specify the suffix (.fa, .fasta, etc) (-s),
 csv file name to keep track of individual loci when concatenated (-f),
 bootstrapping tree ON or OFF (-b)

```

Phycorder has a number of default settings for these so you will not always have to explicitly use all of these options for every run. The use of these flags depends on the input you wish to use and the output you desire to have at the end of a run.

First lets try a basic test case. Within the Phycorder folder is a folder called 

```bash
testdata
```

This folder contains a variety of files for testing your installation and use of Phycorder.

Lets use Phycorder to update a multiple sequence alignment with some new data. Run this command:

```bash

multi_map.sh -a /path/to/phycorder/testdata/combo.fas -d /path/to/phycorder/testdata

```

This command takes in an alignment file (combo.fas) and a directory containing some paired-end read files. The other flags use their default values in this case. The result of this is that phycorder will add the taxa sequences from the read files to combo.fas and will infer a new phylogenetic tree. You can examine the results by looking at these files:

```bash
~/phycorder_run/combine_and_infer/extended.aln
~/phycorder_run/combine_and_infer/RAxML_bestTree.consensusFULL
```

If the run completed successfully you'll see a full phylogenetic tree that looks like this: ![this](images/tree_image_1.png?raw=true)


We just added 3 new taxa to a starting multiple sequence alignment and inferred a tree. Lets try starting with multiple single locus files instead of a single, concatenated sequence file. Use the following commands to look at our single locus files:

```bash
$ls /testdata/single_locus_align_dir/
/testdata/single_locus_align_dir/single_locus_1_.fasta
/testdata/single_locus_align_dir/single_locus_2_.fasta
```

Both of these files contain the homologous sequence for a number of taxa. The first one is a rather big sequence of over 10,000 bases. The second one is a smaller sequence of 4 bases. Take a look at them like so:

```bash
$head -2 /testdata/single_locus_align_dir/single_locus_1_.fasta
$head -2 /testdata/single_locus_align_dir/single_locus_2_.fasta
```

You'll see that one file's sequence is indeed very large while the second file's sequence is only a few letters. This is deliberate to display a function of Phycorder when selecting which data to input. Phycorder should only be used with loci over 1,000 bases long. If taking these files are input, the smaller sequence file will be identified and removed before construction of a concatenated sequence. Lets run a new analyses.

First, remove the folder with the output we just generated

```bash
$rm -r phycorder_run
```

Now, enter the following command:

```bash
multi_map.sh -a /path/to/phycorder/testdata/single_locus_align_dir -d /path/to/phycorder/testdata -m SINGLE_LOCUS_FILES
```

This run will take the single loci MSA files, check for loci longer than the cut-off of 1,000 nucleotides and construct a concatenated alignment of those loci. A file capturing the length and positions of those loci can be found in the

```bash
loci_positions.csv
```

file. This file is a comma delimited file capturing the loci's position in the concatenated alignment, the loci's file name and the loci's length.