---
title: Sen2Cor
date: 2021-07-28

authors:
- ianmcgregor
- izzihinks
- xiaojiegao
---

# Manual Batch S2 L2A sen2cor conversion

## Rationale

This is a how-to guide for walking through the process of batch-converting Sentinel 2 L1C images to L2A. The reasons for making this guide are based on conditions as of July 2021.

1. ESA SNAP software and s2toolbox have no full user guide that makes sense.
2. Main commenters on the STEP forum (SNAP) have said that using the command line function is easier than using the SNAP GUI.
3. SNAP does allow for batch processing but in forum posts asking about it, some have said that you're better off writing a loop in Python. It appears the SNAP GUI just uses dropdown boxes to create a command line argument string.
4. The Sen2Cor v2.9 user guide has inconsistencies with how the algorithm behaves.
5. As of June 2021, Sen2Cor v.2.9 is incorrectly merging SRTM DEM tiles, so there needed to be a workaround (see [here](https://forum.step.esa.int/t/question-about-geometric-relict-in-l2a-converted-images/30489)).
6. As of June 2021, there are only a few areas around the world that have their full Sentinel 2 L1C time series back-processed to L2A. ESA will not reveal what the exact parameters are that they use, however, so no one knows how to exactly replicate the official versions. Software like SentinelHub and Google Earth Engine will only make available what ESA publicly offers, meaning until ESA finishes back-processing, there will not be consistent L2A coverage back to the Sentinel 2 launch.
   - Note that the ideal situation is that this guide becomes obsolete once the full, global L2A timeseries are available on GEE.

**\*Note:** Technically SNAP has a Python interface, but here we'll be going between command line and R within HPC.\*

Author: Ian McGregor

Contributors: Xiaojie (J) Gao, Izzi Hinks, Josh Gray

# Steps for the guide

## Reminder

To do interactive HPC R session, follow these steps

- log in to HPC (yourUnityID@login.hpc.ncsu.edu) via terminal
- `cd /rsstu/users/j/jmgray2/SEAL/YourName` cd to your folder
- To run a temp session with 46 GB ram using 1 core (max is 64 GB), type in terminal:
  - -n = number of cores; -W is time requested in minutes
    - `bsub -Is -n 1 -W 180 -R "rusage[mem=46GB]" tcsh`
  - Use 1 core for 2 hours with basic RAM (16GB)
    - `bsub -Is -n 1 -W 120 tcsh`
- To use normal ram (~16 GB) on a full node with 24 cores for 1 hour, type the following
  - -x = entire node
    - `bsub -Is -n 24 -x -W 60 tcsh`
- Start R
  - `module load R`
  - `R`
- If running in conda, now load the conda env.
- To get out of the interactive session, type `exit` once you have deactivated from conda.

## Downloading

### **Sen2Cor and user guide**

The first step involves downloading the standalone Sen2Cor software. This can be done at [this website](http://step.esa.int/main/snap-supported-plugins/sen2cor/sen2cor-v2-9/). The user guides are also on this webpage toward the bottom.

- For running on HPC, you want to download the **Linux** version and have the installation be in your personal HPC folder (e.g. `/rsstu/users/j/jmgray2/SEAL/YourName/Sen2Cor-02.09.00-Linux64`). You can add the .run file to your personal HPC folder (`/rsstu/users/j/jmgray2/SEAL/YourName/Sen2Cor-02.09.00-Linux64.run`) using [SFTP](https://projects.ncsu.edu/hpc/Documents/CopyFiles.php). Next, navigate to that folder and execute `chmod +x Sen2Cor-02.09.00-Linux64.run` to make the file executable, followed by `./Sen2Cor-02.09.00-Linux64.run` to execute it. This will create the `/rsstu/users/j/jmgray2/SEAL/YourName/Sen2Cor-02.09.00-Linux64` folder.

### **L1C imagery**

Getting a large number of images from ESA can be time-consuming now that they have implemented their Long-Term Archive (LTA), which essentially means you can not batch download images at will. Instead, J found a workaround that downloads the images as they are stored on Google's servers (as used for Google Earth Engine).

**Script** = `downloadS2.R` from J in the [sealR](https://github.com/ncsuSEAL/sealR) package

**Method** = normal R session on your desktop PC

- J recommends running this from R on your lab PC because it's already connected to the HPC network, so it will be faster than a laptop.
- Alternatively, you can run the Sentinel-2 acquisition directly on HPC from any machine. To do so:
  1. From your personal HPC folder, download the zipped Google Cloud SDK: `curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-344.0.0-linux-x86_64.tar.gz`
  2. Extract the Google Cloud SDK: `tar -xf google-cloud-sdk-344.0.0-linux-x86_64.tar.gz`
  3. Initialize the Cloud SDK via the console (this prevents the command from launching a browser-based authorization flow): `gcloud init --console-only`
  4. Download the zipped gsutil from [here](https://cloud.google.com/storage/docs/gsutil_install)
     (maybe not this)
  5. Unzip gsutil with: `tar xfz gsutil.tar.gz -C /rsstu/users/j/jmgray2/SEAL/YourName`
  6. Activate your conda environment, then run: `conda install -c conda-forge gsutil`
  7. Configure gsutil: `gsutil config` and follow the instructions.
  8. Execute the DownloadSentinel2.R script from a login node: `R CMD BATCH DownloadSentinel2.R`. Downloads such as this _must_ be done from login nodes, as computes nodes do not have access to the Internet. NOTE: Jobs on login nodes are usually not allowed -- in this case, where it is necessary to do so, be sure to use less than 5 cores for your job.

### **DEM tiles**

Due to Rationale #5, we need to download the SRTM DEM manually and merge the tiles before recreating the SRTM file structure (for the official sen2cor instructions, see pg 21 of the user guide).

**Method** = Probably easier to do on PC as it is already connected to the HPC network.

1. First, download the SRTM tiles [here](https://srtm.csi.cgiar.org/srtmdata/). Because CGIAR does not show the labels on the selection map on the site, you can use this [other site](https://dwtkns.com/srtm/) to identify the specific tiles you need.
   1. Note the file structure here - SRTM tiles are each in separate zip folders that contain other metadata files.
2. Extract each of the .tif files, and merge them to one full DEM. You can do this using [gdal_merge](https://gdal.org/programs/gdal_merge.html) in terminal (only need the `-o` and input file parameters). This may take a bit depending on the number of tiles you're merging. Alternatively, use QGIS to do the merge (which also uses gdal_merge) or to check that it merged correctly: add your individual DEM tiles to an empty QGIS project, go to Raster --> Miscellaneous --> Merge, select all tiles as the input tiles, and export as a new .tif.
3. Replicate the SRTM file structure by making _N_ copies of the full DEM (where _N_ is the number of tiles you merged), and put each of them into new zip folders that have the same names as the original SRTM zip folders.
4. Move these zip folders to a common folder within your Sen2Cor installation folder.
   1. Example = `/rsstu/users/j/jmgray2/SEAL/YourName/Sen2Cor-02.09.00-Linux64/mergedDEMs`

### **ESACCI-LC add-on**

This is a land-cover classification add-on that is for recent versions of Sen2Cor, which we assume Copernicus uses.

**Method** = Recommend to do this in PC as it is already connected to the HPC network.

1. From the user guide (page 32), this can be downloaded from [here](http://maps.elie.ucl.ac.be/CCI/viewer/download.php) - note that one time it randomly was not working  on Chrome on PC, so it had to be done in Firefox.
2. After downloading, move to this folder (specified in the user guide): `Sen2Cor-02.09.00-Linux64/lib/python2.7/site-packages/sen2cor/aux_data/`
3. Unzip the .zip file within the aux_data folder ('unzip ESACCI-LC-L4-ALL-FOR-SEN2COR.zip'), and then extract the .tar files in the CCI4SEN2COR directory ('tar -xvf ESACCI-LC-L4-ALL-FOR-SEN2COR.tar' and 'tar -xvf ESACCI-LC-L4-LCCS-WB-FOR-SEN2COR.tar').
4. Move the 'ESACCI-LC-L4-Snow-Cond-500m-P13Y7D-2000-2012-v2.0' folder and 'ESACCI-LC-L4-LCCS-Map-300m-P1Y-2015-v2.0.7.tif' and 'ESACCI-LC-L4-WB-Map-150m-P13Y-2000-v4.0.tif' files to the aux_data directory.

## Update GIP parameters and parameter files

Along with its internal algorithm, Sen2Cor uses _GIP.xml_ files for different parameters. We need to update a few of these based on what we've downloaded. These files can all be found at this path: `Sen2Cor-02.09.00-Linux64/lib/python2.7/site-packages/sen2cor/cfg/`.

For a full list of parameters, see this the [SNAP](http://step.esa.int/main/snap-supported-plugins/sen2cor/sen2cor-v2-9/) website, specifically the `IODD` pdf file. For simple runs of Sen2Cor, we keep the majority of the defaults and make only slight changes:

**Method** = either PC or laptop

### **L2A_GIPP.xml**

This is where the majority of the internal parameters are set. Update the following:

- `DEM_Directory` should be set to the folder of your merged DEM zip folders, e.g. from above: `/rsstu/users/j/jmgray2/SEAL/YourName/Sen2Cor-02.09.00-Linux64/mergedDEMs/`
  - Note: Don't forget the '/' at the end of this file path!
- `DEM_Reference` should be set to the same folder (still don't know why)
- `Force_Exit_On_DEM_Error` should be set to TRUE, because you want to make sure the DEM is correctly used.

### **L2A_PB_GIPP.xml**

There are 2 ways you can set the processing baseline, either in this file or in the arguments to the Sen2Cor command itself. I believe it does not matter which one you choose.

The `Baseline_Version` default is 99.99. This should be set to 03.00 to reflect better geolocation accuracy, but the final choice can vary depending on what output you want; see [here](https://sentinel.esa.int/documents/247904/685211/Sentinel-2_L1C_Data_Quality_Report) section 3.6.

- Side-note, ESA has mentioned that the Copernicus L2A images available for download were processed with different baselines, because they are not redoing the whole archive with each new update (e.g. an image from June 2021 showed a processing baseline of 02.11 (from the metadata file `MTD_MSIL2A.xml`) despite this being 4 baseline updates behind the current). See [this document](https://sentinel.esa.int/documents/247904/685211/Sentinel-2-L2A-Data-Quality-Report.pdf/8fbbe100-19ce-4d87-acac-a68fff5f57d0?t=1625665830173) for more details.
- Note also that both documents mentioned here are updated monthly, and are available at [this location](https://sentinel.esa.int/web/sentinel/user-guides/document-library).

### **L2A_CAL_SC_GIPP.xml**

It should already be there, but make sure you have the following at the top. I tested with having these set to absolute paths instead of relative like here, but it made no difference. I assume then that Sen2Cor is correctly finding them based on our correct folder placement.

- `Snow_Map` = GlobalSnowMap.tiff
- `ESACCI_WaterBodies_Map` = ESACCI-LC-L4-WB-Map-150m-P13Y-2000-v4.0.tif
- `ESACCI_LandCover_Map` = ESACCI-LC-L4-LCCS-Map-300m-P1Y-2015-v2.0.7.tif
- `ESACCI_SnowCondition_Map_Dir` = ESACCI-LC-L4-Snow-Cond-500m-P13Y7D-2000-2012-v2.0

### Full code
Primary code
- specify file path for `--GIP_L2A` if we've changed that file (though not strictly necessary)
- specify file path for `--GIP_PB_L2A` if we've changed that file (though not strictly necessary)
- you can set `--resolution` to be "10", but note that because the default processes both the 20m and 10m bands, effectively `--resolution 10` is the same as not putting anything (for Sen2Cor v2.9).

Here is an example of code with the set parameters:
- `/rsstu/users/j/jmgray2/SEAL/yourName/Sen2Cor-02.09.00-Linux64/bin/L2A_Process
--output_dir /rsstu/users/j/jmgray2/SEAL/yourName/s2Data/L2A/46QEL
--resolution 10
--GIP_L2A /rsstu/users/j/jmgray2/SEAL/yourName/Sen2Cor-02.09.00-Linux64/lib/python2.7/site-packages/sen2cor/cfg/L2A_GIPP.xml
--GIP_L2A_PB /rsstu/users/j/jmgray2/SEAL/yourName/Sen2Cor-02.09.00-Linux64/lib/python2.7/site-packages/sen2cor/cfg/L2A_PB_GIPP.xml
--debug
/rsstu/users/j/jmgray2/SEAL/yourName/s2Data/L1C/46QEL/S2A_MSIL1C_20151116T043132_N0204_R090_T46QEL_20151116T043131.SAFE`

## Build commands and run batch conversions

Add the .R and .csh files in [this repository](https://github.com/ncsuSEAL/Sen2Cor_HPC) to your s2Data directory on HPC.

### Build the full command list

#### S2_createOrders

The `S2_createOrders.R` script and associated `S2_createOrders.csh` file will develop a list of system commands to run Sen2Cor on all of the images in your L1C directory. The code will segment this list into .Rds files, each with a max length 1,000 commands. The files are segmented into lists of max 1,000 commands due to the temporal limitations of LSF jobs on HPC; jobs in the general HPC queue can run for up to 4 days (5760 minutes), and those in the CNR queue can run for up to 10 days (14400 minutes). These .Rds files will used in the S2_runSen2Cor_batch.R and .csh files to execute these Sen2Cor commands in parallel with Rmpi.

Prior to submitting the job:
In the `S2_createOrders.R` file:
1. Replace the example base path, `/rsstu/users/j/jmgray2/SEAL/YourName`, on line 13 with the path to your personal directory.
2. If the path to the directory that contains your L1C images is not titled `/s2Data/L1C` within your personal directory, change the path in line 14 to the path to your L1C directory.
3. Adjust the path to your L2A folder on line 15, if necessary. If the L2A directory does not yet exist, it will be created at the location specified here.
4. Adjust the path to, and name of, your desired .Rds files on line 17. This is will be the prefix of the output .Rds files (for example, the prefix `all_sen2cor_commands` will result in `all_sen2cor_commands1.rds`, `all_sen2cor_commands2.rds`, etc.).
5. If your Sen2Cor v2.09 directory is not located directly in your base path, adjust the paths on lines 44, 89, and 90 to match the location of your Sen2Cor directory.
6. Check to see that your desired Sen2Cor specifications (resolution, debug, GIP_L2A, etc.) are included in the lapply function starting on line 56. Adjust these specifications as necessary.
7. Submit the job with `bsub < S2_createOrders.csh`. You should now have .Rds files with the names from step 4 in the desired output directory (your s2Data directory, by default).

### BEFORE running the batch conversion
Note that *there must be as much memory free in HPC (Josh’s whole SEAL group) as the memory taken up by the L1C files you want to process.* Otherwise, the batch conversion jobs will crash.
- Check on memory using:
  - `cd /rsstu/users/j/jmgray2/SEAL`
  - `du -h` will show memory of all directories/subdirectories in wherever you did `cd`. To only look at top-level directories there, use `du -h --max-depth=1`.

If there is not enough memory, you (and the rest of the SEAL lab) can use GLOBUS to archive data that you don't need on HPC by moving it to your (unlimited!) Google Drive.

### Do batch L2A conversions

Using the output from `S2_createOrders.R`, we are now ready to do batch conversion. Simply edit your version of the `S2_runSen2Cor_batch.R` script to be your relative folder path, and then submit `bsub < S2_runSen2Cor_batch.csh` in HPC. For details on the csh file, see below.
- As the code is running, Sen2Cor has its own output that will hide all other user-annotated progress reports. To get around this, Izzi created a way to have a text file output that will show real-time progress with tile and image labels. This will be put in the `s2Data/L2A/` directory.

**Method** = submitted HPC job.

For running test images (<=10), you can either submit a job or do an interactive R session on HPC.
- cd to your SEAL directory
- run this command `bsub -Is -n 10 -W 180 -R "rusage[mem=28GB]" tcsh`
- Either activate your conda environment and then `R`, or load R.
- Run the code in `S2_runSen2Cor_batch.R` line-by-line for your sample images. Each takes ~30 min to process.

### Notes for running the conversions on HPC
NB - the way the parameters are set in the `S2_runSen2Cor_batch.csh` are from multiple conversations with Lisa Lowe (HPC admin), who's been super helpful in figuring this out.
- Conservatively, each image takes 4 GB to process (this is technically higher than what was documented from HPC). The formula is that your max allowable memory should be 4GB * the number of cores in each node.
  - So for a CNR node of 16 cores, this means we want each core in that node to have >= 64GB (4*16). In the csh file, this is listed as `#BSUB -R "rusage[mem=64000]"`.
    - Request MB rather than GB (because the GB estimates are usually rounded, whereas MB are more precise)
  - You need to do ptile (`#BSUB -R span[ptile=N]`) if there are any nodes whose individual cores have less memory than what you are requesting
    - For example, CNR queue has 133 nodes where each node has 12 cores and each of those has 48GB memory.
    - CNR queue also has 3 nodes where each node has 8 cores and each of those has 24GB memory.
    - If you have calculated that you need 12 cores, then you must use ptile to specify how many tasks are assigned to each core because otherwise, HPC may put your tasks on one of the smaller cores, which may cause your job to fail because there’s not enough memory on the node as a whole (this is what determines failure).
      - Reminder that this happens because the `BSUB -x` means exclusive use of the node (but also, even if you don’t have exclusive use, then someone else’s tasks could be assigned and then both your jobs will crash).
  - To see the memory of all nodes, run `lshosts`.
- Technically, we have higher priority for CNR queues (and more allowable executable time [10 days, compared to 4 on non-CNR queues]), but in practice we are finding we tend to get put into non-CNR queues faster anyway.
  - To find which queues are available to you, simply type: `bqueues -u yourUnityID`
  - If you want to see all of them you can use `bqueues -u lllowe` since Lisa has access to all of them.
  - The maximum amount of time given per queue can be found using `bqueues queue-name`, e.g. `bqueues cnr`.
- Henry2 Cluster Status resource from Lisa: https://projects.ncsu.edu/hpc/Monitor/ClusterStatusMemory.php
- We asked Lisa about using the `qc` parameter at all in the `.csh` file, and her response was: _As far as specifying qc or oc...I wouldn't bother. That would narrow choices down too much. 'qc' are the oldest nodes and have 8 cores, but they usually have 16GB and so your requests wouldn't fit on them._

While jobs are going,
- to check the job status, run `bjobs -r -X -o "jobid queue cpu_used run_time avg_mem max_mem slots delimiter=','"` (from [this site](https://projects.ncsu.edu/hpc/Documents/memory.php)).
- to add more time (keeping within the queue max limit), run `bmod -W <num_minutes> <job_ID>`. Note jobID is a number.
