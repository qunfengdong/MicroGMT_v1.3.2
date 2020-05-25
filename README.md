# MicroGMT: A mutation tracker for SARS-CoV-2 and other microbial genome sequences

## Description
MicroGMT is a python based package, which takes either raw sequence reads or assembled genome sequence as input and compares against database sequences to identify and characterize small indels and point mutations in the microbial genoems. Although our default setting is optimized for SARS-CoV-2 virus, the package can be also applied to any other microbial genomes. <br>

## Installation
No installation required. Simply download the repository and unpack by "tar".

## Requirements
* [Python 3](https://www.python.org/). Required python packages: argparse, os, subprocess, sys, time.
* [snpEff](http://snpeff.sourceforge.net/)
* [SAMtools](http://samtools.sourceforge.net/)
* [JAVA](https://www.java.com/en/)

* If the inputs are fasta formatted assembled genomes/contigs, you will also need:<br>
&#160;1. [minimap2](https://github.com/lh3/minimap2)<br>
&#160;2. [BCFtools](https://samtools.github.io/bcftools/)

* If the inputs are fasta formatted assembled genomes/contigs, you will also need:<br>
&#160;1. [GATK/3.8-1-0-Java-1.8.0](https://gatk.broadinstitute.org/hc/en-us)
&#160;2. [picard/2.18.27-Java-1.8](https://broadinstitute.github.io/picard/)
&#160;3. [BWA/0.7.17-intel-2018b](http://bio-bwa.sourceforge.net/)

## Usage
* The main function of MicroGMT contains two steps: sequence_to_vcf.py, which aligns the input file to the reference genome and identify variants; and annotate_vcf.py, which annotates the variants and output summary tables.
* MicroGMT also provide additional utility scripts:<br>
&#160;1. combine_summary_tables.py: combine summary tables from different MicroGMT runs.<br>
&#160;2. remove_from_summary_tables.py: remove unwanted strains/IDs from the summary table.<br>
&#160;3. analysis_utilities.py: format the summary table for easy access with [R](https://www.r-project.org/), and find unique mutations.<br>
&#160;4. Find_new_seqs.sh: find new strains/IDs from a fasta formatted file of assembled sequences that are not already in existing summary tables.<br>
&#160;5. Find_regiosn_for_new_seqs.sh: extract region information from a region file for a list of strains/IDs.

## Inputs

## Outputs


## The pre-built summary tables for SARS-CoV-2
The pre-built summary tables contain mutation and region information of 29896 SARS-CoV-2 sequences downloaded from [GISAID](https://www.gisaid.org/) on May 20, 2020 (please note that the "/"s in strain IDs are replaced by "_"). 

## Usage exampels
### Running MicroGMT for fasta formatted database sequences
For SART-CoV-2:
```bash
# Step 1
python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <path_to_MicroGMT>/NC_045512_source_files/NC_045512.fa \
  -i assembly -fs <database_sequences_file> \
  -o <out_dir_1>

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file> -f both \
  -eff <path_to_snpEff>
```
For user-supplied genomes:
```bash
# Step 1
python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <fasta_reference_file> \
  -i assembly -fs <database_sequences_file> \
  -o <out_dir_1>

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file> -f both \
  -eff <path_to_snpEff>
```
### Running MicroGMT for fastq formatted raw read sequences
For SART-CoV-2:
```bash
# Step 1
cat <fastq_prefix_list_for_raw_read_files> | while read line
do
  python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <path_to_MicroGMT>/NC_045512_source_files/NC_045512.fa \
  -i fastq -fq1 ${line}_1.fq -fq2 ${line}_2.fq \
  -o <out_dir_1> \
  -gatk <path_to_gatk> \
  -picard <path_to_picard> \
  -l ${line}.log -n ${line} -ki
done

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file_for_raw_read_files> -f both \
  -eff <path_to_snpEff>
```
Looping is not required for step 1. You may do step 1 for each fastq samples one by one, and run step 2 for all of them together.

For user-supplied genomes:
```bash
# Step 1
cat <fastq_prefix_list_for_raw_read_files> | while read line
do
  python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <fasta_reference_file> \
  -i fastq -fq1 ${line}_1.fq -fq2 ${line}_2.fq \
  -o <out_dir_1> \
  -gatk <path_to_gatk> \
  -picard <path_to_picard> \
  -l ${line}.log -n ${line} -ki
done

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file_for_raw_read_files> -f both \
  -eff <path_to_snpEff>
```

### Running MicroGMT for fasta formatted contig sequences
**Warning: This option is not tested. Use at your own risk!**
For SART-CoV-2:
```bash
# Step 1
python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <path_to_MicroGMT>/NC_045512_source_files/NC_045512.fa \
  -i contig -fs <contig_sequences_file> \
  -o <out_dir_1>

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file> -f both \
  -eff <path_to_snpEff>
```
For user-supplied genomes:
```bash
# Step 1
python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <reference_fasta_file> \
  -i contig -fs <contig_sequences_file> \
  -o <out_dir_1>

# Step 2
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg <region_file> -f both \
  -eff <path_to_snpEff>
```

### Extract strain/IDs from a new fasta database sequence file to make a list and a fasta file for sequences that are not in an existing summary tables
```bash
<path_to_MicroGMT>/Find_new_seqs.sh \
	<new_database_sequences_file> <id_list_for_existing_summary_tables> \
	<name_of_new_id_list_for_sequences_in_the_new_database_sequence_file> \
	<name_of_file_containg_ids_for_sequences_not_in_existing_summary_tables> \
	<name_of_fasta_file_for_sequences_not_in_existing_summary_tables>
```

### Extract regions for above strain/IDs from a big region file
```bash
<path_to_MicroGMT>/Find_regiosn_for_new_seqs.sh \
	<name_of_file_containg_ids_for_sequences_not_in_existing_summary_tables> \
	<name_of_region_information_file_containg_ids_for_sequences_not_in_existing_summary_tables>
```

### Remove strains/IDs from summary tables
Note: Only <prefix>.all.form1.txt or <prefix>.all.form2.txt is required!<br>
Remove strains from format 1 summary tables:
```bash
python <path_to_MicroGMT>/remove_from_summary_tables.py \
  -i <prefix>.all.form1.txt -r <remove_id_list> \
  -f a -d <remove_out_dir>
```
Remove strains from format 2 summary tables:
```bash
python <path_to_MicroGMT>/remove_from_summary_tables.py \
  -i <prefix>.all.form2.txt -r <remove_id_list> \
  -f b -d <remove_out_dir>
```

### Combine summary tables
Note: Only <prefix>.all.form1.txt or <prefix>.all.form2.txt is required!<br>
Combine format 1 summary tables:
```bash
python <path_to_MicroGMT>/combine_summary_tables.py \
  -d <combine_out_dir> -f a \
  -i1 <prefix_for_input_table_1>.all.form1.txt \
  -i2 <prefix_for_input_table_2>.all.form1.txt
```
Combine format 2 summary tables:
```bash
python <path_to_MicroGMT>/combine_summary_tables.py \
  -d <combine_out_dir> -f b \
  -i1 <prefix_for_input_table_1>.all.form2.txt \
  -i2 <prefix_for_input_table_2>.all.form2.txt
```

### Downstream utilities
Note: Input one summary at a time.<br>
Reformat summary tables:
```bash
python <path_to_MicroGMT>/analysis_utilities.py \
  -i <input_summary_table> \
  -o <output_table> -t <table_format>
```
Find unqiue mutations (unqiue mutations are defined by only one strain/ID has that mutation at a specific locus):
```bash
python <path_to_MicroGMT>/analysis_utilities.py \
  -i <input_summary_table> \
  -o <output_table> -t <table_format>
```

## Tutorial
### 1. Workflow for SARS-CoV-2 sequences
#### Fasta formatted database sequences
Here we use fasta formatted database sequences downloaded from [GISAID](https://www.gisaid.org/) as an example. Suppose more strains were added to GISAID after May 20, 2020 and we want to add these strains to the pre-built summary tables.

* Inputs
Download the fasta formatted database sequences from [GISAID](https://www.gisaid.org/). It is named "sequences.fasta" in this example.<br>
Download the metadata containing region information from [GISAID](https://www.gisaid.org/). It is named "metadata.tsv" in this example.<br>
These files used strain ID as the fasta header. Since the strain IDs contain "/", we need to substitute them with something else ("_" in our example). We also need to extract region information from metadata to make the region file, and substitute blanks (" ") in the region file:
```bash
sed -i 's/\//_/g' sequences.fasta

awk -F "\t" '{print $1"\t"$6}' metadata.tsv > metadata.short.tsv
sed -i 's/\//_/g' metadata.short.tsv
sed -i 's/ /_/g' metadata.short.tsv
```

* Exclude strains/IDs that are already exist in the pre-built summary tables for the new inputs (optional)
If you have a new database fasta file and would like to compare with the existing summary tables to remove existing strains/IDs from it first, we provided utility scripts to do this job conveniently. **This is especially useful for excluding strains/IDs already exist in the pre-built summary tables for the big input database fasta file downloaded from GISAID.**<br>
All you need are the new database fasta file (sequences.fasta from last step) and the id.list file containing all the strains/IDs from the existing summary tables, which is provided along with the pre-built summary tables. For user supplied genomes, this id.list file is also produced by sequence_to_vcf.py.<br>

MicroGMT will output a list containing all strains/IDs in the new database fasta file (sequences.list in this example), a list containing strains/IDs in the new database fasta file that are not exist in the pre-built summary tables (ids_to_add.list in this example) and a new database fasta file without strains/IDs in the pre-built summary tables (ids_to_add.fasta in this example).
```bash
<path_to_MicroGMT>/Find_new_seqs.sh \
	sequences.fasta id.list \
	sequences.list ids_to_add.list \
	ids_to_add.fasta
```
You can also extract region information for these IDs from the region file (metadata.short.tsv from last step). It is not necessary, but will increase speed a little bit. You can still use the big region file (metadata.short.tsv from last step). The output region file name is ids_to_add.tsv for this example.
```bash
<path_to_MicroGMT>/Find_regiosn_for_new_seqs.sh \
	ids_to_add.list metadata.short.tsv \
	ids_to_add.tsv
```

* Make summary tables
Use files from last step to make summary tables:
```bash
python <path_to_MicroGMT>/sequence_to_vcf.py \
	-r <path_to_MicroGMT>/NC_045512_source_files/NC_045512.fa \
  -i assembly -fs ids_to_add.fasta \
	-o <make_out_dir_1>
	
python <path_to_MicroGMT>/annotate_vcf.py \
	-i <make_out_dir_1> -c -o <make_out_dir_2> \
	-rg ids_to_add.tsv -f both \
	-eff <path_to_snpEff>
```
The outputs are all the summary tables of format 1 and format 2 for ids_to_add.fasta, log files, as well as the id.list file which contains all the strain IDs in the ids_to_add.fasta file.

* Remove strains/IDs from summary tables  (optional)
We noticed that strains may be removed from the GISAID SARS-CoV-2 database. So we designed this utility script to remove unwanted strains from summary tables. You will need a list of strains/IDs that need to be removed. Here we will demostrate how to use it to remove unwanted strains from the pre-built summary tables:<br>
If you have a list of IDs in file A (sequences.list from last step), the existing summary tables (the pre-built summary tables in this example), and you want to identify unwanted strains (that is, strains in the pre-built summary tables but not in file A), you may use the following commands. **This is especially useful for excluding strains/IDs already exist in the pre-built summary tables for the big input database fasta file downloaded from GISAID.**<br>
```bash
cat <path_to_summary_tables>/id.list | while read line
do
  if grep -q "^${line}$" sequences.list
  then
    echo "Sequence ${line} won't be removed, skip."
  else
    echo ${line} >> remove.list
  fi
done
```
Remove strains from format 1 summary tables:
```bash
python <path_to_MicroGMT>/remove_from_summary_tables.py \
	-i <path_to_summary_tables>/all0520.all.form1.txt \
	-r remove.list -p removed -l removed_a \
	-f a -d <remove_out_dir>
```
Remove strains from format 2 summary tables:
```bash
python <path_to_MicroGMT>/remove_from_summary_tables.py \
	-i <path_to_summary_tables>/all0520.all.form2.txt \
	-r remove.list -p removed -l removed_b \
	-f b -d <remove_out_dir>
```

* Combine summary tables  (optional)
We will demonstrate how to combine the summary tables from "Make summary tables" and "Remove strains/IDs from summary tables" sessions above. 
Combine format 1 summary tables:
```bash
python <path_to_MicroGMT>/combine_summary_tables.py \
	-d <combine_out_dir> -f a -p combined -l combined_a \
	-i1 <remove_out_dir>/removed.all.form1.txt \
	-i2 <make_out_dir_2>/out_summary.all.form1.txt
```
Combine format 2 summary tables:
```bash
python <path_to_MicroGMT>/combine_summary_tables.py \
	-d <combine_out_dir> -f b -p combined -l combined_b \
	-i1 <remove_out_dir>/removed.all.form2.txt \
	-i2 <make_out_dir_2>/out_summary.all.form2.txt
```

* Make a new strain/ID list for use next time (optional)
We will demostrate an optional step of making a new strain/ID list for use next time (final.list in this example). This list contains all strain/IDs in the final output summary tables. Users can use it as the input list file for removing or adding strains/IDs to the new summary tables in the future.<br>
Please make sure there is no file named "final.list" in your directory before we start.
```bash
cat id.list ids_to_add.list > tmp.list
cat tmp.list | while read line
do
  if grep -q "^${line}$" remove.list
  then
    echo "Sequence ${line} is removed."
  else
    echo ${line} >> final.list
  fi
done
rm -f tmp.list
```

#### Fastq formatted raw read sequences
Here we produce summary tables for 10 strains. The fastq file prefix are in the file "ids_for_10_strains.list". The IDs in the summary tables are the fastq file prefix in this example.
```bash
cat ids_for_10_strains.list | while read line
do
  python <path_to_MicroGMT>/sequence_to_vcf.py \
  -r <path_to_MicroGMT>/NC_045512_source_files/NC_045512.fa \
  -i fastq -fq1 ${line}_1.fq -fq2 ${line}_2.fq \
  -o <out_dir_1> \
  -gatk <path_to_gatk> \
  -picard <path_to_picard> \
  -l ${line}.log -n ${line} -ki
done
	
python <path_to_MicroGMT>/annotate_vcf.py \
  -i <out_dir_1> -c -o <out_dir_2> \
  -rg 10_strains_region_file.tsv -f both \
  -eff <path_to_snpEff>
```

##### Downstream utilities
Reformat summary tables:
```bash
python <path_to_MicroGMT>/analysis_utilities.py \
  -i <input_summary_table> \
  -o <output_table> -t <table_format>
```
Find unqiue mutations (unqiue mutations are defined by only one strain/ID has that mutation at a specific locus):
```bash
python <path_to_MicroGMT>/analysis_utilities.py \
  -i <input_summary_table> \
  -o <output_table> -t <table_format>
```

### 2. Workflow for user-supplied genomes




## Arguments
### sequence_to_vcf.py
``` bash
usage: sequence_to_vcf.py [-h] -r REF_GENOME -i {assembly,contig,fastq} -o
                          OUT_DIR [-fs FASTA_SEQS] [-fq1 FASTQ1] [-fq2 FASTQ2]
                          [-fq FASTQ] [-l LOG] [-n NAME] [-gatk PATH_TO_GATK]
                          [-picard PATH_TO_PICARD] [-kb] [-ki] [-p PRIOR]
                          [-m MBQ] [-a {asm5,asm10,asm20}] [-t THREAD]

optional arguments:
  -h, --help            show this help message and exit

Mandatory inputs:
  -r REF_GENOME         Fasta formatted reference genome file
  -i {assembly,contig,fastq}
                        Type of input file
  -o OUT_DIR            Output directory

Additional arguments for inputs:
  -fs FASTA_SEQS        Fasta format assembly or contig file.
  -fq1 FASTQ1           Fastq file 1. For paired-end fastq data.
  -fq2 FASTQ2           Fastq file 2. For paired-end fastq data.
  -fq FASTQ             Fastq file. For single-end fastq data.

Optional arguments:
  -l LOG                Name of the log file [Sequence_to_vcf.log]
  -n NAME               Name of the input sample. Does not work with 'assembly' option. [test]
  -gatk PATH_TO_GATK    Absolute path to GenomeAnalysisTK.jar. Only required for 'fastq' option.
  -picard PATH_TO_PICARD
                        Absolute path to picard.jar. Only required for 'fastq' option.
  -kb                   Keep BAM files.
  -ki                   Keep index files. Only works with 'fastq' option.
  -p PRIOR              Prior for bcftools variant caller (expected substitution rate). 0 means the prior is disabled. 
                        Only works for 'assembly' or 'contig' option. [0]'.
  -m MBQ                Minimum base quality for variant caller. Only works with 'fastq' option. [10]
  -a {asm5,asm10,asm20}
                        Sequence divergence: asm5/asm10/asm20 for ~0.1/1/5 percentages. 
                        Only works with 'assembly' option. [asm5]
  -t THREAD             Number of threads. [10]
```

### annotate_vcf.py
``` bash
usage: annotate_vcf.py [-h] -i IN_DIR -o OUT_DIR [-r REFERENCE]
                       [-p TABLE_PREFIX] [-c] [-l LOG] [-f {a,b,both}]
                       [-rg REGION_FILE] [-na] [-eff PATH_TO_SNPEFF]

optional arguments:
  -h, --help           show this help message and exit

Mandatory inputs:
  -i IN_DIR            Input directory
  -o OUT_DIR           Output directory

Optional arguments:
  -r REFERENCE         Name of reference. [NC_045512]
  -p TABLE_PREFIX      Prefix of summary tables for annotated vcf files. 
                       Do not include path, except for folder name(s) inside output directory!
  -c                   Create CSV format snpEff summary files.
  -l LOG               Name of the log file [Annotate_vcf.log]
  -f {a,b,both}        Format of summary tables [a]
  -rg REGION_FILE      Name of the region file (Optional)
  -na                  Skip vcf annotation step, just make summary tables from annotated vcfs.
  -eff PATH_TO_SNPEFF  Absolute path to snpEff.jar. Required if annotae vcf files.
```

### combine_summary_tables.py
``` bash
usage: combine_summary_tables.py [-h] -i1 IN_TABLE1 -i2 IN_TABLE2 -d OUT_DIR
                                 -f {a,b} [-p OUT_PREF] [-l LOG]

optional arguments:
  -h, --help     show this help message and exit

Mandatory inputs:
  -i1 IN_TABLE1  Input summary table 1
  -i2 IN_TABLE2  Input summary table 2
  -d OUT_DIR     Output directory
  -f {a,b}       Format of summary tables

Optional arguments:
  -p OUT_PREF    Prefix of the output summary tables.
                 Do not include path, except for folder name(s) inside output directory!
  -l LOG         Name of the log file [Combine_summary_tables.log]
```

### remove_from_summary_tables.py
``` bash
usage: remove_from_summary_tables.py [-h] -i IN_TABLE -r REMOVE_LIST -d
                                     OUT_DIR -f {a,b} [-p OUT_PREF] [-l LOG]

optional arguments:
  -h, --help      show this help message and exit

Mandatory inputs:
  -i IN_TABLE     Input summary table
  -r REMOVE_LIST  List of IDs to be removed from table
  -d OUT_DIR      Output directory
  -f {a,b}        Format of summary tables

Optional arguments:
  -p OUT_PREF     Prefix of the output summary tables.
                  Do not include path, except for folder name(s) inside output directory!
  -l LOG          Name of the log file [Remove_from_summary_tables.log]
```

## Questions and bug report
Please direct all questions and bug reports to Yue Xing at: yue.july.xing@gmail.com

