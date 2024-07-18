# RiboScreen

RiboScreen is a database of maize RNA-Seq contaminants

### Background

Ribosomal RNA (rRNA) is the most abundant RNA species in the cell, accounting for approximately 90% of total RNA. Removing the rRNA fraction before the construction of RNA-Seq libraries is a critical step to enhance the detection of the remaining, most informative 10% of the transcriptome (O’Neil et al. 2013).

The conventional method to overcome this issue is to selectively target the polyadenylated transcripts using oligo(dT) primers during the reverse transcription. Priming with oligo (dT), though, tends to skew the sequencing coverage towards the polyadenylated 3’ end of transcripts, and fails to reveal the transcripts missing the poly-A tail, such as noncoding RNAs (ncRNAs) (Barret et al., 2021).

An alternative approach, ribodepletion, typically removes the rRNA by oligo-hybridization methods, that allows reverse transcription with random primers, producing a more uniform coverage, and, more importantly, enables the detection of ncRNAs. However, ribodepletion requires species-specific oligonucleotides, that are commonly designed to complement mammalian ribosomal subunits. Universal ribodepletion oligonucleotides do not exist for plant and fungal RNA samples so that, for each different species, custom oligonucleotides need to be designed and validated. 

### The Problem
Plant ribodepletion kits are commercially available, but, in our hands, none achieves the full elimination of all rRNAs.

To integrate the physical separation of rDNA before sequencing and further improve the detection of low-abundance transcripts, we used the software SortMeRNA (Kopylova et al., 2012) to implement a post-sequencing filtration strategy to delete the short reads matching contaminant sequences from samples of our interest, derived from root tips of Zea mays cv. B73. 

To this aim, we assembled a database of the most frequent contaminants, including all nuclear ribosomal subunits (5S, 5.8S, 18S, 28S), the internal transcribed spacer (ITS) region, chloroplast and mitochondrial rRNAs, tRNAs, and the signal recognition particle (7SL/SRP). The database was used in to remove contaminants using the tool SortMeRNA https://github.com/sortmerna/sortmerna.

### Pipeline outline
The database was created in three steps: 
1) We obtained known sequences of the chosen contaminants from public repositories.
 -    Chloroplast chromosome            https://www.ncbi.nlm.nih.gov/nuccore/NC_001666.2
 -    Mitochondrion chromosome          https://www.ncbi.nlm.nih.gov/nuccore/NC_007982.1
    
3) To ensure specificity, we used them as queries to search the maize cv. B73 genome, to identify similar but non-identical sequences possibly present in the assembly. 
4) We tested the database against RNA-Seq data from maize cv. B73. We then explored the possibility of using the same database on other samples, running the same analysis on a different maize cultivar, NC350, and on a relative species, sorghum. 

### Sequence Files 

* **18S rRNA subunit :** 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz

* **28S rRNA subunit :** 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz

* **5.S rRNA subunit :** 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz

* **5S rRNA subunit :** 5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz

* **Internal transcribed spacer (ITS) :** rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta.gz

* **Signal recognition particle RNA (SRP) :** SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz

* **Transfer RNAs (tRNA) :** tRNAs.vs_AGPv5.merged.fasta.gz

* **Plastid chromosome :** Chl.vs_AGPv5.fasta.gz

* **Mitochondrial chromosome :** Mt.vs_AGPv5.fasta.gz

### Running sortMeRNA

*  **Manual**
  
https://sortmerna.readthedocs.io/en/latest/manual4.0.html


*  **Usage**
  
```
sortmerna --threads 16 --out2 True --zip-out Yes --fastx true --blast '1 cigar qcov qstrand' --paired_in true \
-ref sortMeRNA.B73_AGPv5.db/ChlMt_db/Mt.vs_AGPv5.fasta \
-ref sortMeRNA.B73_AGPv5.db/ChlMt_db/Chl.vs_AGPv5.fasta \
-ref sortMeRNA.B73_AGPv5.db/tRNAs_db/tRNAs.vs_AGPv5.merged.fasta \
-ref sortMeRNA.B73_AGPv5.db/rRNA_db/annotation_vs_AGPv5/5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref sortMeRNA.B73_AGPv5.db/rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta \
-ref sortMeRNA.B73_AGPv5.db/18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref sortMeRNA.B73_AGPv5.db/28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref sortMeRNA.B73_AGPv5.db/5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref sortMeRNA.B73_AGPv5.db/SRP_db/SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-reads $SAMPLE.R1.fastq \
-reads $SAMPLE.R2.fastq \
--idx-dir sortMeRNA.B73_AGPv5.db/sortmerna.idx-dir \
--workdir output_sortMeRNA.$SAMPLE \
--kvdb    output_sortMeRNA.$SAMPLE/kvdb \
--readb   output_sortMeRNA.$SAMPLE/readb \
--other   output_sortMeRNA.$SAMPLE/clean_$SAMPLE.fastq
```

### References
<sub> - O'Neil D, Glowatz H, Schlumpberger M. Ribosomal RNA depletion for efficient use of RNA-seq capacity. Curr Protoc Mol Biol. 2013 Jul;Chapter 4:Unit 4.19. doi: 10.1002/0471142727.mb0419s103. PMID: 23821444.</sub> 

<sub> - Barrett A, McWhirter R, Taylor SR, Weinreb A, Miller DM, Hammarlund M. A head-to-head comparison of ribodepletion and polyA selection approaches for Caenorhabditis elegans low input RNA-sequencing libraries. G3 (Bethesda). 2021 Jul 14;11(7):jkab121. doi: 10.1093/g3journal/jkab121. PMID: 33856427; PMCID: PMC8495925.</sub> 
