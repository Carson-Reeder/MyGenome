# This is a repository for CS485G applied bioinformatics. 

## 1 Raw Quality Assesment
Raw sequences were analyzed with fastqc and contained in the RawQualityAssesment folder.

## 2 Trimming Raw Sequences
Raw Sequences were trimmed using Trimmomatic with options `IllUMINACLIP:adaptors.fasta:2:30:10, SLIDINGWINDOW:20:20, MINLEN:120`.
adaptors.fasta can be found in the Trimming folder.
`Input Read Pairs: 7245926, Both Surviving: 6227721 (85.95%), Foward Only Surviving: 143497(1.98%), Reverse Only Surviving: 816111 (11.26%), Dropped: 58597 (0.81%)`

## 3 Trimmed Quality Assesment
Trimmed sequences were analyzed with fastq and contained in the TrimmedQualityAssesment folder.
Added a file called totalBases.txt that contains the command ran on forward and reverse reads to count the 
total number of bases
