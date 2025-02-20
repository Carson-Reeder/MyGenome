# This is a repository for CS485G applied bioinformatics. 

## Quality Assesment
Raw sequences were analyzed with fastqc and contained in the RawQualityAssesment folder

## Trimming Raw Sequences
Raw Sequences were trimmed using Trimmomatic with options IllUMINACLIP:adaptors.fasta:2:30:10, SLIDINGWINDOW:20:20, MINLEN:120.
adaptors.fasta can be found in the repository.
