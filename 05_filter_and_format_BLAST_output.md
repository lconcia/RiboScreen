# 05_filter_and_format_BLAST_output

Once identified the contaminant sequences with BLAST, we need to increase the specificity by selecting only the hits of meaningful size and discard hits mapping on chloroplast and mithochondrial genome.
We will then format as BED file to retrieve the sequences as FASTA files.
#### #   visualize the BLAST output
```bash
$ head -n3 28S.Zea_Mays_vs_AGPv5.reblast.out

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
#### #    for Mt and Chl min 150 bp (length of one read)
```bash
$ cat Mt.vs_AGPv5.reblast.out  | awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > Mt.vs_AGPv5.bed
$ cat Chl.vs_AGPv5.reblast.out | awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > Chl.vs_AGPv5.bed
```
#### #    for tRNA min 59 bp (length of shortest tRNA in maize)
```bash
$ cat tRNAs.vs_AGPv5.reblast.out  | awk 'BEGIN{OFS="\t"}{if ($22-$21>=59) print $2,$21,$22,$11,($22-$21) }' | grep  -v 'chrM\|chrC' | sort -k1,1V -k2,2n > tRNAs.vs_AGPv5.bed
```
#### #    for rDNA subunits, filter hits long at least 90% of the length of the respective query. We will obtain the lenght of the query from column 1 (i.e. chr6:16749525-16749680 )
*  1  = qid : query sequence identifier (sequence name in the fasta file)

```bash
$ cat  28S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 28S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat  18S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 18S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat 5.8S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
$ cat   5S.Zea_Mays_vs_AGPv5.reblast.out | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > 5S.Zea_Mays_vs_AGPv5.querysize.above_90.tab
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
```
Merge oberlapping hits, keep the value of % identity.

```bash
$ bedtools merge -c 4,4,4 -o max,median,count -i 28S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 28S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i 18S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 18S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i  5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.bed > 5.8S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
$ bedtools merge -c 4,4,4 -o max,median,count -i  5S.Zea_Mays_vs_AGPv5.querysize.above_90.bed >   5S.Zea_Mays_vs_AGPv5.querysize.above_90.merged.bed
```






for f in *.Zea_Mays_vs_AGPv5.reblast.out
do
cat $f | sed -e 's/[-]/\t/1' -e 's/[:]/\t/1' | awk '{if ((($24-$23)/($3-$2))>=0.90) print  $0"\t"($3-$2)"\t"($24-$23)"\t"($24-$23)/($3-$2) }' - > `basename $f .reblast.out`.querysize.above_90.tab
done


 
* 5S   = 170 bp
* 5.8S = 149 bp
* 18S = 1411 bp
* 28S = 3265 bp

##### #   

## we do not need  % ratio query (column5) and not the query name:start-end (like in rDNA subunits) - we may need it for tRNAs

 
 

 







head -n3 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.reblast.out

NC_001666.2 chrC    0.  1   274214.45   421152  140384  140384  140384  0   100.00  100.00  0   0   0   0   +1  1   140384  +1  1   140384  (1)
NC_001666.2 chrC    0.  7   44435.00    68244   22748   22748   22748   0   100.00  100.00  0   0   0   0   -1  140384  117637  +1  82353   105100  185-6-(2)-8-3-7-184



head -n3 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.reblast.out

NC_007982.1 chrM    0.  1   1112664.49  1708890 569630  569630  569630  0   100.00  100.00  0   0   0   0   +1  1   569630  +1  1   569630  (1)
NC_007982.1 chrM    0.  8   32953.45    50610   16870   16870   16870   0   100.00  100.00  0   0   0   0   -1  130518  113649  +1  42502   59371   5-614-(2)-56-57-3-615-4





grep  -v 'chrM\|chrC' /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.reblast.out | \
awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | sort -k1,1V -k2,2n > /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.bed

    4086 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.bed
  118704 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.reblast.out
 

grep  -v 'chrM\|chrC' /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.reblast.out | \
awk 'BEGIN{OFS="\t"}{if ($22-$21>=150) print $2,$21,$22,$11,($22-$21) }' | sort -k1,1V -k2,2n > /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.bed
 

    3110 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.bed
   70962 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.reblast.out



for f in *.vs_AGPv5.bed
do
ls $f
time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools merge -c 4,4,4 -o max,median,count -i $f > `basename $f bed`merged.bed
done


     1616 Chl.vs_AGPv5.merged.bed
     2290 Mt.vs_AGPv5.merged.bed


## get the fasta 

time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.bed \
-fo   /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.fasta


time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.bed \
-fo   /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.fasta


sed -i.bkp 's/>/>Mt./g'  /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.fasta
sed -i.bkp 's/>/>Chl./g'  /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.fasta



