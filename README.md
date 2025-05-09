# This is a repository for CS485G applied bioinformatics. 

## NCBI Raw Sequence Upload
The raw sequence files were first uploaded to NCBI database and linked with the ABT480 class project on NCBI
Bio project: PRJNA926786
BioSampleId: SAMN46817899
SRA accession: SRR32334370

## Raw Quality Assesment
Raw sequences were analyzed with `fastqc` and are contained in the RawQualityAssesment folder. The foward(1) and reverse(2) reads both included.
`fastqc UFVPY74_1.fq.gz UFVPY74_2.fq.gz`
The fastqc output UFVPY74_1_fastqc.html and UFVPY74_2_fastq.html were then transferred to a windows machine and opened. The adapter content was the main focus of the fastqc analysis. 

## Trimming Raw Sequences
Raw Sequences were trimmed using Trimmomatic 0.38
`java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog UFVPY74_errorlog.txt UFVPY74_1.fq.gz UFVPY74_2.fq.gz UFVPY74_1_paired.fq UFVPY74_1_unpaired.fq UFVPY74_2_paired.fq UFVPY74_2_unpaired.fq IllUMINACLIP:adaptors.fasta:2:30:10, SLIDINGWINDOW:20:20, MINLEN:120`.
adaptors.fasta can be found in the Trimming folder.
The output of Trimmomatic is below
`Input Read Pairs: 7245926, Both Surviving: 6227721 (85.95%), Foward Only Surviving: 143497(1.98%), Reverse Only Surviving: 816111 (11.26%), Dropped: 58597 (0.81%)`

## Trimmed Quality Assesment
Trimmed sequences were analyzed with `fastqc` and are contained in the TrimmedQualityAssesment folder. Again adaptor content was the main focus of the analysis.
The number of bases in foward and reverse reads were then counted using commands: `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_1_paired.fastqc` and `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_2_paired.fastqc`. Foward read contained 926247728 bases and reverse read contained 933687383 bases.

## Determining Optimal K-value
The optimal k-value was found by first using velvet advisor (https://dna.med.monash.edu/~torsten/velvet_advisor/) in order to find a middle, starting k-value. 
This value was found to be around 93. Once this was found, Velvetoptimiser was ran with a range of k-values from 61-131 with a step size of 10. 
`velvetoptimiser -s 61 -e 131 -x 10 -d UFVPY74_velvet_optimal_1 -f ``-shortPaired -fastq -seperate UFVPY74_1_paired.fastq UFVPY74_2_paired.fastq`` -t 1`
This gave an optimal k-value of 91. After this, velvet was ran again with a smaller step size of 2 with k-values ranging from 81-101. 
`velvetoptimiser -s 81 -e 101 -x 2 -d UFVPY74_velvet_optimal_2 -f ``-shortPaired -fastq -seperate UFVPY74_1_paired.fastq UFVPY74_2_paired.fastq`` -t 1`
This gave a final optimal k-value of 99.

## Removal of contigs shorter than 200 base pairs
Contigs in the optimal assembly (k-value 99) were then trimmed if determined to be shorter than 200 contigs. This was done using a perl script. The script outputs a UFVPY74_final.fasta file with the short contigs removed. 
`perl CullShortContigs.pl UFVPY74_optimal.fasta`
After the script was ran, another perl script was ran to verify removal of short contigs in the new UFVPY74_final.fasta file.
`perl SequenceLength.pl UFVPY74_final.fasta`

## BUSCO analysis
BUSCO was used to assess genome completeness. The database used was odb10_ascomycota. This BUSCO run was done on a University of Kentucky supercomputer called the MCC. 
`sbatch /project/farman_s25abt480/SLURM_SCRIPTS/BuscoSingularity.sh UFVPY74_final.fasta`
Output:
`C:98.0%[S:97.8%,D:0.2%],F:0.4%,M:1.6%,n:1706,E:3.8%`  
Final BUSCO score was 98% complete, with the complete + fragmented score being 98.40%.

## 6 BLAST

## 7 NCBI Submission

## 8 Gene Prediction




