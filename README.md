# RiboScreen

### RiboScreen is a database of RNA-Seq contaminants in maize.

## Background
Ribosomal RNA (rRNA) is the most abundant RNA species in the cell, accounting for approximately 90% of total RNA. Removing the rRNA fraction before the construction of RNA-Seq libraries is a critical step to enhance the detection of the remaining, most informative 10% of the transcriptome (O’Neil et al. 2013).

The conventional method to overcome this issue is to selectively target the polyadenylated transcripts using oligo(dT) primers during the reverse transcription. Priming with oligo (dT), though, tends to skew the sequencing coverage towards the polyadenylated 3’ end of transcripts, and fails to reveal the transcripts missing the poly-A tail, such as noncoding RNAs (ncRNAs) (Barret et al., 2021).

An alternative approach, ribodepletion, typically removes the rRNA by oligo-hybridization methods, that allows reverse transcription with random primers, producing a more uniform coverage, and, more importantly, enables the detection of ncRNAs. However, ribodepletion requires species-specific oligonucleotides, that are commonly designed to complement mammalian ribosomal subunits. Universal ribodepletion oligonucleotides do not exist for plant and fungal RNA samples so that, for each different species, custom oligonucleotides need to be designed and validated. Plant ribodepletion kits are commercially available, but, in our hands, none achieves the full elimination of all rRNAs.

## Post-sequencing removal of contaminants
To integrate the physical separation of rDNA before sequencing and further improve the detection of low-abundance transcripts, we used the software SortMeRNA (Kopylova et al., 2012) to implement a post-sequencing filtration strategy to delete the short reads matching contaminant sequences from samples of our interest, derived from root tips of Zea mays cv. B73. 

To this aim, we assembled a database of the most frequent contaminants, including all nuclear ribosomal subunits (5S, 5.8S, 18S, 28S), the internal transcribed spacer (ITS) region, chloroplast and mitochondrial rRNAs, tRNAs, and the signal recognition particle (7SL/SRP). The database was used in to remove contaminants using the tool SortMeRNA https://github.com/sortmerna/sortmerna.

## Pipeline outline
### 1) Retrieve and format the genome assembly and annotation.
See [01_format_B73_genome.md](https://github.com/lconcia/RiboScreen/blob/main/01_format_B73_genome.md)
### 2) Retrieve the contaminants sequence.
We obtained known sequences of the chosen contaminants from public repositories.
To use them as BLAST queries we need to convert them to a suitable format. See [02_format_queries.md](https://github.com/lconcia/RiboScreen/blob/main/02_format_queries.md)
| Sequence group | Source |
| ------------- | -------------- |
| <sup>rRNA (28S, 18S, 5.8S, and 5S subunits) </sup>| <sup>[NCBI annotation](https://github.com/lconcia/RiboScreen/blob/main/01_format_B73_genome.md#annotation-formatting)</sup> |
| <sup>rRNA Internal transcribed spacer (ITS) </sup> |<sup>[NCBI annotation](https://github.com/lconcia/RiboScreen/blob/main/01_format_B73_genome.md#annotation-formatting)</sup> |
| <sup>Signal recognition particle (SRP)</sup>| <sup>https://rth.dk/resources/rnp/SRPDB/rna/sequences/srprna_seqs.zip</sup>  |
| <sup>tRNAs</sup> | <sup>https://bioinformatics.um6p.ma/PltRNAdb/data/download/Zea_mays.Nuclear.fa.gz </sup>  |
| <sup>Plastid genome</sup> | <sup>https://www.ncbi.nlm.nih.gov/nuccore/NC_001666.2</sup>   |
| <sup>Mitochondrial genome</sup> | <sup>https://www.ncbi.nlm.nih.gov/nuccore/NC_007982.1</sup>  |

### 3) BLAST sequences over maize AGPv5 genome assembly.
To ensure specificity, we identified contaminants sequences in the genome of maize cv. B73.
See [03_BLAST_over_B73_AGPv5.md](https://github.com/lconcia/RiboScreen/blob/main/03_BLAST_over_B73_AGPv5.md) 
### 4)  Filter and format the BLAST output.
See [04_filter_and_format_BLAST_output.md](https://github.com/lconcia/RiboScreen/blob/main/04_filter_and_format_BLAST_output.md)

* **28S rRNA subunit :** 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz
* **18S rRNA subunit :** 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz
* **5.8S rRNA subunit :** 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz
* **5S rRNA subunit :** 5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz
* **Internal transcribed spacer (ITS) :** rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta.gz
* **Signal recognition particle (SRP) :** SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta.gz
* **Transfer RNAs (tRNA) :** tRNAs.vs_AGPv5.merged.fasta.gz
* **Plastid chromosome :** Chl.vs_AGPv5.fasta.gz
* **Mitochondrial chromosome :** Mt.vs_AGPv5.fasta.gz

### 5) Obtain the ITS sequence from rDNA coordinates.
The Internal transcribed spacer (ITS) is the spacer DNA situated between the small-subunit ribosomal DNA (rDNA) and large-subunit rRNA genes.
We can derived the ITS sequence from the rDNA subunits coordinates. See [05_ITS_sequences.md](https://github.com/lconcia/RiboScreen/blob/main/05_ITS_sequences.md)

### 6) Use the sequences to identify contaminant with sortMeRNA.
We tested the database against RNA-Seq data from maize cv. B73. We then explored the possibility of using the same database on other samples, running the same analysis on a different maize cultivar, NC350, and on a relative species, sorghum. 

*  **Manual**
  
https://sortmerna.readthedocs.io/en/latest/manual4.0.html

*  **Usage**
  
```
sortmerna --threads 16 --out2 True --zip-out Yes --fastx true --blast '1 cigar qcov qstrand' --paired_in true \
-ref Mt.vs_AGPv5.fasta \
-ref Chl.vs_AGPv5.fasta \
-ref tRNAs.vs_AGPv5.merged.fasta \
-ref annotation_vs_AGPv5/5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta \
-ref 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref 5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-ref SRP_db/SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta \
-reads $SAMPLE.R1.fastq \
-reads $SAMPLE.R2.fastq \
--idx-dir sortMeRNA.B73_AGPv5.db/sortmerna.idx-dir \
--workdir output_sortMeRNA.$SAMPLE \
--kvdb    output_sortMeRNA.$SAMPLE/kvdb \
--readb   output_sortMeRNA.$SAMPLE/readb \
--other   output_sortMeRNA.$SAMPLE/clean_$SAMPLE.fastq
```

### References

<sub>O'Neil D, Glowatz H, Schlumpberger M. Ribosomal RNA depletion for efficient use of RNA-seq capacity. Curr Protoc Mol Biol. 2013;Chapter 4. doi:10.1002/0471142727.mb0419s103</sub>

<sub>Barrett A, McWhirter R, Taylor SR, Weinreb A, Miller DM, Hammarlund M. A head-to-head comparison of ribodepletion and polyA selection approaches for Caenorhabditis elegans low input RNA-sequencing libraries. G3 (Bethesda). 2021;11(7):jkab121. doi:10.1093/g3journal/jkab121</sub> 

<sub>Kopylova E, Noé L, Touzet H. SortMeRNA: fast and accurate filtering of ribosomal RNAs in metatranscriptomic data. Bioinformatics. 2012;28(24):3211-3217. doi:10.1093/bioinformatics/bts611</sub>
 
