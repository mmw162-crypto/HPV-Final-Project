# HPV-Final-Project

## Cleaning and Organizing (4/20)
- created folder under mmw162/Bioinformatics_Final_MIM
## Downloading fastq files
Install SRA-tools through an environment: 
`module load anaconda3`

 `conda create -n sra_env -c bioconda sra-tools` (say yes)
 
`conda activate sra_env`
- Mkdir for fastq. Cd into fastq directory.
  
  `mkdir fastqc_final`
  
  (full path: mmw162/fastqc_final)
  
`prefetch SRR27288028`

`prefetch SRR27288027'

`prefetch SRR27287968`

- Cd into new downloaded directory
- Change files into fastq format, then compress.
`fasterq-dump *.sra`

`gzip *.fastq`
