---
title: "Automating the ChIP-seq workflow"
author: "Meeta Mistry, Radhika Khetani, Mary Piper, Jihe Liu, Mary Piper, Will Gammerdinger"
date: "Monday, September 27, 2021"
---

## Learning Objectives:

* Construct a workflow to automate the ChIP-seq workflow
* Create a shell script which groups a series of sequential commands into a script
* Demonstrate different approcahes for jobs submission of the workflow script to the cluster (in serial, in parallel)

## Automating the ChIP-seq analysis path from sequence reads to BAM files

Once you have optimized all the tools and parameters using a single sample (likely using an interactive session), you can write a script to run the whole workflow on all the samples in parallel.

This will ensure that you run every sample with the exact same parameters, and will enable you to keep track of all the tools and their versions. In addition, the script is like a lab notebook; in the future, you (or your colleagues) can go back and check the workflow for methods, which enables efficiency and reproducibility.

### Using "scratch space"

Before we get started, let's talk a little bit about how data are stored on O2. O2, like many clusters, has several different storage options; each of which has different amounts of space available, and is differently backed up. One filesystem is the `/n/scratch/` space. This directory has a lot of shared disk space available, but the files are not backed up and they will be deleted if left "untouched" for more than 45 days.

By design `/n/scratch/` is to be used for intermediate files that are created during any analysis. An example is in the schematic below. 

<p align="center">
<img src="../img/scratch_recommended_practice.png" width="600">
</p>

Today, we are going to learn how to use the `/n/scratch/` storage location as we work on automating our workflow ([More information about scratch space on O2](https://harvardmed.atlassian.net/wiki/spaces/O2/pages/2652045313/Scratch+Storage). We will be maintaining our data in our (backed up) home directories, but all of the output files will be in scratch space. When we are done, we can copy over only those output files that are essential.

#### Creating a folder in `/n/scratch/`

To get started let's create a folder for ourselves in `/n/scratch/` first. We can do so by running the existing script `/n/cluster/bin/scratch_create_directory.sh` from a login node.

```bash
$ sh /n/cluster/bin/scratch_create_directory.sh
```

When you press enter you will see:

```
Do you want to create a scratch directory under /n/scratch/users? [y/N]
```

Please say `y`. Next, it will display the guidelines for this folder and ask you to verify that you have read them:

```
Do you want to create a scratch directory under /n/scratch/users? [y/N]> y

By typing 'YES' I will comply with HMS RC guidelines for using Scratch.
I also confirm that I understand that files in my scratch directory WILL NOT BE BACKED UP IN ANY WAY.
I also understand that 45 DAYS after I last modify a given file or directory in my scratch directory,
it will be DELETED with NO POSSIBILITY of retrieval.

I understand HMS RC guidelines for using scratch: 
```

Please answer `Yes` or `yes` here, once you do you will get some additional information and your command prompt back.

```
I understand HMS RC guidelines for using scratch:  yes
Your scratch directory was created at /n/scratch/users/r/rc_trainingXX.
This has a limit of 25TiB of storage and 2.5 million files.
You can check your scratch quota using the quota-v2 command.
```

Great, now we all have created a work directory for ourselves in the `/n/scratch/` storage space! 

```bash
ls -l /n/scratch/users/${USER:0:1}/${USER}/
```

When we create our script, we will make sure that all of the analysis output gets saved in the `/n/scratch/users/${USER:0:1}/${USER}/` folder.

> #### A quick explanation on the use of `${USER:0:1}`
> The `users` on the scratch space are organized into subdirectories starting with the first letter of their username. `${USER:0:1}` will return the first letter of the username. Test this using `echo ${USER:0:1}` at the command line.
>
> This is syntax for creating substrings in `bash`:
>`${VARIABLE:START:LENGTH}`
>  - `VARIABLE` is the variable that you would like to create a substring of
>  - `START` is the 0-based indexing of the position to start the substring
 > - `LENGTH` is the number of characters following the starting position to include in the substring

Now that we have navigated to our `scratch` space, let's go ahead and create a directory for our analysis:

### Start an interactive session

We will be working with an interactive session with 2 cores. 

> If you have a session with fewer cores then `exit` out of your current interactive session and start a new one with `-c 2`.

```bash
$ srun --pty -p interactive -t 0-2:00 -c 2 --mem 4G /bin/bash
```

### More Flexibility with variables

We can write a shell script that will run on a specific file, but to make it more flexible and efficient we would prefer that it lets us give it an input FASTQ file when we run the script. To be able to provide an input to any shell script, we need to use **Positional Parameters**.

For example, we can refer to the components of the following command as numbered variables **within** the actual script:

```bash
# * DO NOT RUN *
sh  run_analysis.sh  input.fq  input.gtf  12
```

`${0}` => run_analysis.sh

`${1}` => input.fq

`${2}` => input.gtf

`${3}` => 12

The variables `${1}`, `${2}`, `${3}` and so on are **positional parameters** in the context of the shell script, and can be used within the script to refer to the files/number specified on the command line. Basically, the script is written with the expectation that `${1}` will be a fastq file and `${2}` will be a GTF file, and so on.

*There can be virtually unlimited numbers of inputs to a shell script, but it is wise to only have a few inputs to avoid errors and confusion when running a script that used positional parameters.*

> [This is an example of a simple script that used the concept of positional parameters and the associated variables](http://steve-parker.org/sh/eg/var3.sh.txt). You should try this script out after the class to get a better handle on positional parameters for shell scripting.

We will be using this concept in our automation script, wherein we will accept the full or relative path to a file as input.

### Writing the automation script!

We will start writing the script on our laptops using a simple text editor like Sublime Text or Notepad++. Let's being with the shebang line and a `cd` command so that our results are all written on `/n/scratch/`. 

```bash
#!/bin/bash/

# change directories to /n/scratch/ so that all the analysis is stored there.

cd /n/scratch/users/${USER:0:1}/${USER}/
```

**We want users to input the path to the FASTQ file as input to the shell script**, i.e. `sh chipseq_analysis_on_input_file.sh <name-of-fastq-file>`. To make this work, we have to replace all the places in the script where we want to refer to the FASTQ file, with the variable `$1`. 

We could just use the variable `${1}`, but that is not an intuitive variable name for a FASTQ file, is it? So we want to create a new variable called `fq` and copy the contents of `${1}` into it. 


```bash
# initialize a variable with an intuitive name to store the name of the input fastq file

fq=${1}
```

In the rest of the script, we can now call the fastq file using `${fq}` instead of `${1}`!

> When we set up variables we do not use the `$` before it, but when we *use the variable*, we always have to have the `$` before it.
>
> For example: 
>
> initializing the `fq` variable => `fq=${1}`
>
> using the `fq` variable => `fastqc ${fq}`


To ensure that all the output files from the workflow are properly named with sample IDs, we should extract the "base name" (or sample ID) from the name of the input file. There is a command called `basename` that will do that! 

We can save the output of the `basename` command and save it as a new variable called `base` that we can use downstream.

```bash
# grab base of filename for naming outputs
base=`basename ${fq} .fastq.gz`          
```

> **What is `basename`?**
>
> The `basename` command takes a path or a name, and trims away all the information **before** the last `/`. If you specify the string to clear away **at the end**, it will do that as well. 
> 
> In this case, if the variable `${fq}` contains the path *"~/chipseq_workshop/raw_data/wt_sample2_chip.fastq.gz"*, then:
>
> ```basename ${fq} .fastq.gz```
>
> Will output:
>
> ```wt_sample2_chip```
>
> ***
>
> **What do the backticks used with `basename` (``) do?**
>   
> To assign the value of the `basename` command to the `base` variable, we encapsulate the `basename ${fq} .fastq.gz` command in backticks. This syntax is necessary for assigning the output of a given command to a given variable.

Next, we'll setup our directory structure for outputs using the `-p` option. This will make sure that `mkdir` will create the directory only if it does not already exist, and it will not throw an error if it does exist.

```
# make all of the output directories
# The -p option means mkdir will create the whole path if it 
# does not exist and refrain from complaining if it does exist
mkdir -p chipseq/results/fastqc
mkdir -p chipseq/results/bowtie2
```

Now that we have already created our output directories, we can create variables using those directories. 

```
# directory with bowtie genome index
genome=/n/groups/shared_databases/bowtie2_indexes/mm10

# set up output filenames and locations
fastqc_out=chipseq/results/fastqc
bowtie_results=chipseq/results/bowtie2

## set up file names
align_sam=chipseq/results/bowtie2/${base}.sam
align_bam=chipseq/results/bowtie2/${base}.bam
align_sorted=chipseq/results/bowtie2/${base}_sorted.bam
align_final=chipseq/results/bowtie2/${base}_final.bam
```

Creating these variables makes it easier to see what is going on in a long command. For example, we can now use `align_sam` instead of `/results/bowtie2/${base}.sam`. In addition, if there is a need to change the output directory or the genome being aligned to, the change needs to be made just in one location instead of throughout the script. 

### Keeping track of tool versions

All of our variables are now staged. Next, let's make sure all the modules are loaded. This is also a good way to keep track of the versions of tools that you are using in the script:

```bash
# set up the software environment
module load fastqc/0.12.1
module load gcc/14.2.0
module load bowtie2/2.5.4
module load samtools/1.21
module load sambamba/0.7.1
```

### Preparing for future debugging

It is a good idea to use the `echo` command for debugging. `echo` prints the string of characters specified within the quotations onto the Terminal. When you strategically place `echo` commands indicating the step of the analysis, you can determine where the script failed using the last `echo` statement displayed when you are troubleshooting. For example, we could add the below `echo` command with `${base}` before we perform the FastQC analysis to indicate the step and the sample ID.

```
echo "FastQC analysis of ${base}"
```

> You can also use `set -x`:
>
> `set -x` is a debugging tool that will make bash display the command before executing it. In case of an issue with the commands in the shell script, this type of debugging lets you quickly pinpoint the step that is throwing an error. Oftentimes, tools will display the error that caused the program to stop running, so keep this in mind for times when you are running into issues where this is not available.
> You can turn this functionality off by using `set +x`

### Running the tools

Let's write up the commands to run the tools we have already tested, with a couple of modifications:
* use variable names instead of actual file names
* multithread when possible (`bowtie2`, `sambamba`)

```
# Run FastQC
fastqc -o ${fastqc_out} ${fq}

# Run bowtie2
bowtie2 -p 2 -q --local -x ${genome} -U ${fq} -S ${align_sam}

# Create BAM from SAM
samtools view -h -S -b -o ${align_bam} ${align_sam}

# Remove SAM file
rm ${align_sam}

# Sort BAM file by genomic coordinates
samtools sort ${align_bam} -o ${align_sorted} 

# Filter out multi-mappers and duplicates
sambamba view -h -t 2 -f bam -F "[XS] == null and not unmapped " ${align_sorted} > ${align_final}

# Create indices for all the bam files for visualization and QC
samtools index ${align_final}
```

### Last addition to the script

It is best practice to have the script **usage** specified at the top of any script. This usage message should have information such that when your future self (or a co-worker) uses the script, they know what it will do and what input(s) are needed. For our script, we should have the following lines of comments right at the top after `#!/bin/bash/`:

```
# This script takes a fastq file of ChIP-seq data, runs FastQC and outputs a BAM file for it that is ready for peak calling. FASTQ files are aligned against the mm10 genome using Bowtie2. The outputted BAM file **does not** contain duplicate reads and is sorted by genomic coordinates using sambamba and samtools, respectively.
# USAGE: sh chipseq_analysis_on_input_file.sh <path to the fastq file>
```

It is okay to specify this after all the commands are in the script, when you will have most clarity about the script's purpose.

Your script should now look like this:

```
#!/bin/bash/

# This script takes a fastq file of ChIP-seq data, runs FastQC and outputs a BAM file for it that is ready for peak calling. Fastq files are aligned against the mm10 genome using Bowtie2. The outputted BAM file **does not** contain duplicate reads and is sorted by genomic coordinates using sambamba and samtools, respectively.
# USAGE: sh chipseq_analysis_on_input_file.sh <path to the fastq file>

# change directories to /n/scratch/ so that all the analysis is stored there.
cd /n/scratch/users/${USER:0:1}/${USER}/

# initialize a variable with an intuitive name to store the name of the input fastq file
fq=${1}

# grab base of filename for naming outputs
base=`basename ${fq} .fastq.gz`  

# make all of the output directories
# The -p option means mkdir will create the whole path if it 
# does not exist and refrain from complaining if it does exist
mkdir -p chipseq/results/fastqc
mkdir -p chipseq/results/bowtie2

# directory with bowtie genome index
genome=/n/groups/shared_databases/bowtie2_indexes/mm10

# set up output filenames and locations
fastqc_out=chipseq/results/fastqc
bowtie_results=chipseq/results/bowtie2

## set up file names
align_sam=chipseq/results/bowtie2/${base}.sam
align_bam=chipseq/results/bowtie2/${base}.bam
align_sorted=chipseq/results/bowtie2/${base}_sorted.bam
align_final=chipseq/results/bowtie2/${base}_final.bam

# set up the software environment
module load fastqc/0.12.1
module load gcc/14.2.0
module load bowtie2/2.5.4
module load samtools/1.21
module load sambamba/0.7.1

echo "FastQC analysis of ${base}"

# Run FastQC and place the output to the appropriate folder
fastqc -o ${fastqc_out} ${fq}

echo "Bowtie2 alignment of ${base}"

# Run bowtie2
bowtie2 -p 2 -q --local -x ${genome} -U ${fq} -S ${align_sam}

echo "Convert SAM to BAM for ${base}"

# Create BAM from SAM
samtools view -h -S -b -o ${align_bam} ${align_sam}

# Remove SAM file
rm ${align_sam}

echo "Filtering ${base} BAM file"

# Sort BAM file by genomic coordinates
samtools sort ${align_bam} -o ${align_sorted} 

# Filter out duplicates
sambamba view -h -t 2 -f bam -F "[XS] == null and not unmapped " ${align_sorted} > ${align_final}

# Create indices for all the bam files for visualization and QC
samtools index ${align_final}

echo "The analysis for ${base} is done!"
```

### Saving and running script

To transfer the contents of the script from your laptop to O2, you can copy and paste the contents into a new file called `chipseq_analysis_on_input_file.sh` using `vim`. 

```bash
$ cd ~/chipseq_workshop/scripts/

$ vim chipseq_analysis_on_input_file.sh 
```
> *Alternatively, you can save the script on your computer and transfer it to `~/chipseq_workshop/scripts/` using FileZilla.*

We should all have an interactive session with 6 cores, so we can run the script as follows from the `~/chipseq_workshop/scripts/` directory:

```bash
$ sh chipseq_analysis_on_input_file.sh ~/chipseq_workshop/raw_data/wt_sample2_chip.fastq.gz
```

This script will take a while to run, given the alignment, filtering and sorting steps all take a long time to run. So we can use `CTRL` + `C` and kill the job. Since the first part of the script should have run, we can go check if the folders were appropriately created.

```bash
$ cd /n/scratch/users/${USER:0:1}/${USER}/

$ tree
```

You should see a directory structure that looks like this:

```
.
└── chipseq
    └── results
        ├── bowtie2
        └── fastqc

4 directories, 0 files
```

## Running the script to submit jobs in parallel to the SLURM scheduler

The above script will run in an interactive session **one file at a time**. But the whole point of writing this script was to run it on all files at once. How do you think we can do this?

To run the above script **"in serial"** for all of the files on a worker node via the job scheduler, we can create a separate submission script that will need 2 components:

1. **SLURM directives** at the **beginning** of the script. This is so that the scheduler knows what resources we need in order to run our job on the compute node(s).
2. a **`for`** loop that iterates through and runs the above script for all the fastq files.

Below is what this second script (`chipseq_analysis_on_allfiles.slurm`) would look like **\[DO NOT RUN THIS\]**:

```bash
# **\[DO NOT RUN THIS\]**

#!/bin/bash

#SBATCH -p short 		# partition name
#SBATCH -t 0-2:00 		# hours:minutes runlimit after which job will be killed
#SBATCH -c 2 		# number of cores requested -- this needs to be greater than or equal to the number of cores you plan to use to run your job
#SBATCH --mem=8G           # total memory requested
#SBATCH --job-name chipseq 		# Job name
#SBATCH -o %j.out			# File to which standard out will be written
#SBATCH -e %j.err 		# File to which standard error will be written

# this `for` loop, will take chip-seq fastq files as input and output filtered BAM files ready for peak calling.

for fq in ~/chipseq_workshop/raw_data/*.fastq.gz
do
  echo "running analysis on ${fq}"
  sh chipseq_analysis_on_input_file.sh ${fq}
done
```

**But we don't want to run the analysis on these 6 samples one after the other!** We want to run them "in parallel" as 6 separate jobs. 

**Note:** If you create and run the above script, or something similar to it, i.e. with SLURM directives at the top, you should give the script name `.run` or `.slurm` as the extension. This will make it obvious that it is meant to submit jobs to the SLURM scheduler. 

***
**Exercise**

How would you run `chipseq_analysis_on_allfiles.slurm`, i.e. the above script?

***

## Submitting jobs **in parallel** to the SLURM scheduler

Parallelization will save you a lot of time with real (large) datasets. To parallelize our analysis, we will still need to write a second script that will call the original script we just wrote. We will still use a `for` loop, but we will be creating a regular shell script and we will be specifying the SLURM directives a little differently as input to the `for` loop. 

Use `vim` to start a new shell script called `chipseq_run_allfiles.sh` in the `scripts` directory: 

```bash
$ cd ~/chipseq_workshop/scripts/

$ vim chipseq_run_allfiles.sh
```

Due to the space limit in our home directory, we will loop through only two `KO` samples. The command being submitted within the `for` loop is `sbatch` with SLURM directives specified on the same line (similar to what we have been doing for `srun`):

```bash
#! /bin/bash

for fq in ~/chipseq_workshop/raw_data/ko_*_chip.fastq.gz
do

sbatch -p short -t 0-2:00 -c 2 --mem=8G --job-name chipseq-analysis -o %j.out -e %j.err \
--wrap="sh ~/chipseq_workshop/scripts/chipseq_analysis_on_input_file.sh ${fq}"

sleep 1	    # wait 1 second between each job submission
  
done
```
> Please note that after the `sbatch` directives the command `sh ~/chipseq_workshop/scripts/chipseq_analysis_on_input_file.sh ${fq}` is in quotes.

```bash
$ sh chipseq_run_allfiles.sh
```

What you should see on the output of your screen would be the jobIDs that are returned from the scheduler for each of the jobs that your script submitted. 

You can use `O2sacct` to check progress. 

```bash
$ O2sacct

$ tree /n/scratch/users/${USER:0:1}/${USER}/chipseq/
```

Don't forget about the `scancel` command, should something go wrong and you need to cancel your jobs.

## Post-analysis clean up

Once your run has completed you can check the `.err` and `.out` files for each submitted job. 

Finally, you can move over any results files that you will need for peak calling and visualization (the sorted `bam` and corresponding `bai` files) to your personal home directory or to your lab group's directory. *Remember that `/n/scratch/` is not backed up and any "untouched" files will be deleted after 45 days.*


> **NOTE:** All job schedulers are similar, but not the same. Once you understand how one works, you can transition to another one without too much trouble. They all have their pros and cons which are considered by the system administrators when picking one for a given HPC environment. Some examples of other job schedulers are LSF, SGE, PBS/Torque.

---
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
