## ChIP-seq analysis of the human transcription factor Runx1

This was an integrated analysis of bulk RNA-seq and ChIP-seq to investigate the role of Runx1 in the MCF7 cell line, a model line used for the study of breast cancer.

Tools used: fastqc, trimmomatic, Bowtie2, samtools, multiqc, deeptools, HOMER, bedtools, UCSC genome browser

Methods: 
*QC, Trimming, and Alignment* 

Full fastq files for IP and Input control samples were downloaded via ftp links from the NCBI Gene Expression Omnibus (GEO accession number GSE75070).These fastq files were inspected for quality control using fastqc v0.12.1-0. Once QC checked, the reads were trimmed of adapters using trimmomatic v0.39. Bowtie2 v2.5.3 was used to create an index of the entire human genome using the GENCODE hg38 primary assembly for use in alignment.

The trimmed reads were aligned against this GRch38 index using Bowtie2 v2.5.3 with default parameters and converted the output to bam file using samtools v1.19.2. The alignment bam files were sorted and indexed using samtools v1.19.2, following default parameters. Alignment quality control was performed using samtools v1.19.2 flagstats with default parameters. All QC reports generated thus far (FastQC and flagstats) were concatenated using multiqc v1.20. The sorted bam files were converted to BigWig format using deeptools v3.5.4 bamCoverage with default parameters. These BigWig files were used to create a matrix of average scores for each of the files in every genomic region using deeptools v3.5.4 multiBigwigSummary. This matrix was then used to create a clustered heatmap of the Pearson correlation values between all samples using deeptools v3.5.4 plotCorrelation. 

*Annotation, Peak Calling, Motif Finding* 

bam files were converted to a HOMER specific input for downstream analysis using HOMER v4.11 makeTagDirectory. Peak calling was performed on the IP samples, using the Input control as the background enrichment control with HOMER v4.11 findPeaks. These peak files were converted to a BED format using HOMER v4.11. These peak BED files were used to identify reproducible peaks, (peaks that have at least 50% overlap between the 2 replicates) with bedtools v2.31.1. Signal-artifact regions were filtered from this bed file using the ENCODE blacklist with bedtools v2.31.1. The filtered peaks were annotated to their nearest genomic feature using HOMER v4.11 annotatepeaks with the GENCODE primary assembly gtf file. Motifs were identified using HOMER v4.11 findMotifsGenome with the GENCODE hg38 primary assembly fasta file.

*Signal Covereage Analysis* 

The score matrix for each IP replicate was computed using deeptools v3.5.4 computeMatrix with bin size = 2000 and with reference genomic coordinates for the entire genome extracted from the UCSC Table Browser (selections: Clade: Mammal, Genome: Human, assembly: hg38, group: Genes and Gene Predictions, track: NCBI RefSeq, table: UCSC Refseq, region: genome). This score matrix was used to plot signal coverage for each IP replicate with deeptools v3.5.4 plotProfile.

*Integration with RNA-seq*

The peak calling results were integrated with RNAseq results to determine how often a Runx1 binding peak was observed in genes that were found to be differentially expressed in the same cell line upon Runx1 knockdown using a shRNA. A table of differentially expressed genes was downloaded from NCBI Gene Expression Omnibus GSE75070, and filtered according to the original publication's parameters: p-adjusted < 0.01 and |log2foldchange| > 1.

