# HPV-Final-Project
GOAl: 

## Cleaning and Organizing (4/20)
- created folder under mmw162/Bioinformatics_Final_MIM
- 
## Downloading fastq files
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
