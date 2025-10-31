# This is a repository describing the method of processing, analyzing, and processing the raw genome sequence UFVPY74, from fungus `Pyricularia oryzae`.


# NCBI Raw Sequence Upload
The raw sequence files were first uploaded to NCBI database and linked with the ABT480 class project on NCBI
Bio project: PRJNA926786
BioSampleId: SAMN46817899
SRA accession: SRR32334370
[SRA](https://www.ncbi.nlm.nih.gov/sra/SRR32334370)

# Raw Quality Assesment
Raw sequences were analyzed with `fastqc` and are contained in the RawQualityAssesment folder. The foward(1) and reverse(2) reads both included.
`fastqc UFVPY74_1.fq.gz UFVPY74_2.fq.gz`
The fastqc output UFVPY74_1_fastqc.html and UFVPY74_2_fastq.html were then transferred to a windows machine and opened. The adaptor content was the main focus of the fastqc analysis. 

# Trimming Raw Sequences
Raw Sequences were trimmed using Trimmomatic 0.38
`java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog UFVPY74_errorlog.txt UFVPY74_1.fq.gz UFVPY74_2.fq.gz UFVPY74_1_paired.fq UFVPY74_1_unpaired.fq UFVPY74_2_paired.fq UFVPY74_2_unpaired.fq IllUMINACLIP:adaptors.fasta:2:30:10, SLIDINGWINDOW:20:20, MINLEN:120`.
adaptors.fasta can be found in the Trimming folder.
The output of Trimmomatic is below
`Input Read Pairs: 7245926, Both Surviving: 6227721 (85.95%), Foward Only Surviving: 143497(1.98%), Reverse Only Surviving: 816111 (11.26%), Dropped: 58597 (0.81%)`

# Trimmed Quality Assesment
Trimmed sequences were analyzed with `fastqc` and are contained in the TrimmedQualityAssesment folder. Again adaptor content was the main focus of the analysis.
The number of bases in foward and reverse reads were then counted using commands: `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_1_paired.fastqc` and `awk 'NR % 4 == 2 {total += length($0)} END {print total}' UFVPY74_2_paired.fastqc`. Foward read contained 926247728 bases and reverse read contained 933687383 bases.

# Determining Optimal K-value
The optimal k-value was found by first using velvet advisor (https://dna.med.monash.edu/~torsten/velvet_advisor/) in order to find a middle, starting k-value. 
This value was found to be around 93. Once this was found, Velvetoptimiser was ran with a range of k-values from 61-131 with a step size of 10. 
`velvetoptimiser -s 61 -e 131 -x 10 -d UFVPY74_velvet_optimal_1 -f ``-shortPaired -fastq -seperate UFVPY74_1_paired.fastq UFVPY74_2_paired.fastq`` -t 1`
This gave an optimal k-value of 91. After this, velvet was ran again with a smaller step size of 2 with k-values ranging from 81-101. 
`velvetoptimiser -s 81 -e 101 -x 2 -d UFVPY74_velvet_optimal_2 -f ``-shortPaired -fastq -seperate UFVPY74_1_paired.fastq UFVPY74_2_paired.fastq`` -t 1`
This gave a final optimal k-value of 99.

# Removal of contigs shorter than 200 base pairs
Contigs in the optimal assembly (k-value 99) were then trimmed if determined to be shorter than 200 contigs. This was done using a perl script. The script outputs a UFVPY74_final.fasta file with the short contigs removed. 
`perl CullShortContigs.pl UFVPY74_optimal.fasta`
After the script was ran, another perl script was ran to verify removal of short contigs in the new UFVPY74_final.fasta file.
`perl SequenceLength.pl UFVPY74_final.fasta`

# BUSCO analysis
BUSCO was used to assess genome completeness. The database used was odb10_ascomycota. This BUSCO run was done on a University of Kentucky supercomputer called the MCC. 
`sbatch /project/farman_s25abt480/SLURM_SCRIPTS/BuscoSingularity.sh UFVPY74_final.fasta`
Output:
`C:98.0%[S:97.8%,D:0.2%],F:0.4%,M:1.6%,n:1706,E:3.8%`  
Final BUSCO score was 98% complete, with the complete + fragmented score being 98.40%.

# BLAST for Mitochondrial Sequences
BLAST was used to determine which contigs correspond to the Mitochodrial genome prior to NCBI final submission. This was done as NCBI requires a file that identifies these contigs. MoMitochondrion.fasta is the file used to determine Mitochondrial genomes in the UFVPY74_final.fasta sequence.
`blastn -query MoMitochondrion.fasta -subject UFVPY74_final.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid slen length qstart qend sstart send btop' -out MoMitochondrion.UFVPY74_final.BLAST`
This gives us a .BLAST output which needs to run through another script so that a .csv file can be created that identifies contigs found to match the Mitochondrial genome. 
`awk '$4/$3 > 0.9 {print $2 ",mitochondrion"}' MoMitochondrion.UFVPY74_final.BLAST > UFVPY74_mitochondrion.csv`

# NCBI Submission
Now that the UFVPY74_final.fasta file and UFVPY74_mitochondrion.csv are properly created and everything is trimmed, it is time to upload to NCBI for final submission.
Submission number: SUB15302181
accession number: JBNQXE000000000
Genome Accession:
We have assigned the following accession number to your submission:

SUBID           BioProject      BioSample       Accession       Organism
---------------------------------------------------
SUB15302181     PRJNA926786     SAMN46817899    JBNQXE000000000 Pyricularia oryzae UFVPY74

Please cite the accession number JBNQXE000000000 like this:

This Whole Genome Shotgun project has been deposited at DDBJ/ENA/GenBank
under the accession JBNQXE000000000. The version described
in this paper is version JBNQXE010000000.

# Gene Prediction
## SNAP
After final submission, SNAP was ran on UFVPY74_final. This involved a reference genome, B71Ref2. Using Maker and Fathom, the gene sequence was extracted and properly annotated for SNAP usage. 
`maker2zff B71Ref2.gff`
`fathom genome.ann genome.dna -gene-stats`
`fathom genome.ann genome.dna -categorize 1000`
`fathom uni.ann uni.dna -gene-stats`
`fathom uni.ann uni.dna -export 1000 -plus`
`forge export.ann export.dna`
`hmm-assembler.pl Moryzae . > Moryzae.hmm`
With the Moryzae parameter file now created, it is time to run SNAP
`snap-hmm Moryzae.hmm UFVPY74_final.fasta > UFVPY74_final-snap.zff`
Use Fathom to statistics
`fathom UFVPY74_final-snap.zff UFVPY74_final.fasta -gene-stats`
Create a gff2 file for compatability
`snap-hmm Moryzae.hmm UFVPY_final.fasta -gff > UFVPY74_final-snap.gff2`
## Augustus
Augustus was then also run, it is similar to snap but uses a unique Markov model for gene prediction
`augustus --species=magnaporthe_grisea --gff3=on --singlestrand=true --progress=true ../snap/UFVPY74_final.fasta > UFVPY74_final-augustus.gff`
## Maker
Finally, Maker was used to combine the results of Snap and Augustus together
`maker -CTL`
The maker_opts.ctl file was edited to include proper setup, linking to Moryzae.hmm, UFVPY74_final, and the proper protein (genbank/ncbi-protein-Magnaporthe_organism.fasta)
Tee was used to redirect Maker output to a file before running
`ls -l ~ | tee listing.txt`
Maker was ran using command:
`maker 2>&1 | tee maker.log`
After Maker was complete the gff3 files were then merged together
`gff3_merge -d UFVPY74_final.maker.output/UFVPY74_final_master_datastore_index.log -o UFVPY74_final-annotations.gff`
The fasta files were also merged similarly
`fasta_merge -d UFVPY74_final.maker.output/UFVPY74_final_master_datastore_index.log -o UFVPY74_final-genes.fasta`
The final merged gff3 file as well as the UFVPY74_final-genes.proteins.fasta file from fasta_merge were uploaded to the UKY MCC supercomputer.










