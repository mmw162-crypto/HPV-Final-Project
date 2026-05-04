# HPV-Final-Project
GOAl: Re-run a portion of the pipeline from Jung, et. al, using updated versions of Bowtie2 (v 5.4 vs v5.0) and Kracken2 (v2.17.1 vs v2.1.2) /Bracken (v3.0.1 vs v2.8) to classify the vaginal microbiome state in patients with HPV 16/18. 

## Samples used from NCBI
https://www.ncbi.nlm.nih.gov/biosample/38928164

https://www.ncbi.nlm.nih.gov/biosample/38928165

https://www.ncbi.nlm.nih.gov/biosample/38928226

https://www.ncbi.nlm.nih.gov/biosample/38928227

## Cleaning and Organizing (4/20)
- created folder under mmw162/Bioinformatics_Final_MIM
- deleted old files from bucket
## Downloading fastq files from NCBI 
log on to compute node 
`srun --pty bash`

Install SRA-tools through an environment: 
`module load anaconda3`

 `conda create -n sra_env -c bioconda sra-tools` (say yes)
 
`conda activate sra_env`
- Mkdir for fastq. Cd into fastq directory.
  
  `mkdir fastqc_final`
  
  (full path: mmw162/fastqc_final)
  
`prefetch SRR27288028`

`prefetch SRR27288027`

`prefetch SRR27287968`

`prefetch SRR27287969`

- Cd into new downloaded directory
- Change files into fastq format, then compress.
`fasterq-dump *.sra`

`gzip *.fastq`

used a slurm script to unzip files: 
`#!/bin/bash`

`#SBATCH --job-name="transfer"`

`#SBATCH --output="%x.o%j"`

`#SBATCH --nodes=1`

`#SBATCH --ntasks=1`

`#SBATCH --cpus-per-task=4`

`#SBATCH --time=5-00`

`#SBATCH --mem=10G`

`#SBATCH --mail-type=END,FAIL`

`#SBATCH --mail-user=mam840@georgetown.edu`

`# running gzip to unzip the SRR27287968 files`

`gzip /home/mam840/finalproject.MIM/SRR27287968/*.fastq`

- transferring files from personal directory to bucket:

`#!/bin/bash`

`#SBATCH --job-name="transfer"`

`#SBATCH --output="%x.o%j"`

`#SBATCH --nodes=1`

`#SBATCH --ntasks=1`

`#SBATCH --cpus-per-task=4`

`#SBATCH --time=5-00`

`#SBATCH --mem=10G`

`#SBATCH --mail-type=END,FAIL`

`#SBATCH --mail-user=mam840@georgetown.edu`

`# Load gsutil module`

`module load google-cloud-sdk`

`# For example, to transfer the file "Sample1_sorted.bam" under your home directory to the bucket, use this but just change/check the file name.`

`gsutil cp /home/mam840/finalproject.MIM/SRR27287968/SRR27287968.sra_*.fastq.gz gs://gu-biology-dept-class/Bioinformatics_Project_MIM/raw/`


## running fastqc on raw files
Enter interactive mode on a compute node (from where you are) with:

`srun --pty bash`
Confirm the prompt changes (e.g., from net-ID@m12-controller to a compute node indicator).​

Then load FastQC using 

`module load fastqc`

You can confirm FastQC is available and see options:

`fastqc -h`

Create a directory for the reports (once per dataset)

`mkdir -p fastqc_out`

Then run FastQC. -o fastqc_out tells FastQC to put the .html and .zip outputs in the fastqcout directory.

`fastqc -o fastqc_out  SRR27288027.sra_*.fastq`

Example with two paired‑end files:

`fastqc -o fastqc_out SRR27288027.sra_*.fastq.gz`

Still on the compute node:

`ls fastqc_out`

Put files into the google buckt:

`gcloud storage cp *.html gs://gu-biology-dept-class/mmw162/Bioinformatics_Project_MIM/`

 ## FastQC Analysis: 
Filename	SRR27288027.sra_1.fastq.gz

Total Sequences	28636756

Total Bases	2.8 Gbp

Sequences flagged as poor quality	0

Sequence length	100

% GC	40

-----------------------------------------------

Filename	SRR27288027.sra_2.fastq.gz

Total Sequences	28636756

Total Bases	2.8 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40

-------------------------------------------------
Filename	SRR27287968.sra_1.fastq.gz

Total Sequences	22337013

Total Bases	2.2 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40

-------------------------------------------------------

Filename	SRR27287968.sra_2.fastq.gz

Total Sequences	22337013

Total Bases	2.2 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40

--------------------------------------------------
Filename	SRR27288028.sra_1.fastq.gz

Total Sequences	22870780

Total Bases	2.2 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	39

-----------------------------------------------------
Filename	SRR27288028.sra_2.fastq.gz

Total Sequences	22870780

Total Bases	2.2 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	39

-----------------------------------------------
Filename	SRR27287969.sra_1.fastq.gz

Total Sequences	23583443

Total Bases	2.3 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40

----------------------------------------------------
## run trimmomatic to trim reads 
`#!/bin/bash`

`#SBATCH --job-name="sample6.A"`

`#SBATCH --output="%x.o%j"`

`#SBATCH --mail-type=END,FAIL --mail-user=mmw162@georgetown.edu`

`#SBATCH --nodes=1`

`#SBATCH --ntasks=1`

`#SBATCH --cpus-per-task=4`

`#SBATCH --time=03:00:00`

`#SBATCH --mem=10G`

`shopt -s expand_aliases`

`module load trimmomatic`

# input files
`R1=/home/mmw162/fastqc_final/SRR27288027/SRR27288027.sra_1.fastq.gz`

`R2=/home/mmw162/fastqc_final/SRR27288027/SRR27288027.sra_2.fastq.gz`

# adapters
`adapters=/home/mmw162/mmw162/HW4_input_files/TruSeq3-PE.fa`

# output directory
`OUTDIR=/home/mmw162/fastqc_final/trimmed`

`mkdir -p $OUTDIR`

`trimmomatic PE -threads $SLURM_CPUS_PER_TASK \`

`$R1 $R2 \`

`$OUTDIR/SRR27288028_1.paired.fq.gz $OUTDIR/SRR27288028_1.unpaired.fq.gz \`

`$OUTDIR/SRR27288028_2.paired.fq.gz $OUTDIR/SRR27288028_2.unpaired.fq.gz \`

`ILLUMINACLIP:$adapters:2:30:10 \`

`SLIDINGWINDOW:4:20 \`

`MINLEN:50`

## copied trimmed reads to the bucket

`#!/bin/bash`

`#SBATCH --job-name="transfer"`

`#SBATCH --output="%x.o%j"`

`#SBATCH --nodes=1`

`#SBATCH --ntasks=1`

`#SBATCH --cpus-per-task=4`

`#SBATCH --time=5-00`

`#SBATCH --mem=10G`

`#SBATCH --mail-type=END,FAIL`

`#SBATCH --mail-user=mmw162@georgetown.edu`

`# Load gsutil module`

`module load google-cloud-sdk`

`# For example, to transfer the file "Sample1_sorted.bam" under your home directory to the bucket, use this but just change/check the file name.`

`gsutil cp /home/mmw162/fastqc_final/trimmed/*.fq.gz gs://gu-biology-deptclass/Bioinformatics_Project_MIM/trimmed_reads`


## Run fastqc on trimmed files
GOAL: confirm trimming improved sequence read quality, removed adapters etc
Enter interactive mode on a compute node (from where you are) with:

`srun --pty bash`
Confirm the prompt changes (e.g., from net-ID@m12-controller to a compute node indicator).​

Then load FastQC using

`module load fastqc`

Create a directory for the reports (once per dataset)

`mkdir -p trimmedfastqc_out`

Then run FastQC. -o trimmedfastqc_out tells FastQC to put the .html and .zip outputs in the fastqcout directory.

`fastqc -o trimmedfastqc_out  SRR27288027.sra_*.fastq`

Still on the compute node:

`ls trimmedfastqc_out`

Put files into the google buckt:

`gcloud storage cp *.html gs://gu-biology-dept-class/Bioinformatics_Project_MIM/fastq_trimmed`

## use Bowtie2 to align reads to the human genome 
- downloaded human reference genome GRCh38 from the colorectal cancer group's bucket
- use a slurm script to run bowtie2 for each sample

`#!/bin/bash`

`#SBATCH --job-name=bowtie2_HPV`

`#SBATCH --output=/home/mam840/finalproject.MIM/logs/bowtie-%j.out`

`#SBATCH --error=/home/mam840/finalproject.MIM/logs/bowtie-%j.err`

`#SBATCH --mail-type=END,FAIL`

`#SBATCH --mail-user=mam840@georgetown.edu`

`#SBATCH --time=8:00:00`

`#SBATCH --mem=16G`

`#SBATCH --cpus-per-task=8`

`# ---------SET UP----------`

`SAMPLE="Sample1.69"     #whatever your sample # is!`

`INDEX="/home/mam840/finalproject.MIM/bowtie2/index/GRCh38"`

`OUTPUTDIR="/home/mam840/finalproject.MIM/bowtie2/"`

`# --------- LOAD MODULES ----------`

`module purge`

`module load bowtie2/2.5.4`

`module load samtools`

`# --------- RUN BOWTIE2 AND PIPE TO SAMTOOLS ----------`

`# First make output and log directories; move into OUTPUTDIR`

`mkdir -p "${OUTPUTDIR}"`

`cd "${OUTPUTDIR}"`

`mkdir -p logs`

`echo "Running bowtie2 on sample ${SAMPLE}"`

`bowtie2 -p 8 -x "${INDEX}" \`

  `-1 "/home/mam840/finalproject.MIM/trimmed/SRR27287969_1.paired.fq.gz" \`
  
  `-2 "/home/mam840/finalproject.MIM/trimmed/SRR27287969_2.paired.fq.gz" \`

`| samtools view -b - > "${SAMPLE}.bam"`

`echo "Finished running bowtie2 and performing compression"`

`#---------sort and index files`

`echo "Sorting"`

`samtools sort "${SAMPLE}.bam" > "${SAMPLE}_sorted.bam"`

`echo "Indexing"`

`samtools index "${SAMPLE}_sorted.bam"`

`echo "Extracting unmapped (microbial) reads..."`

`samtools fastq \`

    `-f 12 \`
    
    `-F 256 \`
    
    `-1 "${OUTPUTDIR}/${SAMPLE}_unmapped_R1.fastq.gz" \`
    
    `-2 "${OUTPUTDIR}/${SAMPLE}_unmapped_R2.fastq.gz" \`
    
    `-@ 8 \`
    
    `"${SAMPLE}_sorted.bam"`

`echo "Finished ${SAMPLE}"`


## use Kracken2/Bracken on human removed reads for taxonomic classification
This script classifies microbial reads from four vaginal metagenome samples using Kraken2 v2.17.1 and estimates species-level abundance with Bracken. Input files are unmapped FASTQ reads after Bowtie2 host removal against GRCh38. The 8GB pre-built standard Kraken2 database was used (k2_standard_08GB, downloaded 2026-02-26).

Note on samples 68 and 69: These samples retained high human read contamination (50% and 99% Homo sapiens respectively) even after the redo Bowtie2 step. Human reads were manually removed. Very few microbial reads remained (~8MB and ~1.7MB). Sample 69 also had low sequencing depth (5.6M reads vs 21–27M for other samples). Results for these two samples should be interpreted with caution.

Script: redo_kraken.sh
`#!/bin/bash`
`#SBATCH --job-name=redo_kraken`
`#SBATCH --output=redo_kraken_%j.out`
`#SBATCH --error=redo_kraken_%j.err`
`#SBATCH --mail-type=END,FAIL`
`#SBATCH --mail-user=icc241@georgetown.edu`
`#SBATCH --time=08:00:00`
`#SBATCH --cpus-per-task=8`
`#SBATCH --mem=64G`

`source ~/.bashrc`
`conda activate kraken_env`

`DB=~/hpvproject/slurmscripts`
`READS=~/hpvproject/redo_fastq`
`OUTDIR=~/hpvproject/kraken2_out_redo`
`BRACKEN_OUT=~/hpvproject/bracken_out_redo`

`mkdir -p $OUTDIR $BRACKEN_OUT`

# Sample1.27
`echo "Processing Sample1.27..."`
`kraken2 --db $DB --paired --threads 8 --gzip-compressed \`
    `--report $OUTDIR/Sample1.27.k2report \`
    `--output $OUTDIR/Sample1.27.kraken2 \`
    `$READS/Sample1.27.Final_unmapped_R1.fastq.gz \`
    `$READS/Sample1.27.Final_unmapped_R2.fastq.gz`
`bracken -d $DB -i $OUTDIR/Sample1.27.k2report \`
    `-o $BRACKEN_OUT/Sample1.27_species.bracken -r 100 -l S -t 10`
`echo "Done: Sample1.27"`

# Sample1.28
`echo "Processing Sample1.28..."`
`kraken2 --db $DB --paired --threads 8 --gzip-compressed \`
    `--report $OUTDIR/Sample1.28.k2report \`
    `--output $OUTDIR/Sample1.28.kraken2 \`
    `$READS/Sample1.28_unmapped_R1.fastq.gz \`
    `$READS/Sample1.28_unmapped_R2.fastq.gz`
`bracken -d $DB -i $OUTDIR/Sample1.28.k2report \`
    `-o $BRACKEN_OUT/Sample1.28_species.bracken -r 100 -l S -t 10`
`echo "Done: Sample1.28"`

# Sample1.68
`echo "Processing Sample1.68..."`
`kraken2 --db $DB --paired --threads 8 --gzip-compressed \`
    `--report $OUTDIR/Sample1.68.k2report \`
    `--output $OUTDIR/Sample1.68.kraken2 \`
    `$READS/Sample1.68_unmapped_R1.fastq.gz \`
    `$READS/Sample1.68_unmapped_R2.fastq.gz \`
`bracken -d $DB -i $OUTDIR/Sample1.68.k2report \`
    `-o $BRACKEN_OUT/Sample1.68_species.bracken -r 100 -l S -t 10`
`echo "Done: Sample1.68"`

# Sample1.69
`echo "Processing Sample1.69..."`
`kraken2 --db $DB --paired --threads 8 --gzip-compressed \`
    `--report $OUTDIR/Sample1.69.k2report \`
    `--output $OUTDIR/Sample1.69.kraken2 \`
    `$READS/Sample1.69.Final_unmapped_R1.fastq.gz \`
    `$READS/Sample1.69.Final_unmapped_R2.fastq.gz`
`bracken -d $DB -i $OUTDIR/Sample1.69.k2report \`
   `-o $BRACKEN_OUT/Sample1.69_species.bracken -r 100 -l S -t 10`
`echo "Done: Sample1.69"`

`echo "All samples complete."`
`echo "Kraken2: $OUTDIR"`
`echo "Bracken: $BRACKEN_OUT"`

## Human Contamination check
After running, human contamination was checked per sample:
`grep -i "homo" ~/hpvproject/kraken2_out_redo/Sample1.27.k2report`
`grep -i "homo" ~/hpvproject/kraken2_out_redo/Sample1.28.k2report`
`grep -i "homo" ~/hpvproject/kraken2_out_redo/Sample1.68.k2report`
`grep -i "homo" ~/hpvproject/kraken2_out_redo/Sample1.69.k2report`

RESULTS: 
Sample1.27 = 1.55%
Sample1.28 = low
Sample1.68 = 50.33%
Sample1.69 = very high

## Data Analysis & Results
<img width="2801" height="1326" alt="Rplot06" src="https://github.com/user-attachments/assets/fe6c7144-c32e-492f-b5f8-9c7140cdde74" />
Fig. 1 A) Pie charts represent the mean proportional abundance of bacterial species within the selected samples. B) Bar charts display the proportion of dominant species in individual samples.

<img width="2522" height="1299" alt="Rplot08" src="https://github.com/user-attachments/assets/23c12952-cea0-4235-b10e-8315ac589f66" />
Fig. 2 A) Bar charts display shannon diversity index B) Bar charts display species richness.



