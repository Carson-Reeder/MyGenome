# This is a repository for CS485G applied bioinformatics. 

## 1 Raw Quality Assesment
Raw sequences were analyzed with `fastqc` and contained in the RawQualityAssesment folder.

## 2 Trimming Raw Sequences
Raw Sequences were trimmed using Trimmomatic with options `IllUMINACLIP:adaptors.fasta:2:30:10, SLIDINGWINDOW:20:20, MINLEN:120`.
adaptors.fasta can be found in the Trimming folder.
`Input Read Pairs: 7245926, Both Surviving: 6227721 (85.95%), Foward Only Surviving: 143497(1.98%), Reverse Only Surviving: 816111 (11.26%), Dropped: 58597 (0.81%)`

## 3 Trimmed Quality Assesment
Trimmed sequences were analyzed with `fastqc` and contained in the TrimmedQualityAssesment folder.
The number of bases in foward and reversse reads were then counted using commands: `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_1_paired.fastqc` and `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_2_paired.fastqc`. Foward read contained 926247728 bases and reverse read contained 933687383 bases.
