# Bioinformatics Introduction
## Introduction
This repository is desinged to introduce you to many of the basic concepts involved in typical Clinical Bioinformatics project. The topics covered in this repository will include:
 - Git version control
 - Conda environments for managing tools used
 - Python programming
 - Sequencing file types
 - Interacting with APIs

 This README file is designed to guide you through some of the basic concepts, but it will be up to you to actually create the code to perform the desired task. This is not designed to be a python tutorial, you will be expected to develop this understanding yourself and apply what you learn about python to this project.

 Anything you already know, you can skip through. Good luck!

 ## Project
 The aim of this project is for you to create and apply region filtering to a sequencing file.

 The file you are given is a VCF file that has been sequenced to investigate Lynch Syndrome. You will need to create a BED file specifying the genomic regions of the genes that are relevant to this condition.

 ## Background

 ### Sequencing Files
 There are many different file types involved in NGS sequencing, but below is a breakdown of the most important ones.

 ##### BCL - Binary Call File
 These files are the raw files that come off the sequencer. They contain the raw calls made by the sequencer for all of the DNA molecular clusters on the flow cell. This means short reads, both forward and reverse, for multiple patients/samples.

 ##### Fastq - Demultiplexed Raw Sequencing File
 Demultiplexed sequencing data goes into fastq files. During the pre-sequencing PCR step, molecular barcodes are added to the strands to identify the sample. These tagged moleculaes are then pooled together and added to the flow cell for sequencing. Bioinformatics can pick apart which sequence belongs to which patient by looking for the barcode that was added before they were pooled.

 The result is two fastq files per sample, one containing all the forward reads, and one containing all the reverse reads.

 FastQ files contain not only the raw read, but quality information about each base call - i.e., how confident the sequencer is that the given base is the base call.

 If you're interested in why Fastq files are not named after anything memorable like the others - its because they get their name from being in FASTA format + contain Quality information. Fasta + Quality = FastQ. Fasta files are named after the FASTA algorithm developed in the 1980s to perform rapid sequence alignment (fast-all).

 ##### SAM/BAM - Sequence Alignment Maps / Binary Alignment Maps

FastQ files can be aligned to the human genome using tools like BWA and BOWTIE2. This puts them in order of a provided reference genome. The result in a "sequence alignment map". SAM files are human-readable and therefore BIG. To mitigate this, they're compressed into binary format - BAM.

 ##### VCF - Variant Call Files

The alignment maps can then be compared to the same reference genome, and any differences can be called. These "variant calls" are stored in a VCF file. Large VCF files can be compressed to... you guessed it! - binary call files (BCF).

 ##### BED - Browser Extensible Data
 BED files are simply tab-separated genomic coordinates in a list. They can be used to filter other files, like BAM files or BCFs, to contain only sequences that we are interested in. This reduces the chance of indicental findings and simplifies downstream processes.

### NGS analysis
This is broken down into three main steps:
 - Primary analysis: the demultiplexing or BCL files. Outputs FastQ.
 - Secondary analysis: quality control steps, alignment, variant calling. Outputs VCF.
 - Tertiary analysis: variant annotation, assigning clinical relevance to called variants, filtering.

## Project Guide

### Installation
First, we need to install the project directory. Fork this repository on GitHub, and navigate to it.

Second, above this README, there is a large green button labelled ```<> Code```. Click this and copy the HTTPS URL.
  
At your terminal, navigate to a directory you're hapy for this repository to be stored in. Then run:

```
git clone [YOUR_FORKED_REPO_URL]
cd bioinfx_intro
```

You are now in the repository directory, use ```ll``` to inspect the files and note that they match those in this repository. Time to code!

### First Steps
#### Conda
You'll need to have anaconda/miniconda installed - see here for a guide to installing miniconda: https://www.anaconda.com/docs/getting-started/miniconda/install

Conda allows you to manage environments. Environments are what you'll develop in to ensure consistent execution on your project. They have controlled package versions, so future updates can't disrupt the normal functioning of your code.

Once installed, set up a new environment and install the latest version of python. For guidance, see the conda cheatsheet: https://docs.conda.io/projects/conda/en/stable/user-guide/cheatsheet.html

Activate this environment and ensure any further packages you install are installed within your conda environment. As long as you are inside an activate environment, you can simply use ```pip``` to install python packages. Try install requests to your new conda environment:
```
pip install requests
```

#### Integrated Development Environment (IDE)
With your environment set up, you're ready to start coding. This guide won't prescribe how you go about that, my preference is to use VSCode which you can install from here: https://code.visualstudio.com/download

With VSCode installed, you can open your repository by typing ```code .``` into your terminal whilst inside the repostory directory. Alternatively, this can be done by opening VSCode first, then opening your project.

Once inside VSCode, you can open a terminal with the top toolbar. From here you can activate your environment, create files and directories, and use git. Create your first python file by running ```touch main.py```. This should appear in your explorer to the left.

#### Git
Now we have our first file, we have something to commit to git. You can see your changes so far by running
```
git status
```

This should list all the new files and changed files. Go ahead and stage them:
```
git add main.py
```

If you check ```git status``` now, you'll see that this change in now staged. You can commit this change to the version history:
```
git commit -m "insert a meaningful message that describes the changes applied in this commit"
```

This new file is now part of the version history of this local repository. However, this has not changed anything on our remote repository - the one on GitHub. Do do this, we "push" the changes:
```
git push
```

The new file will now appear in GitHub. Ideally, we don't want to be committing directly to the main branch of a repository. Create a branch in GitHub (branches > New branch). We've now made a change remotely, that isn't reflected locally.

Back on the terminal, we can "fetch" an updated remote repository:
```
git fetch
```

This will read that there is a new branch. You can move into it:
```
git checkout new_branch_name
```

For sanity, we can also "pull" any other changes that have been made remotely:
```
git pull
```

However, in this instance, it will simply say you're up to date.

There's lots more you can do with git, but these are some of the basic concepts you'll want to get used to!

#### Python
You're now ready to code, this part is up to you. Do it however you want to.

The sequencing data provided contains colorectal panel sequencing data. We only want sequencing data for Lynch Syndrome - that should be 4 or 5 genes.

To get the genes and the genomic regions for these genes, please use the PanelApp API: https://panelapp.genomicsengland.co.uk/api/docs/

You can use the requests package installed earlier to interact with APIs.

You can use the R number from the National Genomic Test Directory: https://www.england.nhs.uk/publication/national-genomic-test-directories/

Once you have the genomic locations from the PanelApp API, you can use them to create a BED file.

#### Test your output
If you've successfully created a BED file, you can test it by filtering the colorectal VCF file.

Create a new conda environment, activate it, and install bcftools to it - ```conda install -c conda-forge -c bioconda bcftools```

Use BCFtools to filter the vcf files using the bed file you created, BCFTools documentation here: https://samtools.github.io/bcftools/howtos/filtering.html

## Troubleshooting
For now - ask Niall.