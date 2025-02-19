---
title: Getting started with NCSU's HPC
date: 2022-04-25

authors:
- ianmcgregor
- izzihinks
- xiaojiegao
- owensmith
- jennaabrahamson
---

<!-- vim-markdown-toc GFM -->

* [Basic HPC functionality](#basic-hpc-functionality)
    * [Logging in to HPC](#logging-in-to-hpc)
    * [General HPC policy](#general-hpc-policy)
    * [What is HPC?](#what-is-hpc)
        * [So wait, I just try to find the highest memory nodes to get the fastest output right?](#so-wait-i-just-try-to-find-the-highest-memory-nodes-to-get-the-fastest-output-right)
    * [Your folders within HPC](#your-folders-within-hpc)
        * [Changing permissions](#changing-permissions)
    * [Mount the HPC group folder to local computer](#mount-the-hpc-group-folder-to-local-computer)
        * [For office computers (Windows)](#for-office-computers-windows)
        * [For personal computers](#for-personal-computers)
    * [Basic commands](#basic-commands)
    * [Using R in HPC](#using-r-in-hpc)
    * [HPC Jobs](#hpc-jobs)
        * [Serial Jobs](#serial-jobs)
        * [Specifying a job's parameters](#specifying-a-jobs-parameters)
        * [Parallel Jobs](#parallel-jobs)
            * [Caution](#caution)
        * [Using Rmpi (parallel, distributed memory).](#using-rmpi-parallel-distributed-memory)
        * [How to choose a queue / node / core / memory amount?](#how-to-choose-a-queue--node--core--memory-amount)
            * [Tip](#tip)
        * [Submitting a Job](#submitting-a-job)
        * [After submitting a job](#after-submitting-a-job)
        * [Automating the submission of multiple jobs](#automating-the-submission-of-multiple-jobs)
    * [HPC Shell](#hpc-shell)
    * [HPC Support](#hpc-support)

<!-- vim-markdown-toc -->


# Basic HPC functionality
While all of the information here is taken from NCSU's [webpage](https://projects.ncsu.edu/hpc/main.php/Documents/BladeCenter/GettingStartedbc.php), this can be a little bit more readable. However, their site is still helpful so be sure to check that out if you want!


## Logging in to HPC
First, open a terminal and type the following command: `ssh -Y <unityID>@login.hpc.ncsu.edu`. You will be prompted to enter your NCSU password and do 2FA.
(the ssh stands for Secure SHell, and the -Y means that you trust HPC with security stuff)

Congrats! You're logged in.

## General HPC policy
- Do not run on login node.
- Do make efficient use of resources.
- Use common sense.

## What is HPC?
HPC stands for high performance computing. It is a collection of CPUs that we can run jobs on, and is split into a hierarchical structure.
- Queue > Host > node / memory > cores
- There are different queues we have access to, and each queue has a certain number of nodes, which themselves have a certain number of cores and certain amount of memory per core. For example, one way of seeing information on the CNR queue shows `16 cores 64 GB  71 nodes`. This means there are 71 nodes that each have a memory of 64 GB that can be allocated over 16 cores (per node).
- You can see all the hosts / nodes you have access to on the general HPC cluster using `lshosts` once you log in.
  - Alternatively you can check out the HPC website [here](https://projects.ncsu.edu/hpc/Monitor/ClusterStatusMemory.php), but note it can be misleading. For example, there are a ton of nodes that have 127 GB and look to be available, but if you submit a job to those you may still find it pending for a while. This is because many of those are owned by Engineering. Unfortunately the website is not forthcoming about this.
- Similarly, you can see all the queues you have access to using `bqueues -u <unityID>`. If you want to see the default queues available, simply type `bqueues`.

What about the CNR queue? As students in the College of Natural Resources (CNR), we have exclusive access to this queue, which is helpful for 2 main reasons:
- In general, there's less competition for resources, so your job is more likely to start right away.
- We have a max run time of 10 days as opposed to 4 days in the general HPC cluster.
- That being said, we can't see the cores per node on the CNR queue as easily as we can general HPC. Instead, we can use `bqcmem cnr` but **WARNING** please use this sparingly if at all and **DO NOT SHARE**. This is a command used by HPC staff and we are not supposed to know what it is.
  - Instead, here is the output of that command as of Sep. 2021. Remember, the memory is per node, which is split over the number of associated cores.

|cores|memory| N nodes|
|---|---|---|
|-  cores |1 GB  |  24 nodes
|8  cores |16 GB |  114 nodes
|8  cores |48 GB |  3 nodes
|8  cores |24 GB |  220 nodes
|12 cores |24 GB | 7 nodes
|12 cores |48 GB | 133 nodes
|12 cores |36 GB | 27 nodes
|16 cores |128 GB|  8 nodes
|16 cores |72 GB | 54 nodes
|16 cores |56 GB | 1 nodes
|16 cores |64 GB | 71 nodes
|20 cores |128 GB| 40 nodes
|20 cores |512 GB| 1 nodes
|20 cores |256 GB| 1 nodes

### So wait, I just try to find the highest memory nodes to get the fastest output right?
Yeah you'd think that right? Unfortunately the different CPUs housing the nodes are varied - some are older (slower) and some are newer (faster). Exactly which ones are older/newer is information that is not fully shared, otherwise users would only ever request those CPUs. So for example, while we have access to the CNR queue, which is great, we've been told that (as of Aug 2021) a number of CPUs there are actually older than the ones in the general HPC cluster. So...it's a bargain.
- **NB** we do have some workaround to this: please see **Tips** under the Heading **How to choose a queue / node / core / memory amount?**
- We can also check out [this website](https://projects.ncsu.edu/hpc/Monitor/hpc_status.php) if you're not asking for exclusive access to a node, and you can see how burdened each one is (select different rectangles). As with the bqc command above, please **DO NOT SHARE** as this site is not for public use.

## Your folders within HPC
You have a number of folders in HPC.
- Home - 1 GB, backup monthly: `cd /home/[unityID]` or `cd $HOME`
- Scratch - 10 TB, you can use this to run your code, clean monthly: `cd /share/[group_name]`
- Group directory for installing software: `cd /usr/local/usrapps/jmgray2`
- SEAL directory: `cd /rsstu/users/j/jmgray2/SEAL`
  - To create a new directory in the SEAL directory, type `mkdir /rsstu/users/j/jmgray2/SEAL/[NewDirectoryName]`
- As of July 24, 2021, the SEAL directory has a max storage size of 40TB. If you have data that you’re not using, you can transfer your files to Google Drive using GLOBUS (ask Josh about this).

For further storage requirements,
- \*Mass storage (need request from OIT): `cd /ncsu/volume1/[group_name, e.g. jmgray2]`
- \*Other storage - unlimited, maybe only can be created from PI: `cd /rsstu/users/[first_letter_of_group]/[group_name]`

### Changing permissions
In general, files and folders created by you are only writeable by *you*. That means if you want others to have the ability to test your code or use your files, you need to give them permission (by default they only have read access).

To change this, there are two ways on Mac, and both of these (as well as the content below) are explained well in [this site](https://www.macinstruct.com/node/415). For Windows, this is a little bit trickier but the end goal is to have chmod = 777. Regardless, permissions need to be changed while you are logged in to HPC.
1. Navigate to the folder that contains the file permission you want to change, e.g. `cd /rsstu/users/j/jmgray2/SEAL/aFolderName`
2. Type `ls -l` [lower-case L] without quotes and press return. This will show you a list of all the files in the folder and their permissions (see website linked above).
3. To change the permission to give read-write access to everyone for a file or folder, enter `chmod 777 yourFileName.csv`, or `chmod -R 777 /Path/To/Your/Folder` (the -R stands for recursive, meaning it will apply the read-write access to all files and subdirectories within the specified directory)
4. You can make sure this worked by typing `ls -l` again.



## Mount the HPC group folder to local computer

### For office computers (Windows)
Your Windows computers in the CGA offices can mount our HPC group folder to the file explorer so that it's easier to transfer files.

To mount the folder, first check the [OIT Research Storage guide](https://oitrsvprd.oit.ncsu.edu/docs/storage/access/mount/windows/). Note that for our group folder, the `folder path` should be `\\oitrspprd.hpc.ncsu.edu\rsstu\users\j\jmgray2\SEAL`.

If you encounter a problem, use the `diagnose` button of the `Map network drive` window. If the diagnose window says something like: "the file or folder is detected, but does not respond", you probably have an access permission issue. Try the following steps:

1. Find the `OIT Research Sevices Portal` button in the top right corner of [the website](https://oitrsvprd.oit.ncsu.edu/docs/storage/access/mount/windows/), use it to login with your Unity ID.
2. Under the `Computer Access` tab, you should find your computer name. Grant access for your computer, and remount again, it should then work. If you don't find your computer name, click the `Add Computer` button to add it manually.

### For personal computers
The steps are basically the same, but to use your personal computers, you have to use [Cisco VPN](https://oit.ncsu.edu/campus-it/campus-data-network/vpn/) to get access to the computing resources from off campus. After installing the VPN, login with your Unity ID. Then, if on a Mac, open Finder and press `cmd + k`, in the server address box, type `smb://oitrspprd.hpc.ncsu.edu/rsstu/users/j/jmgray2/SEAL`; if on a Windows, the steps are same as on the office computers.



## Basic commands
These are just a small sample of basic commands, but for other linux (terminal) commands to use in HPC such as moving a file to another location, creating a directory, etc, please see these websites ([here](https://www.educative.io/blog/bash-shell-command-cheat-sheet) and [here](https://dev.to/awwsmm/101-bash-commands-and-tips-for-beginners-to-experts-30je)).

- Change your directory (hence, cd) to the desired directory: `cd /rsstu/users/j/jmgray2/SEAL/<yourFolderName>`
- To go one directory above your current directory: `cd ..`
- See all items in directory and permissions: `ls -l`
- See the number of items in a directory: `ls | wc -l`
- Calculate size of a directory: `du -h --max-depth=1` (note that `--max-depth=1` hides subdirectory contents when printing output)
- To look at a file's contents in terminal: cd to the corresponding directory, then type `less <fileName>`. Press `q` to quit.

## Using R in HPC
In RStudio you can select multiple lines of code and send them all to run one after the other. This is called "Bracketed paste" in vscode. This doesn't work on HPC, so you need to go to "Settings", "R" and uncheck the box called "Bracketed Paste". If using R via radian, however, you will need to turn this on.

The info below comes from https://projects.ncsu.edu/hpc/Software/Apps.php?app=R

There are two main ways to use R in HPC, either using HPC's version or in conda:
- To run HPC's version: Loading R is simple, and involves doing `module load R` and `R`. Now you can treat your terminal as an R terminal and do any kind of R commands that you would normally run. Note that this is base R, which means you need to load / install certain packages manually (e.g. it doesn't have data.table already). This means for submitting a job using an R script, you MUST install your packages ahead of time.
  - to do so, use install.packages(). When it prompts you for permission to create a local directory for installing the additional libraries, accept the default library location. All subsequent packages installed will be added here automatically.
  - When using multiple versions of R or multiple versions of libraries and packages, a user may create multiple libraries and specify the library locations when installing and when loading the libraries. Libraries installed under one version or compilation of R may not work with another version. To install to a library location different from the one defined in .Renviron, do `install.packages("packagename",lib="../libs/R_libs_v2")`. To load a library in a different location, do `library("packagename",lib.loc="../libs/R_libs_v2")`. If your library demands more storage than your home directory allows, visit [this resource](https://projects.ncsu.edu/hpc/Software/workflows/r.php).
- To run R with conda: please see [here](https://github.com/ncsuSEAL/sealHPChelp/blob/main/condaHPC.md).

Bear in mind that only the login node in HPC have internet access, so you should use login node to install any R packages. But, do not run heavy computing scripts on the login node.

If you want to test code scripts on HPC before you submit a job or when you find something wrong with your submitted jobs, you can use HPC's interactive sessions to debug your code. Also, interactive sessions could be used to run your local code scripts on HPC for the computing power. Just remember that interactive sessions are running on HPC's debug nodes, which means you have to set up the R environment, install all necessary packages, and upload your data to HPC before using interactive sessions as debug nodes don't have internet access.
1. First, cd to your folder with your R script. `cd /rsstu/users/j/jmgray2/SEAL/IanMcGregor`
2. Submit a request to do an interactive session.
    - `bsub -Is -n 1 -W 180 -R "rusage[mem=46GB]" tcsh` Max memory you can request is 46GB. Default is 16.
    - `bsub -Is -n 10 -W 60 tcsh`
    - `bsub -Is -n 24 -x -W 60 tcsh` where `-x` = entire node; `-n` = number of cores; `-W` is time requested in minutes
3. After the interactive session begins, you can load R from HPC or via conda
    - `module load R`, then `R`
    - `conda activate /usr/local/usrapps/jmgray2/<unityID>/<yourEnvironmentName>`, then `R`.
4. To quit R, type `quit()`.
5. To quit an interactive session, type `exit`.

## HPC Jobs

### Serial Jobs
### Specifying a job's parameters
Running code in HPC requires a .csh file. This is basically an instruction guide for the system to know how to allocate resources to run your code. To comment out one of these lines, add a second "#" at the beginning. The .csh file should be named and saved in the same directory as the script that you want to run.

Each .csh file has a number of parameters that you can add and remove as you need. At a minimum, you need the following:
```
#!/bin/tcsh
#BSUB -n 8              	# request 8 cores, change as needed
#BSUB -W 120            	# request 120 minutes for running. Max is 4 days (5760) for general HPC, or 10 days (14400) for CNR queue
#BSUB -J MyCoolScript     # specify job name
#BSUB -q shared_memory  	# ensure all cores are in same node
#BSUB -o stdout.%J      	# output file (%J will be replaced by job ID)
#BSUB -e stderr.%J      	# error file (%J will be replaced by job ID)
<instructions for code, for example: R CMD BATCH --vanilla script_name.R>
```

Other parameters for the .csh file can include (and see [this](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=bsub-options) for more options):
```
#BSUB -q serial_long          # if job runs longer than 24 hours, add this
#BSUB -q shared_memory  	    # ensure all cores are in same node
#BSUB -q cnr                  # specifically request the CNR queue
#BSUB -R "rusage[mem=64000]"  # memory allocation of 64 GB
#BSUB -x                      # exclusive use of node (this way you avoid sharing the node with another job, which is randomly assigned)
#BSUB -R span[hosts=1]        # ensure all cores are on same node (don't need this if using BSUB -x)
```

If you are running your code in a conda environment, then have the activation be part of your .csh file. For example,
```
#!/bin/tcsh
#BSUB -n 8              	# request 8 cores, change as needed
#BSUB -W 120            	# request 120 minutes for running, change as needed
#BSUB -J MyCoolScript     # specify job name
#BSUB -q shared_memory  	# ensure all cores are in same node
#BSUB -o stdout.%J      	# output file (%J will be replaced by job ID)
#BSUB -e stderr.%J      	# error file (%J will be replaced by job ID)
conda activate /usr/local/usrapps/jmgray2/imcgreg/env_diss
Rscript ./myScript.R
conda deactivate
```
**Note from HPC Staff:** As a lab group, we tend to have all our files and data stored at `/rsstu/users/j/jmgray2/SEAL`. If you are happy with the performance (length of time it takes to run your code), then you can stay running files out on `/rsstu`. However, if it's taking awhile you may experience better performance by copying over your LSF submit script and R script to `/share/$GROUP/$USER`, and running from that path (or some sub-directory of that path).


### Parallel Jobs
One good thing about running code in HPC is that we can use a number of cores to parallelize the code. However, this kind of parallelization is akin to taking an `lapply` over several items and having each of the items analyzed separately.

If we don't care what each task does (i.e., the output of a task isn't dependent on the output of another), then we can use the parallel package and do `parLapply` or `clusterApply` depending on what you want to do.

#### Caution
Apparently, parallel code in R doesn't play nicely in HPC. Even if you specify `#BSUB -n 16` and do `parallel::detectCores()` when creating your cluster, your code can have other ideas. For example, if you are given a node with 16 cores, then the cluster you create will indeed have 16 cores. BUT, if other functions within your R code have behind-the-scenes parallelization, then extra threads will be spawned and you can overload the node (at worst, crash the node).
- It can be super difficult to tell if your code has any behind-the-scenes stuff because, well, that's the point of a function sometimes, is that you let it go and it does its thing. There is some HPC documentation on this [here](https://projects.ncsu.edu/hpc/Documents/paralleljobs.php) but if you're really having a hard time with this, we suggest emailing them directly oit_help@ncsu.edu.
  - Note that this is *not* a problem with Rmpi.
- As described by Lisa:

```
LSF's "BSUB -n 4" only *reserves* the hotel room for a family of 4.  It hopes you don't invite a frat party instead, but it really can't stop you.  And hotel staff don't really catch it and tell you to stop unless you break the fire code or the neighbors call the cops.
```

### Using Rmpi (parallel, distributed memory).
If we want the output of one node to affect the output of another, then we need to do something different via Rmpi. Installing it is easy and can be done with Steps 1-3 from https://projects.ncsu.edu/hpc/Software/Apps.php?app=Conda-MPI, which is also spelled out below. Note that this requires a conda environment (see the [condaHPC resource](https://github.com/ncsuSEAL/sealHPChelp/blob/main/condaHPC.md)).
- `conda activate /usr/local/usrapps/jmgray2/<unityID>/<yourEnvironmentName>`
- `module load openmpi-gcc/openmpi1.8.4-gcc4.8.2`    #This version of MPI is necessary for a successful compile
- `setenv RMPI_INCLUDE /usr/local/apps/openmpi/1.8.4-gcc4.8.2/include`
- `setenv RMPI_LIBPATH /usr/local/apps/openmpi/1.8.4-gcc4.8.2/lib`
- `R`
- `install.packages("Rmpi",repos="https://mirrors.nics.utk.edu/cran/",configure.args="--with-Rmpi-libpath=$RMPI_LIBPATH --with-Rmpi-include=$RMPI_INCLUDE --with-Rmpi-type=OPENMPI")`

That should be it for installation!

Rmpi needs additional instructions in the .csh file. Here is an example with both conda environment and HPC (choose 1 or the other and delete).
```
#!/bin/tcsh
#BSUB -n 8              	# request 8 cores
#BSUB -W 120            	# request 120 minutes for running
#BSUB -J MyCoolScript     # specify job name
#BSUB -o stdout.%J      	# output file (%J will be replaced by job name)
#BSUB -e stderr.%J      	# error file (%J will be replaced by job name)

## Python environment
module load openmpi-gcc/openmpi1.8.4-gcc4.8.2
conda activate /usr/local/usrapps/jmgray2/imcgreg/env_diss
mpirun -n 1 Rscript ./myScript.R
conda deactivate

## General HPC
module load R
module load openmpi-gcc/openmpi1.8.4-gcc4.8.2
mpirun -n 1 Rscript.R
```

### How to choose a queue / node / core / memory amount?
Identifying the number of cores and time necessary for running code can be a challenge. One recommendation is to take one iteration of your task and submit that as a simple job, then look at the output of the task when it's finished (stdout file). That will show you the memory, then you can scale up from there. For example, let's say I'm converting Sentinel 2 L1C (top of atmosphere) image to L2A (bottom atmosphere), and I know that one conversion takes ~ 4GB. I would multiply by the number of cores per node I request (say, 16) to get the `rusage` in MB.

Determining the number of cores you want to request is invariably a function of how fast you want your job to run and the nodes available. Please see the top of this document under the **What is HPC?** section.

A csh file based on example above. NOTE: try to always specify your memory in MB rather than GB, as they tend to round GB (so you might actually get less memory than you requested).

```sh
#!/bin/tcsh
#BSUB -n 16                     # specify number of cores per node
#BSUB -R span[ptile=16]        # use if there are nodes whose cores have less memory than what you're requesting
                                ## the ptile number will determine the number of hosts using n/ptile
#BSUB -R "rusage[mem=64000]"    # memory allocation of 4GB (approximate memory needed for processing 1 L1C image)
                                ## multiplied by the number of cores per node requested (in MB) = 64000
#BSUB -x                        # exclusive use of node
#BSUB -W 5760                   # request 4 days for running (max for general HPC) or 10 days (14400 for CNR queue)
#BSUB -J sen2cor_batch_2        # specify job NAME
#BSUB -o stdout.%J              # output file (%J will be replaced by job name [number])
#BSUB -e stderr.%J              # error file (%J will be replaced by job name [number])
```

#### Tip
Some CPUs are newer than others, and some are faster than others. At times you could have a job submitted that takes >24 hours, but if you resubmit the exact same job will take <2 hours. How can we optimize speed and memory usage?
- Make sure you're using `#BSUB -x` so no one else is on your node when you are
- If a job seems to be going super slow, you can kill it and check the kind of node you're on by doing `lshosts | grep n2f2-4` (where n2f2-4 is the node). This gives you the number of cores that node has.
- Request `#BSUB -R select[avx2]` to ensure you are put on a newer CPU as these tend to be faster.

### Submitting a Job
To run the job in HPC, cd to the folder containing your .csh and .R files, and enter `bsub < yourfilename.csh` in terminal.

This automatically sends the job to HPC and creates a file called `<somename>.Rout` in your directory, which will show you the code's progress as it is being run by HPC. This is also where you can find out if it’s running into an error at all.
- Tip: Open this file in visual studio code; it will automatically update itself when you return to that tab.

### After submitting a job
- To check a job’s status: `bjobs`
- To see job status in detail: `bjobs -l [job_ID]`, so e.g. `bjobs -l 383639`
  - If the code has stopped (either from error for from fully finishing), it will say “No unfinished jobs found”.
- To kill a particular job: `bkill [job_ID]`
- To kill all jobs: `bkill 0`
- Change job running time: `bmod -W "[new_time_in_minutes]" [job_ID]`
  - Maximum running time limit depends on the queue used, A standard "serial" queue has limit running time of 4 days.

### Automating the submission of multiple jobs
Sometimes we need to submit multiple jobs at once. There are 3 main options, but let's go over an example to understand use-case. Let's say I have a function in R (call it "longFun", takes 1 argument which is the iteration number) that randomly chooses 90% of my data and performs an analysis. I need to run this 100 times on HPC for cross-validation, so in other words, I need to submit 100 jobs, each of which runs the same function.
- \*Note that in this example, R is being called from a conda environment.

1. Technically, HPC has some good documentation on this, which can be found [here](https://projects.ncsu.edu/hpc/Documents/lsf_scripts.php). However, it has been mentioned that this version of submitting multiple jobs (e.g. the "Rscript for multiple job submissions" section) is **not the preferred method**.

Here is an example csh for Method 1. Notice how all of my normal `#BSUB` arguments are in one line
```sh
#!/bin/csh
#
# Script:  This is a reformat of HPC's "R_loops.csh"
#
#
## To run this (and submit 100 identical jobs), type the following in terminal after cd folder in HPC
#     ./runThisFile.csh 100
#  Note that script must have execute permissions, i.e.,
#     chmod u+x R_loops.csh

conda activate /usr/local/usrapps/jmgray2/imcgreg/env_dissPareto3

# Specify number of jobs to submit
set numIter = $1

# Initialize iteration loop counter
set iter = 1

while ($iter <= $numIter)
    echo "Submitting job iter = $iter"

    bsub -n 16 -W 360 -R "rusage[mem=22000]" -x -J fold_iter=$iter -oo out_iter=$iter -eo err_iter=$iter "Rscript ./longFun.R $iter"

    @ iter++

end

conda deactivate
```

2. Use `launch`. This is an automatic batch-job submitter which is installed to HPC. The instructions can be found [here](https://github.ncsu.edu/lllowe/launch) (you need to log in with unity id) from Lisa Lowe. Also see [this google doc](https://docs.google.com/document/d/1qjtimdJh-9dR8GH7U1FDdpwYp_qR2ONARZmcFkKka4o/edit) for a walkthrough / further descriptions from Vashek.

3. Use `pynodelauncher`. This is similar to `launch` but it is a python package. See the instructions [here](https://github.com/ncsu-landscape-dynamics/pynodelauncher) from Vashek and Lisa Lowe. As of Nov 2021, this was not working when calling R scripts, so unsure what's going on.

**Note**: When using job arrays to automate submissions, the max for one array is 20,000 jobs.

## HPC Shell

Out of the box Henry uses the C-Shell (csh/tcsh), however you can specify that
your job scripts use the Bash shell by adding the following to the beginning of
your script

```bash
#!/bin/sh
# ... job commands

source /etc/profile.d/modules.sh
```



## HPC Support
If you run into issues and want to send a specific question to an HPC specialist, email oit_hpc@help.ncsu.edu. Provide as much context as possible (script and .csh file code, screenshots of error messages and output, your job ID, etc).
