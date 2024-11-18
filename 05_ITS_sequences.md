# Internal transcribed spacer (ITS) queries

The rRNA subunits are co-transcribed as a single polycistronic precursor, the 35S rRNA, containing three rRNA species (18S, 5.8S, and 25S), as well as internal transcribed spacers (ITS1 and ITS2) that will be removed during the maturation process. 

![Untitled](https://github.com/user-attachments/assets/3d6f063c-ef3a-4c6e-83ba-fcd27b9eb754)

To screen RNA-Seq data from contaminants, we want to used only ITS belonging to complete 35S transcripts. We will first identify full 35S units, and then subtract 18S, 5.8S, and 25S.


#### Determine the genomic coordinates of all 35S precursor.

ITS1 and ITS2 in maize are typically shorted than 250 bp.
We will merge the coordinates of 18S, 5.8S, and 25S subunits that are within a distance of 250 bp.

```bash
cat \
<( awk '{print $0"\t5.8S"}' 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) \
<( awk '{print $0"\t18S"}' 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) \
<( awk '{print $0"\t28S"}' 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed) | cut -f 1-3,7 | \
sort -k1,1 -k2,2n > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S+18S+28S.bed

bedtools merge -d 250 -c 4 -o collapse \
-i rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S+18S+28S.bed | \
sort -k1,1 -k2,2n > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed


#### expected output
chr6	16745916	16751710	28S,5.8S,18S
chr6	16754714	16760506	28S,5.8S,18S
chr6	16766453	16772017	28S,5.8S,18S
```

#### Extract only the complete 35S  units.

```bash
grep '28S,5.8S,18S$\|18S,5.8S,28S$' rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.bed > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.rDNA_units.complete.bed
```

