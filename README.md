# RiboScreen

RiboScreen is a database of maize RNA-Seq contaminants

### Introduction

Ribosomal RNA (rRNA) is the most abundant RNA species in the cell, accounting for approximately 90% of total RNA. Removing the rRNA fraction before the construction of RNA-Seq libraries is a critical step to enhance the detection of the remaining, most informative 10% of the transcriptome (O’Neil et al. 2013).

The conventional method to overcome this issue is to selectively target the polyadenylated transcripts using oligo(dT) primers during the reverse transcription. Priming with oligo (dT), though, tends to skew the sequencing coverage towards the polyadenylated 3’ end of transcripts, and fails to reveal the transcripts missing the poly-A tail, such as noncoding RNAs (ncRNAs) (Barret et al., 2021).

An alternative approach, ribodepletion, typically removes the rRNA by oligo-hybridization methods, that allows reverse transcription with random primers, producing a more uniform coverage, and, more importantly, enables the detection of ncRNAs. However, ribodepletion requires species-specific oligonucleotides, that are commonly designed to complement mammalian ribosomal subunits. Universal ribodepletion oligonucleotides do not exist for plant and fungal RNA samples so that, for each different species, custom oligonucleotides need to be designed and validated. 

### The Problem
Plant ribodepletion kits are commercially available, but, in our hands, none achieves the full elimination of all rRNAs.

To integrate the physical separation of rDNA before sequencing and further improve the detection of low-abundance transcripts, we used the software SortMeRNA (Kopylova et al., 2012) to implement a post-sequencing filtration strategy to delete the short reads matching contaminant sequences from samples of our interest, derived from root tips of Zea mays cv. B73. 

To this aim, we assembled a database of the most frequent contaminants, including all nuclear ribosomal subunits (5S, 5.8S, 18S, 28S), the internal transcribed spacer (ITS) region, chloroplast and mitochondrial rRNAs, tRNAs, and the signal recognition particle (7SL/SRP).

### Pipeline description
The database was created in three steps: 
* First, we obtained known sequences of the chosen contaminants from public repositories. 
* Second, to ensure specificity, we used them as queries to search the maize cv. B73 genome, to identify similar but non-identical sequences possibly present in the assembly. 
* Third, we tested the database against RNA-Seq data from maize cv. B73. We then explored the possibility of using the same database on other samples, running the same analysis on a different maize cultivar, NC350, and on a relative species, sorghum. 

Below we describe the pipeline used and make the contaminant database available to the community. 
    
### References
<sub> - O'Neil D, Glowatz H, Schlumpberger M. Ribosomal RNA depletion for efficient use of RNA-seq capacity. Curr Protoc Mol Biol. 2013 Jul;Chapter 4:Unit 4.19. doi: 10.1002/0471142727.mb0419s103. PMID: 23821444.</sub> 

<sub> - Barrett A, McWhirter R, Taylor SR, Weinreb A, Miller DM, Hammarlund M. A head-to-head comparison of ribodepletion and polyA selection approaches for Caenorhabditis elegans low input RNA-sequencing libraries. G3 (Bethesda). 2021 Jul 14;11(7):jkab121. doi: 10.1093/g3journal/jkab121. PMID: 33856427; PMCID: PMC8495925.</sub> 
