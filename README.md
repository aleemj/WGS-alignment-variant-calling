WGS-alignment-variant-calling
Alignment and joint variant calling workflow for WGS samples.

Last updated by Adrian on 16 December 2024

##########Latest update##########

16 Dec 2024: All code have been edited for joint variant calling implementation with multi-threading.

##########UPDATE notes for Adrian##########

*multithreading implemented for all steps, BWA, bcftools and samtools (not sure if worth it yet)

    #PBS -l select=1:ncpus=4:mem=100G

*IN BCFTOOLS MPILEUP use: -mvOz

*IF BCFTOOLS MERGE use: -0 --missing-to-ref

figure out how to change sample names in individual bcf file!

*IN ALL PLINK CODES MUST INCLUDE!: -keep-allele-order

*need to Use plink to assign unique names to each genetic variants​!!!

*other plink encoding does not seem to be necesary for now

check for:

contig names need to change?, technically no, otherwise: sed -i -e 's/ENA|CABPTN010000001|CABPTN010000001.1/C1/g' finalbed.bim​ #to this measure

plink can accept "_"? supposedly, otherwise: sed -i -e 's/_/-/g' finalvcf​

##########READ ME!##########

the following code contains 4 steps. Step 1 should be run once first, step 2 continuosly over the course until all samples are aligned. Then Step 3 and 4 once.

before running this code, one should already have a sub-folder (raw) in the alignment folder containning many other subfolders - one for every sample containing the raw reads

example folder hierachy:

    #Arugula_alignment_20240722
        #Raw
            #A10_22_4_1
        
running these steps will create all subdirectories and store files accordingly

the final output will be genotype files ready for analysis depending on filtering applied

please edit directories accordingly

#STEP 1

Run this once to create respective directories, index reference if required and write sh files for alignment of every sample until individual indexed bam files.

#STEP 2

runs as many sample sh files as the system allows, will check for already completed bam files before running remaining sh files. This line of code can be run as many times over the course of a few days until all samples are created.

#STEP 3

Joint variant calling step for each chromosome, ensure bam files are all correctly listed. Uses the reference genome (fai file) to check for and create sh files for every chromosome. Each sh file must then be run individually. 

#STEP 4

Merge all the indexed bcf files (variant calling files for each chromosome). Then it should filter accordingly and convert necesary file format using plink. Output final genotype files into a new subdirectory.
