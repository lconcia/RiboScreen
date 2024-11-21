# Internal transcribed spacer (ITS) queries

The rRNA subunits are co-transcribed as a single polycistronic precursor, the 35S rRNA, containing three rRNA species (18S, 5.8S, and 25S), as well as internal transcribed spacers (ITS1 and ITS2) that will be removed during the maturation process. 

![Untitled](https://github.com/user-attachments/assets/3d6f063c-ef3a-4c6e-83ba-fcd27b9eb754)

To screen RNA-Seq data from contaminants, we want to used only ITS belonging to complete 35S transcripts. We will first identify full 35S units, and then subtract 18S, 5.8S, and 25S.


#### 1) Determine the genomic coordinates of all 35S precursor.

ITS1 and ITS2 in maize are typically shorted than 250 bp.
We will merge the coordinates of 18S, 5.8S, and 25S subunits that are within a distance of 250 bp.

```bash
$ cat \
<( awk '{print $0"\t5.8S"}' 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) \
<( awk '{print $0"\t18S"}' 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) \
<( awk '{print $0"\t28S"}' 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) | cut -f 1-3,7 | \
sort -k1,1 -k2,2n > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S+18S+28S.bed

$ bedtools merge -d 250 -c 4 -o collapse \
-i rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S+18S+28S.bed | \
sort -f -k1,1 -k2,2n > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed

$ head -n 4 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed

#### expected output
chr6    17106224        17112014        18S,5.8S,28S
chr6    17114979        17117166        18S,5.8S
chr6    17125248        17128625        28S
chr6    17131590        17137384        18S,5.8S,28S
(...)
```

#### 2) Extract only the complete 35S  units.

```bash
$ grep '28S,5.8S,18S$\|18S,5.8S,28S$' rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.complete.bed

$ head -n 3 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.complete.bed

#### expected output
chr10	34699901	34705691	18S,5.8S,28S
chr6	16745916	16751710	28S,5.8S,18S
chr6	16754714	16760506	28S,5.8S,18S
chr6	16766453	16772017	28S,5.8S,18S
(...)
```

#### 3) Subtract the subunits from the complete 35S  units.
```
$ bedtools subtract -nonamecheck \
-a <( sort -k1,1 -k2,2n rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed ) \
-b <( sort -k1,1 -k2,2n rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S+18S+28S.bed ) > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.bed

$ head -n 4 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.complete.bed

#### expected output
chr6	16749299	16749524	28S,5.8S,18S
chr6	16749680	16749900	28S,5.8S,18S
chr6	16758096	16758320	28S,5.8S,18S
chr6	16758476	16758696	28S,5.8S,18S
(...)
```

#### 4) Retrieve the corresponding sequences
```bash
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.bed \
-fo  rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta

$ head -n 4 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.ITS.fasta

#### expected output
>ITS.chr10:34701711-34701932
ccgtgacccttaaacaaaacagaccgcgaacgagtcacccgtgccgccgggctccggcccggcacgctgccccccccgaacctcccgcggggaaggggggggacgctaaaaagaacccacggcgccccgggcgccaaggaacaccagtactacctcctgccccgcggagcggtcggcccgccttccgctcccagggcagcggttacaccttaatcgacacg
>ITS.chr10:34702088-34702309
gccaaaagacactcccaacacccccccgcggggcgagggacgtggcgtctggccccccgcgccgcacggcgaggtgggccgaagcaggggctgccggcgaaccgcgccgggcgcagcacgtggtgggcgacatcaagttgttctcggtgcagcgtcccggcgcgcggccggccattcggccctaaggacccatcgagcgaccgagcttgccctcggaccgc
(...)
```
