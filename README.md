README for SMART2
-----------------
*Time-stamp: <2017-07-09 16:39:00 Hongbo Liu>*

Introduction
------------
**SMART2** is a newly developed tool for deep analysis of DNA methylation data detected by bisulfite sequencing platforms. This tool is focused on three main functions including **de novo identification of differentially methylated regions (DMRs) by genome segmentation, identification of DMRs from predefined regions of interest, and identification of differentially methylated CpG sites**. It is known that DNA methylation plays important roles in the regulation of cell development and differentiation. DNA methylation/unmethylation mechanisms are common in all tissue/cell. However, different cell types with the same genome have different methylomes. Recently, high-throughput sequencing combining bisulfite treatment (**Bisulfite-Seq**) have been used to generate DNA methylomes from a wide range of human tissue/cell types at a genome-wide perspective. In order to de novo identify DMRs across different biological groups, entropy-based procedures facilitated the quantification of **methylation specificity** for each CpG and the determination of the Euclidean distance and similar entropy for each pair of neighboring CpGs. Subsequently, **genome segmentation** based on these quantified parameters segments the genome into primary segments comprising CpG sites with high methylation similarities across all groups. Further, the primary segments in close proximity and sharing similar methylation patterns were merged into larger segments of different types, including **DMRs** and **non-DMRs** which are identified based on methylation specificity and one-way ANOVA analysis. Eventually, the DMRs with specific hypo-/hypermethylation in the minority of groups, **group-specific hypomethylation marks (HypoMarks)** and **the group-specific hypermethylation marks (HyperMarks)**, are identified using a statistical method. To facilitate the mining of methylation marks across cell types and species. In addition, SMART2 also supports the identification of DMRs from pre-defined regions of interest and differentially methylated CpG sites.

Detailed information about SMART2 is available at http://fame.edbc.org/smart.

Source code of SMART2 is available at https://pypi.python.org/pypi/SMART-BS-Seq.


New Features of SMART2
----------------------
- **Provides more functions** *~~~~~~~~Identification of differentially methylated regions of interest, or differentially methylated CpG sites.*
- **Provides statistical analysis** *~~~~~~~One-way ANOVA analysis was added to identify the DMRs which is more reliable.* 
- **Supports various BS-Seq data** *~~~~~~SMART2 supports data analysis for WGBS, RRBS, and targeting bisulfite sequencing techniques including TruSeqEPIC, SureSelect and CpGiant.*
- **Supports replicate samples** *~~~~~~~~~~SMART2 supports replicates for the same group such as samples from the same clinical group.*
- **Supports missing value replacement** *~~~~SMART2 supports the replacement of missing value via the median methylation value of other available samples from the same group.*
- **Be applicable to any species** *~~~~~~~~~~~~Re-designed algorithm workflow makes SMART2 is applicable for any species.*
- **Multiprocessing speeds up analysis** *~~~~~~~Re-designed algorithm workflow makes SMART2 is more quick for huge data analysis.*

Install
-------
.. code-block:: bash

    $ pip install SMART-BS-Seq

`Detailed information can be found in the file 'INSTALL' in the distribution.`


Usage of SMART2
---------------
.. code-block:: bash

    $ SMART MethylMatrix [-t {DeNovoDMR,DMROI,DMC}] [-r REGION_OF_INTEREST]
                         [-n PROJECT_NAME] [-o OUTPUT_FOLDER] [-MR MISS_VALUE_REPLACE]
                         [-MS METHYLATION_SPECIFICITY] [-ED EUCLIDEAN_DISTANCE]
                         [-SM SIMILARITY_ENTROPY] [-CD CPG_DISTANCE] [-CN CPG_NUMBER]
                         [-SL SEGMENT_LENGTH] [-PD P_DMR] [-PM P_METHYLMARK] [-v] [-h]

If above command doesn't work, you can try one of the following solutions:

**(1) Add SMART command to system path**

.. code-block:: bash

    Linux$ export PATH=/usr/local/bin:$PATH
    MacOS$ export PATH=/Library/Frameworks/Python.framework/Versions/2.7/bin:$PATH

Then rerun SMART command as described above.

**(2) Run the source code which can be found in the installation directory**

Installation directory of SMART:

- Linux (Ubuntu 16.04): */usr/local/lib/python2.7/dist-packages/SMART/*
- macOS (Sierra 10.12): */Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages/SMART/*

.. code-block:: bash

    $ cd /usr/local/lib/python2.7/dist-packages/SMART/ 
    $ python SMART.py MethylMatrix [-t {DeNovoDMR,DMROI,DMC}] [-r REGION_OF_INTEREST]
                                   [-n PROJECT_NAME] [-o OUTPUT_FOLDER] [-MR MISS_VALUE_REPLACE]
                                   [-MS METHYLATION_SPECIFICITY] [-ED EUCLIDEAN_DISTANCE]
                                   [-SM SIMILARITY_ENTROPY] [-CD CPG_DISTANCE] [-CN CPG_NUMBER]
                                   [-SL SEGMENT_LENGTH] [-PD P_DMR] [-PM P_METHYLMARK] [-v] [-h]


Positional arguments
^^^^^^^^^^^^^^^^^^^^

**MethylMatrix**
    The input methylation file (such as /WGBS/MethylMatrix.txt) including methylation values in all samples to compare (REQUIRED). The methylation data should be arranged as a matrix in which each row represents a CpG site. The columns are tab-separated. The column names should be included in the first line, with the first three columns representing the location of CpG sites: chrome, start, end. The methylation values start from the fourth column. And the methylation value should be between 0 (unmethylated) to 1 (fully methylated). The missing values should be shown as -. The names of samples should be given as G1_1,G1_2,G2_1,G2_2,G3_1,G3_2,G3_3, in which Gi represents group i. The Methylation matrix can be build based on bed files (chrome start end betavalue) by bedtools as: bedtools unionbedg -i G1_1.bed G1_2.bed G2_1.bed G2_2.bed G3_1.bed G3_2.bed G3_3.bed -header -names G1_1 G1_2 G2_1 G2_2 G3_1 G3_2 G3_3 -filler - > MethylMatrix.txt. [Type: file]

    The example data is also available `here <http://fame.edbc.org/smart/Example_Data_for_SMART2.zip>`_.

Optional arguments
^^^^^^^^^^^^^^^^^^

**-t {DeNovoDMR,DMROI,DMC}**
    Type of project including 'DeNovoDMR','DMROI' and 'DMC'. DeNovoDMR means de novo identification of differentially methylated regions (DMRs) based on genome segmentation. DMROI means the comparison of the methylation difference in regions of interest (ROIs) across multiple groups. DMC means identification of differentially methylated CpG sites (DMCs). It should be noted DMC is time-consuming for whole-renome methylation data. [Type: string] [DEFAULT: 'DeNovoDMR']
    
**-r REGION_OF_INTEREST**
    Genome regions of interest in bed format without column names (such as /WGBS/Regions_of_interest.bed) for project type DMROI (REQUIRED only for DMROI). The regions in the file should be sorted by chromosome and then by start position (e.g., sort -k1,1 -k2,2n in.bed > in.sorted.bed). If this file is provided, SMART treat each region as a unit and compare its mean methylation across groups by methylation specificity and ANOVA analysis. DEFAULT: '' [Type: string]
    
**-n PROJECT_NAME**
    Project name, which will be used to generate output file names. DEFAULT: "SMART" [Type: string]
    
**-o OUTPUT_FOLDER** 
    The folder in which the result will be output. If specified all output files will be written to that directory. [Type: folder] [DEFAULT: the directory named using project name and current time (such as SMART20140801132559) in the current working directory]
    
**-MR MISS_VALUE_REPLACE**
    Replace the missing value with the mediate methylation value of available samples in the corresponding group. The user can control whether to replace missing value by setting this parameter from 0.01 (meaning methylation values are available in at least 1% of samples) to 1.0 (meaning methylation values are available in 100% of samples, i.e there is no missing values). [Type: float] [Range: 0.01 ~ 1.0] [DEFAULT: 0.5]
    
**-MS METHYLATION_SPECIFICITY**
    Methylation Specificity Threshold for DMC or DMR calling. This parameter can be used to identify DMC or DMR as the CpG site or region with methylation specificity which is greater than the threshold. [Type: float] [Range: 0.2 ~ 1.0] [DEFAULT: 0.5]
    
**-ED EUCLIDEAN_DISTANCE**
    Euclidean Distance Threshold for methylation similarity between neighboring CpGs which is used in genome segmentation for de novo identification of DMR. The methylation similarity between neighboring CpGs is high if the Euclidean distance is less than the threshold. [Type: float] [Range: 0.01 ~ 0.5] [DEFAULT: 0.2]
    
**-SM SIMILARITY_ENTROPY**
    Similarity Entropy Threshold for methylation similarity between neighboring CpGs which is used in genome segmentation for de novo identification of DMR. The methylation similarity between neighboring CpGs is high if similarity entropy is less than the threshold. [Type: float] [Range: 0.01 ~ 1.0] [DEFAULT: 0.6]
    
**-CD CPG_DISTANCE**
    CpG Distance Threshold for the maximal distance between neighboring CpGs which is used in genome segmentation for de novo identification of DMR. The neighboring CpGs will be merged if the distance less than this threshold. [Type: int] [Range: 1 ~ 2000] [DEFAULT: 500]
    
**-CN CPG_NUMBER**
    Segment CpG Number Threshold for the minimal number of CpGs of merged segment and de novo identified DMR. The segments/DMRs with CpG number larger than this threshold will be output for further analysis. [Type: int] [Range: > 1] [DEFAULT: 5]
    
**-SL SEGMENT_LENGTH**
    Segment Length Threshold for the minimal length of merged segment and de novo identified DMR. The segments/DMRs with a length larger than this threshold will be output for further analysis. [Type: int] [Range: > 1] [DEFAULT: 20]
    
**-PD P_DMR**
    p value of one-way analysis of variance (ANOVA) which is carried out for identification of or DMCs or DMRs across multiple groups. The segments with p value less than this threshold are identified as DMC or DMR. [Type: float] [Range: 1.0e-100 ~ 0.05] [DEFAULT: 0.05]
    
**-PM P_METHYLMARK**
    p value of one sample t-test which is carried out for identification of Methylation mark in a specific group based on the identified DMRs. The DMRs with p value less than this threshold is identified as group- specific methylation mark (Hyper methylation mark or Hypo methylation mark). [Type: float] [Range: 1.0e-100 ~ 0.05] [DEFAULT: 0.05]
    
**-v, --version**
    Show program's version number and exit
    
**-h, --help**
    Show this help message and exit

Example
-------
Example data
^^^^^^^^^^^^
The example data can be found in the directory Example under the installation directory of SMART, and is also available `here <http://fame.edbc.org/smart/Example_Data_for_SMART2.zip>`_. In this example, 10,000 CpG sites in each of human chromosomes were extracted for the test of SMART. The user can use the following command to test SMART.
It should be noted that the location of installation directory of SMART may be different in different Operating System.

- Linux (Ubuntu 16.04): */usr/local/lib/python2.7/dist-packages/SMART/*
- macOS (Sierra 10.12): */Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages/SMART/*


Example command
^^^^^^^^^^^^^^^
.. code-block:: bash

    Linux$ cd /usr/local/lib/python2.7/dist-packages/SMART/
    macOS$ cd /Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages/SMART/
    $ SMART ./Example/MethylMatrix_Test.txt -t DeNovoDMR -o ./Example/    
    $ SMART ./Example/MethylMatrix_Test.txt -t DMROI -r ./Example/CpGisland_hg19.bed -o ./Example/    
    $ SMART ./Example/MethylMatrix_Test.txt -t DMC -o ./Example/


Output Files
------------

The results for **DeNovoDMR** are given in the folder **DeNovoDMR** Folder including:

- **1_AvailableCpGs.txt** ~ *CpG sites with available methylation in all groups*
- **2_MergedSegment.bed** ~ *Merged segments based on small segments for visualization in UCSC browser*
- **3_MergedSegment_Methylation.txt** ~ *Merged segments with DNA methylation in samples and groups*
- **4_DMR_Methylation.txt** ~ *DMRs identified by SMART*
- **5_DMR_Group_Specificity.txt** ~ *Group specificity of DMRs*
- **6_DMR_Methylmark.txt** ~ *Group specific methylation marks*
- **Summary.txt** ~ *Summary of SMART analysis*

The results for **DMROI** are given in the folder **DMROI** Folder including:

- **1_AvailableCpGs.txt** ~ *CpG sites with available methylation in all groups*
- **2_ROI.bed** ~ *ROIs in bed format for visualization in UCSC browser*
- **3_ROI_Methylation.txt** ~ *ROIs and their methylation levels in samples and groups*
- **4_DMROI_Methylation.txt** ~ *Differentially methylated ROIs with methylation values*
- **5_DMROI_Group_Specificity.txt** ~ *Differentially methylated ROIs with group specificity*
- **6_DMROI_Methylmark.txt** ~ *Group specific methylation marks of DifferMethlROIs*
- **Summary.txt** ~ *Summary of SMART analysis*

The results for **DMC** are given in the folder **DifferMethlCpG** Folder including:

- **1_AvailableCpGs.txt** ~ *CpG sites with available methylation in all groups*
- **2_DifferMethlCpGs.txt** ~ *Differentially methylated CpG sites*
- **Summary.txt** ~ *Summary of SMART analysis*


Other useful links
------------------
:SMART: http://fame.edbc.org/smart/

:Forum: https://groups.google.com/forum/#!forum/smart-announcement

:QDMR:  http://fame.edbc.org/qdmr/


Citation
--------
Hongbo Liu et al. *Systematic identification and annotation of human methylation marks based on bisulfite sequencing methylomes reveals distinct roles of cell type-specific hypomethylation in the regulation of cell identity genes.* Nucleic Acids Res: 2016 ,44(1),75-94.

Contact
-------
:For any help:  you are welcome to write to Hongbo Liu (hongbo919@gmail.com) at http://cce.edbc.org/members/HongboLiu.html.

