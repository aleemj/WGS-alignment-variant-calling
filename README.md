# WGS-alignment-variant-calling
Alignment and variant calling code for WGS samples.

#last updated by Adrian on 25 July 2024

##########Latest update##########
#24 July 2024 - replaced bcftools mpileup with output bcf instead of vcf -Ou, followed by bcftools call -Ob instead of -Oz, should speed up variant calling and fix file corruption issue when reheardering
#25 July 2024 - reheadering step still does not work, remove for now and manually change sample names later
#25 July 2024 - use 24:00:00 instead of 96:00:00 to change queue, all should technically run within this time frame but have to be sure
#25 July 2024 - step 3 creates plink directory which was missing from previous version

##########UPDATE notes for Adrian##########
#what are the best CPU options?
#PBS -l select=1:ncpus=1:mem=100G
#can multithread both bwa -t and bcftools -threads but processing cost may not be worth it, no significant improvement especially in bcftools
#computing resources used = time*ncpu*select
#IN BCFTOOLS MPILEUP
#-mvOz
#IN BCFTOOLS MERGE 
#-0 --missing-to-ref
#figure out how to change sample names in individual bcf file
#IN ALL PLINK CODES MUST INCLUDE!
#-keep-allele-order
#need to Use plink to assign unique names to each genetic variants​!!!
#other plink encoding does not seem to be necesary for now
#check for:
#contig names need to change?, technically no, otherwise: sed -i -e 's/ENA|CABPTN010000001|CABPTN010000001.1/C1/g' finalbed.bim​ #to this measure
#plink can accept "_"? supposedly, otherwise: sed -i -e 's/_/-/g' finalvcf​
#average of about ~5 hours per sample based on NSCC timing? why does it take more time in reality?
#what is the processing quota used for each sample? 5 hours given time*ncpu*select

##########READ ME!##########

#the following code contains 3 steps. Step 1 should be run once first, step 2 continuosly over the course until all samples are created. Then Step 3 once.
#before running this code, one should already have a sub-folder (raw) in the alignment folder containning many other subfolders - one for every sample containing the raw reads
#example folder hierachy:
#Arugula_alignment_20240722
    #Raw
        #A10_22_4_1
#running these steps will create all subdirectories for intermediate file storage, and store files accordingly
#the final output will be genotype files ready for analysis depending on filtering applied
#please edit directories accordingly

#STEP 1
#creates respective directories, indexes reference if required and writes sh files for every sample until individual indexed vcf files.
#IMPT - edit PARENT_DIR and REFERENCE_GENOME
#last updated 22072025

#STEP 2
#runs as many sample sh files as the system allows, will check for already completed vcf files before running remaining sh files. This line of code can be run as many times over the course of a few days until all samples are created.
#IMPT - edit SH_DIR and VCF_DIR directories
#last updated 22072024

#STEP 3 
#merge all the individual, indexed vcf files. Then it should filter accordingly and convert necesary file format using plink. Output final genotype files into a new subdirectory.
#IMPT - edit VCF_DIR and PLINK_DIR and specific where PLINK tool is located
#last updated 23072025
