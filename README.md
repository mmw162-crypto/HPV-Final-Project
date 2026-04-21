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

- Cd into new downloaded directory
- Change files into fastq format, then compress.
`fasterq-dump *.sra`

`gzip *.fastq`

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

 Analysis: 
Filename	SRR27288027.sra_1.fastq.gz

Total Sequences	28636756

Total Bases	2.8 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40


Filename	SRR27288027.sra_2.fastq.gz

Total Sequences	28636756

Total Bases	2.8 Gbp

Sequences flagged as poor quality	0

Sequence length	100

%GC	40
