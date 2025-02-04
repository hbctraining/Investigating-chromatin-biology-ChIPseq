# Investigating chromatin biology using ChIP-seq and CUT&RUN 

### Learning Objectives
* Understand the necessity for, and use of, the command line interface (bash) and HPC for analyzing high-throughput sequencing data.
* Understand best practices for designing a ChIP-seq / CUT&RUN / ATAC-seq experiment.
* Perform the steps involved in going from raw FASTQ files to peak calls for an individual sample.
* Review qualitative ways to assess peak calls and if they support the hypothesis

# Installations

***All:***

* [FileZilla Client](https://filezilla-project.org/download.php?type=client) (make sure you get ‘FileZilla Client')

***Mac users:***

* Plain text editor like [Sublime text](http://www.sublimetext.com/) or similar

***Windows users:***

* [GitBash](https://git-scm.com/download/win)
* Plain text editor like [Notepad++](http://notepad-plus-plus.org/) or similar

## Notes
* These materials focus on the use of local computational resources at Harvard, which are **only accessible to Harvard affiliates**
* Non-Harvard folks can [download the data](../lessons/downloading_data.md) and set up to work on their local clusters (with the help of local system administrators)

### Instructions for Harvard researchers with access to HMS-RC's O2 cluster

To run through the code in the lessons below, you will need to be **logged into O2** and **working on a compute node** (i.e. your command prompt should have the word `compute` in it).

1. Log in using `ssh ecommonsID@o2.hms.harvard.edu` and enter your password.
2. Once you are on the login node, use `srun --pty -p interactive -t 0-2:30 --mem 1G /bin/bash` to get on a compute node _or as specified in the lesson_.
3. Proceed only once your command prompt has the word `compute` in it.
4. If you log out between lessons (using the `exit` command twice), please follow points 1. and 2. above to log back in and get on a compute node when you restart with the self learning.

## Lessons

### Part 1 
1. [Shell basics review](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/shell_review.html)
1. [Working in an HPC environment - Review](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/working_on_HPC_noExercises.html)
1. [Best Practices in Research Data Management (RDM)](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/04a_data_organization.html)
1. [Dataset overview and Project organization)](../lessons/02_dataset_and_project_setup.md)
     
***

### Part II
1. [A review of high-throughput sequencing methods for understanding chromatin biology](../lessons/01a_Understanding_chromatin_with_HTS.md)
1. [Experimental design considerations for HTS of chromatin](../lessons/01b_experimental_design_considerations.md)
1. [Quality Control of Sequence Data: Running FASTQC and evaluating results](../lessons/03_QC_FASTQC.md)
1. [Alignment using Bowtie2](../lessons/04_alignment_using_bowtie2.md)

***

### Part III 
1. [Filtering BAM files](../lessons/05_filtering_BAM_files.md)
1. [Peak calling](../lessons/06_peak_calling_macs.md)
1. [Handling peak files using `bedtools`](../lessons/07_handling_peaks_bedtools.md)

***

### Part IV
1. [File formats for peak visualization](../lessons/08_creating_bigwig_files.md)
1. [Qualitative assessment of peak enrichment using deepTools](../lessons/09_data_visualization.md)
1. [Troubleshooting your ChIP-seq analysis](../lessons/troubleshooting_chipseq_partI.md)  

***

### Part V
1. [Automating the ChIP-seq workflow](../lessons/10_automation_new.md) 

***

### Answer Keys

* [Data Management and project organization](../homework/Day1_readme_answerkey.md)
* [QC and Alignment questions](../homework/Day1_answer_key.md)
* [Handling peak calls](../homework/Day2_answer_key.md)
* [Automation shell script](../homework/chipseq_analysis_on_input_file.sh)
* [Parallelization script](../homework/chipseq_run_allfiles.sh)

***
   
## Building on this workshop
* [Integration of ChIP-seq and RNA-seq](../lessons/integrating_rna-seq_and_chip-seq.md)
* [Advanced bash commands (aliases, copying files, and symlinks)](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/more_bash_cluster.html)
* [Introduction to R workshop materials](https://hbctraining.github.io/Intro-to-R-flipped/#lessons) 

***

## Resources
* ENCODE Data Standards and Processing Pipeline Information for [Histone](https://www.encodeproject.org/chip-seq/histone/) and [Transcription Factors](https://www.encodeproject.org/chip-seq/transcription_factor/)
* [ENCODE guidelines and practices for ChIP-seq](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3431496/). An older paper, but a good outline of general best practices.
* Experimental design considerations:
    * [Thermofisher Step-by-step guide to a successful ChIP experiment](https://www.thermofisher.com/us/en/home/life-science/antibodies/antibodies-learning-center/antibodies-resource-library/antibody-application-notes/step-by-step-guide-successful-chip-assays.html)
    * "Chromatin Immunoprecipitation (ChIP) Principles and How to Obtain Quality Results", [BenchSci Blog](https://blog.benchsci.com/chromatin-immunoprecipitation-chip-principles-and-how-to-obtain-quality-results)
    * [O’Geen et al (2011), Methods Mol Biol](https://pubmed.ncbi.nlm.nih.gov/21913086/) - A focus on performing ChIP assays to characterize histone modifications
* [Jung et al (2014). NAR.](https://academic.oup.com/nar/article/42/9/e74/1248114) - Impact of sequencing depth in ChIP-seq experiments 


***
*These materials have been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
