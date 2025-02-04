# Workshop Schedule

> **NOTE:** The *Basic Data Skills* [Shell for Bioinformatics](https://hbctraining.github.io/Shell-for-bioinformatics/) workshop is a prerequisite.


## Pre-reading:

* Please **study the contents** and **work through all the exercises** within the following lessons:
  * [Shell basics review](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/shell_review.html)
  * [Best Practices in Research Data Management (RDM)](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/04a_data_organization.html)
  * [Working in an HPC environment](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/working_on_HPC_noExercises.html)
  * [A review of high-throughput sequencing methods for understanding chromatin biology](../lessons/01a_Understanding_chromatin_with_HTS.md)
  
  
## Day 1

| Time |  Topic  | Instructor |
|:-----------:|:----------:|:--------:|
| 09:30 - 09:45 | [Workshop Introduction](../lectures/workshop_intro_slides.pdf) | Meeta |
| 09:45 - 11:00 | [Understanding chromatin biology using high-throughput sequencing](../lectures/Introduction_to_ChIP-seq_2024_small.pdf) | Dr. Shannan Ho Sui |
| 11:00- 11:05 | Break|  |
| 11:05 - 11:20 | [HPC review Q&A](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/working_on_HPC_noExercises.html) | Will |
| 11:20 - 11:50 | [Dataset overview and project organization](../lessons/02_dataset_and_project_setup.md) | Will |
| 11:50 - 12:00 | Overview of self-learning materials and homework submission | Meeta|



### Before the next class:

I. Please **study the contents** and **work through all the code** within the following lessons:
   1. [Experimental design considerations for HTS of chromatin](../lessons/01b_experimental_design_considerations.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>Before you begin thinking about performing the experiment, it is important to plan for it and choose a protocol that is best suited for you. There are many things to consider depending on the cells you are working with, and your protein of interest. <br><br>In this lesson, we will:<br>
             - Highlight the experimental design considerations for ChIP-seq and compare and contrast with CUT&RUN and ATAC-seq<br>
             - Highlight the sequencing considerations for each methods listed above<br><br>
        </details>
   

   2. [Quality Control of Sequence Data: Running FASTQC and evaluating results](../lessons/03_QC_FASTQC.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>The first step of most NGS analyses is to evaluate the quality of your sequencing reads. <br><br>In this lesson you will explore:<br>
            - The FASTQC software, and how to run it on your raw sequencing data<br>
            - The HTML report that is returned from FASTQC and how to interepret the different plots<br><br>
        </details>
        
   3. [Alignment using Bowtie2](../lessons/04_alignment_using_bowtie2.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>The next step is taking our high quality reads and figuring out where in the genome the originated from. In theory this seems like a simple task, but in practice it is quite challenging. <br><br>In this lesson you will cover:<br>
            - The Bowtie2 software, a popular tool for aligning DNA sequence reads<br>
            - Alignment file formats<br>
            - How to run your alignment as a job on the cluster<br><br>
        </details>


> **NOTE:** To run through the code above, you will need to be **logged into O2** and **working on a compute node** (i.e. your command prompt should have the word `compute` in it). **Need a refresher on the cluster?** Check out [this lesson](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/working_on_HPC_noExercises.html) from the pre-reading assignment.
> 1. Log in using `ssh rc_trainingXX@o2.hms.harvard.edu` and enter your password (replace the "XX" in the username with the number you were assigned in class). Your login information can be found [here](https://docs.google.com/spreadsheets/d/1kBlYowhjjHJC9ZovmbBULmbqozKpprM17vZ2wPlhNg0/edit#gid=0).
> 2. Once you are on the login node, use `srun --pty -p interactive -t 0-2:30 --mem 1G /bin/bash` to get on a compute node or as specified in the lesson. > 3. Proceed only once your command prompt has the word `compute` in it.
> 4. If you log out between lessons (using the `exit` command twice), please follow points 1. and 2. above to log back in and get on a compute node when you restart with the self learning.
>

2. **Complete the exercises**:
   * Each lesson above contains exercises; please go through each of them.
   * Copy over your solutions into the [Google Forms](https://forms.gle/uwqY9zzqfUEZEdQ78) the **day before the next class**.


### Questions?
* ***If you get stuck due to an error*** while runnning code in the lesson, [email us](mailto:hbctraining@hsph.harvard.edu) 

## Day 2

| Time |  Topic  | Instructor |
|:-----------:|:----------:|:--------:|
| 9:30 - 10:15 | Self-learning lessons review |  All |
| 10:15 - 11:00 | [Filtering BAM files](../lessons/05_filtering_BAM_files.md) | Will |
| 11:00 - 11:05 | Break|  |
| 11:05 - 12:00 | [Peak calling](../lessons/06_peak_calling_macs.md) | Meeta |

### Before the next class:

I. Please **study the contents** and **work through all the code** within the following lessons:
   1. [Handling peak files using `bedtools`](../lessons/07_handling_peaks_bedtools.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>Now that we have called peaks for each of our samples, it's time to look at the output. The output of MACS2 includes various files, with the narrowPeak file being the most important for interpretation. <br><br>In this lesson you will cover:<br>
             - The basics of the BED file format (and how it extends to narrowPeak files)<br>
             - The bedtools suite of tools<br>
             - Filtering and intersecting BED files <br><br>
        </details>
   
   2. [File formats for peak visualization](../lessons/08_creating_bigwig_files.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>ChIP-seq data is best evaluated by visualizing peaks. However, in order to do so we require the appropriate file formats.
         <br><br>In this lesson you will:<br>
            - Learn about different file formats for peak visualization<br>
            - Create bigWig files<br><br>
            - Discuss normalization metrics and considerations when choosing a method <br><br>
         </details>

   3. [Qualitative assessment of peak enrichment using deepTools](../lessons/09_data_visualization.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>An exciting component of ChIP-seq analysis is to be able to visualize your results, and gain some biologically meaningful insight. This may in turn generate hypothesis for you to further explore with your data!  <br><br>In this lesson you will learn:<br>
            - How to use deepTools to create heatmaps and profile plots<br>
            - To ask questions about your data and find answers through visualization<br><br>
        </details>



2. **Complete the exercises**:
   * Each lesson above contains exercises; please go through each of them.
   * Copy over your solutions into the [Google Forms](https://forms.gle/JZKs9AMmePdsoDPd8) the **day before the next class**.

### Questions?
* ***If you get stuck due to an error*** while runnning code in the lesson, [email us](mailto:hbctraining@hsph.harvard.edu) 

***

## Day 3

| Time |  Topic  | Instructor |
|:-----------:|:----------:|:--------:|
| 9:30 - 10:00 | Self-learning lessons review | All |
| 10:00 - 10:30 | [Troubleshooting your ChIP-seq analysis](../lessons/troubleshooting_chipseq_partI.md) | Meeta |
| 10:30 - 10:35 | Break|  |
| 10:35 - 11:45 | [Automating the ChIP-seq workflow](../lessons/10_automation_new.md) | Will |
| 11:45 - 12:00 | [Wrap-up](../lectures/workshop_wrapup_slides.pdf) | Meeta |


## Answer keys

* Day 1 exercises 
  * [Data Management and project organization](../homework/Day1_readme_answerkey.md)
  * [QC and Alignment questions](../homework/Day1_answer_key.md)

* Day 2 exercises 
  * [Handling peak calls](../homework/Day2_answer_key.md)

* Day 3 In-class 
  * [Automation shell script](../homework/chipseq_analysis_on_input_file.sh)
  * [Parallelization script](../homework/chipseq_run_allfiles.sh)

***

## Resources
* ENCODE Data Standards and Processing Pipeline Information for [Histone](https://www.encodeproject.org/chip-seq/histone/) and [Transcription Factors](https://www.encodeproject.org/chip-seq/transcription_factor/)
* [ENCODE guidelines and practices for ChIP-seq](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3431496/). An older paper, but a good outline of general best practices.
* Experimental design considerations:
    * [Thermofisher Step-by-step guide to a successful ChIP experiment](https://www.thermofisher.com/us/en/home/life-science/antibodies/antibodies-learning-center/antibodies-resource-library/antibody-application-notes/step-by-step-guide-successful-chip-assays.html)
    * "Chromatin Immunoprecipitation (ChIP) Principles and How to Obtain Quality Results", [BenchSci Blog](https://blog.benchsci.com/chromatin-immunoprecipitation-chip-principles-and-how-to-obtain-quality-results)
    * [O’Geen et al (2011), Methods Mol Biol](https://pubmed.ncbi.nlm.nih.gov/21913086/) - A focus on performing ChIP assays to characterize histone modifications
* [Jung et al (2014). NAR.](https://academic.oup.com/nar/article/42/9/e74/1248114) - Impact of sequencing depth in ChIP-seq experiments 



## Building on this workshop
* [Integration of ChIP-seq and RNA-seq](../lessons/integrating_rna-seq_and_chip-seq.md)
* [Advanced bash commands (aliases, copying files, and symlinks)](https://hbctraining.github.io/Intro-to-rnaseq-hpc-salmon-flipped/lessons/more_bash_cluster.html)
* [Introduction to R workshop materials](https://hbctraining.github.io/Intro-to-R-flipped/#lessons) 


***

*These materials have been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
