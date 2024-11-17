# Filter the BLAST hits and retrieve the sequences 

Once identified the contaminant sequences with BLAST, we need to increase the specificity by selecting only the hits of meaningful size and discard hits mapping on chloroplast and mithochondrial genome.
We then format as BED file to retrieve the sequences as FASTA files.
#### #   visualize the BLAST output
```bash
$ head -n 3 28S.Zea_Mays_vs_AGPv5.reblast.out

##### expected output
chr6:16745916-16749299	chr6	0.	1	6609.18	10149	3383	3383	3383	0	100.00	100.00	0	0	0	0	+1	1	3383	+1	16745917	16749299	(1)
chr6:16745916-16749299	chr6	0.	1	6596.15	10129	3383	3381	3381	1	99.94	99.94	0	0	1	1	+1	1	3383	+1	17223070	17226451	(2)
chr6:16745916-16749299	chr6	0.	1	6596.15	10129	3383	3381	3381	1	99.94	99.94	0	0	1	1	+1	1	3383	+1	17214276	17217657	(3)
```
#### #   Select the following columns (from https://blast.advbiocomp.com/doc/tabular.html) to format as BED file:
*  2  = sid subject : sequence identifier (chromosome)
* 21  =  sstart     : the starting coordinate of the alignment in the subject sequence
* 22  =  send       : the ending coordinate of the alignment in the subject sequence
* 11  =  pcident    : percent identity over the alignment length (as a fraction of alignlen)
#### #    for Mt and Chl min 150 bp (length of one sequencing read)
```bash
$ cat Mt.vs_AGPv5.reblast.out  | awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > Mt.vs_AGPv5.bed
$ cat Chl.vs_AGPv5.reblast.out | awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > Chl.vs_AGPv5.bed
```
#### #    for tRNA min 59 bp (length of shortest tRNA in maize)
```bash
$ cat tRNAs.vs_AGPv5.reblast.out  | awk 'BEGIN{OFS="\t"}{if ($22-$21>=59) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > tRNAs.vs_AGPv5.bed
```
#### #    for rDNA subunits and SRP, we select the hits long at least 90% of the length of the respective query. We will obtain the lenght of the query from column 1 (i.e. chr6:16749525-16749680 )
*  1  = qid : query sequence identifier (sequence name in the fasta file)

```bash
$ cat  28S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 28S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat  18S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 18S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat 5.8S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat   5S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 5S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat SRP.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > SRP.Zea_Mays_vs_AGPv5.querysize.above_90.tab
```
Format as bed file selecting these columns
*  4 = sid : subject (database) sequence identifier
* 13 = pcident : percent identity over the alignment length (as a fraction of alignlen)
* 23 = sstart  : the starting coordinate of the alignment in the subject sequence
* 24 = send  : the ending coordinate of the alignment in the subject sequence
* 28 = % of query coverage 
* 1 + 2 + 3 = query name
```bash
$ cat 28S.Zea_Mays_vs_AGPv5.querysize.above_90.tab | awk 'BEGIN{OFS="\t"}{print $4,$23,$24,$13,$28,$1":"$2"-"$3}' | sort -k1,1 -k2,2n > 28S.Zea_Mays_vs_AGPv5.querysize.above_90.bed
$ cat 18S.Zea_Mays_vs_AGPv5.querysize.above_90.tab | awk 'BEGIN{OFS="\t"}{print $4,$23,$24,$13,$28,$1":"$2"-"$3}' | sort -k1,1 -k2,2n > 18S.Zea_Mays_vs_AGPv5.querysize.above_90.bed
$ cat 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.tab | awk 'BEGIN{OFS="\t"}{print $4,$23,$24,$13,$28,$1":"$2"-"$3}' | sort -k1,1 -k2,2n > 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.bed
$ cat 5S.Zea_Mays_vs_AGPv5.querysize.above_90.tab | awk 'BEGIN{OFS="\t"}{print $4,$23,$24,$13,$28,$1":"$2"-"$3}' | sort -k1,1 -k2,2n > 5S.Zea_Mays_vs_AGPv5.querysize.above_90.bed
$ cat SRP.Zea_Mays_vs_AGPv5.querysize.above_90.tab | awk 'BEGIN{OFS="\t"}{print $4,$23,$24,$13,$28,$1":"$2"-"$3}' | sort -k1,1 -k2,2n > SRP.Zea_Mays_vs_AGPv5.querysize.above_90.bed
```
Merge overlapping hits, keep the value of % identity.

```bash
$ bedtools merge -c 4,4,4 -o max,median,count -i 28S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i 18S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i  5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i  5S.Zea_Mays_vs_AGPv5.querysize.above_90.bed >   5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i  SRP.Zea_Mays_vs_AGPv5.querysize.above_90.bed >   SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed


head -n 3 ./references/rDNA/28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed

##### expected output
chr10	34702309	34705691	100	99.7	498
chr6	16745916	16749299	100	99.88	498
chr6	16754714	16758096	100	99.94	498
```

## Retrieve the genomic sequences in FASTA format

```bash
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Mt.vs_AGPv5.bed -fo Mt.vs_AGPv5.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Chl.vs_AGPv5.bed -fo Chl.vs_AGPv5.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta tRNAs.vs_AGPv5.bed -fo tRNAs.vs_AGPv5.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed -fo 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed -fo 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed -fo 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta 5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed -fo 5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed  -fo SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
```

### Add the type of contamint to the fasta header to distinguish them
 
```bash
$ sed -i.bkp 's/>/>Mt./g'   Mt.vs_AGPv5.fasta
$ sed -i.bkp 's/>/>Chl./g'  Chl.vs_AGPv5.fasta
$ sed -i.bkp 's/>/>tRNA./g' tRNAs.vs_AGPv5.merged.fasta
$ sed -i.bkp 's/>/>18S./g'  28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ sed -i.bkp 's/>/>28S./g'  18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ sed -i.bkp 's/>/>5.8S./g' 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ sed -i.bkp 's/>/>5S./g'   5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta
$ sed -i.bkp 's/>/>SRP./g'  SRP.Zea_Mays_vs_AGPv5.querysize.above_90.merged.fasta

##### expected output
>SRP.NW_023367221.1:30409-30711
CGAGCAGTTAGCGGCTGTTTGTAACCTGAGCGGGGGCAATAAAGTGGTGTGGATGCCAATTGTGGCAGCTGGATCTGGGTCTGGTGCTGCAATCTGTGGCCCGCCCGTTCCAAGTTGGTAGTTGGGCTGTGGCCGCTTTGGCGAAGGCCGTAGCGTTCGAGCCTTTAGAGTGGCGGGCAATGCGTGAGGCTGGTTTCACAGAGCAGCGAATACCGTCCACTTCCAACGGTGGAAGGATTACGGGCAACTGCACTCCAGGCCCACCTAGGCCTAGGCGCTCTTTCTGGCAGACCACCATTTTT
>SRP.chr1:19809290-19809598
CGAGCTCAGTTGCGCGGGCTTGTAACCCATGTGGGGGTATTAAGGTGGTGTGGATGCTTGGTACCGTGTCTTTGCCTGGGTCCATGGTGTGCCTTTGTTGGCCTGCCCGTTCCAAGTTGGTAGTGGCCGTTGGAAGCCAAGGCGAAGGCCCTGGGCTTCCTTGGACCTATAGTGGCAGGAACGGCGTGAGGCTGTCTTCACAGAGCAGCGATCACTGCCCGCTATTAACGGTGGAAGGATAACAGGCCACTGCAGCATGGGCTCGCTTTAAGGCCTAGACTTTCATACTAAAGCAGACCACCATTTTT
```
