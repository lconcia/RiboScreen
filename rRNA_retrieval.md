#### This file describe the identification of rRNA sequences from B73 genome assembly
### test4
#### test3
##### test2
###### test1

#### 1) Extract rRNA features from the annotation

```bash
$ awk ' $3=="rRNA" '  Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```
##### expected output
```bash
chr1 cmsearch rRNA  45918126  45918244 . + . ID=rna-XR_004855769.1;Parent=gene-LOC111590732;Dbxref=GeneID:111590732,RFAM:RF00001,GenBank:XR_004855769.1;Name=XR_004855769.1;gbkey=rRNA;gene=LOC111590732;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855769.1
chr1 cmsearch rRNA  78523371  78523489 . + . ID=rna-XR_004855692.1;Parent=gene-LOC111590622;Dbxref=GeneID:111590622,RFAM:RF00001,GenBank:XR_004855692.1;Name=XR_004855692.1;gbkey=rRNA;gene=LOC111590622;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855692.1
chr2 cmsearch rRNA 227610721 227610839 . + . ID=rna-XR_004856556.1;Parent=gene-LOC111590902;Dbxref=GeneID:111590902,RFAM:RF00001,GenBank:XR_004856556.1;Name=XR_004856556.1;gbkey=rRNA;gene=LOC111590902;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856556.1
chr2 cmsearch rRNA 227611040 227611158 . + . ID=rna-XR_004856667.1;Parent=gene-LOC111591002;Dbxref=GeneID:111591002,RFAM:RF00001,GenBank:XR_004856667.1;Name=XR_004856667.1;gbkey=rRNA;gene=LOC111591002;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856667.1
chr2 cmsearch rRNA 227611359 227611477 . + . ID=rna-XR_004856752.1;Parent=gene-LOC111591093;Dbxref=GeneID:111591093,RFAM:RF00001,GenBank:XR_004856752.1;Name=XR_004856752.1;gbkey=rRNA;gene=LOC111591093;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856752.1
```
 
#### 2) Split the file by rRNA species 
 
```bash
grep  18S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff

grep  28S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff

grep   5S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff

grep 5.8S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff
```
 
#### 3) convert from GFF to BED files 

```bash
cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff | sed 's/;/\t/g' | awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' - > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed
```

##### expected output 

```bash
==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed <==
chr6	16749900	16751710	18S-ID=rna-XR_004851001.1	.	-
chr6	16758696	16760506	18S-ID=rna-XR_004851002.1	.	-
chr6	16770207	16772017	18S-ID=rna-XR_004851003.1	.	-

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed <==
chr6	16745916	16749299	28S-ID=rna-XR_004850581.1	.	-
chr6	16754714	16758096	28S-ID=rna-XR_004850541.1	.	-
chr6	16775232	16778609	28S-ID=rna-XR_004850751.1	.	-

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed <==
chr6	16749525	16749680	5.8S-ID=rna-XR_004850911.1	.	-
chr6	16758321	16758476	5.8S-ID=rna-XR_004850922.1	.	-
chr6	16769833	16769988	5.8S-ID=rna-XR_004850933.1	.	-

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed <==
chr1	45918126	45918244	5S-ID=rna-XR_004855769.1	.	+
chr1	78523371	78523489	5S-ID=rna-XR_004855692.1	.	+
chr2	227610721	227610839	5S-ID=rna-XR_004856556.1	.	+
```

 #### 4) Extract the fasta sequences from the genome assembly

```bash
for f in rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.*.bed
do
ls $f
bedtools getfasta -fi /work2/03302/lconcia/stampede2/references/maize/Zm-B73-REFERENCE-NAM-5.0_without_scaffold.fa -bed  $f -fo $(basename $f bed)fasta
done
```

-rw-r--r-- 1 lconcia G-815499  38K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed
-rw-r--r-- 1 lconcia G-815499  34K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed
-rw-r--r-- 1 lconcia G-815499  31K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed
-rw-r--r-- 1 lconcia G-815499  31K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed





chr10   34699901    34701711    18S-ID=rna-XR_004853520.1   .   +
chr6    16749900    16751710    18S-ID=rna-XR_004851001.1   .   -

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed <==
chr10   34702309    34705691    28S-ID=rna-XR_004853522.1   .   +
chr6    16745916    16749299    28S-ID=rna-XR_004850581.1   .   -

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed <==
chr6    16749525    16749680    5.8S-ID=rna-XR_004850911.1  .   -
chr6    16758321    16758476    5.8S-ID=rna-XR_004850922.1  .   -

==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed <==
chr1    45918126    45918244    5S-ID=rna-XR_004855769.1    .   +
chr1    78523371    78523489    5S-ID=rna-XR_004855692.1    .   +



############################################################################################################################################################
############################################################################################################################################################


### blast the annotated subunits 


### extract the fasta 

bedtools getfasta \
-fi /work2/03302/lconcia/stampede2/references/maize/Zm-B73-REFERENCE-NAM-5.0_without_scaffold.fa \
-bed  $f \
-fo   fasta_files/`basename $f bed`fasta
done


-rw-r--r-- 1 lconcia G-815499  38K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed
-rw-r--r-- 1 lconcia G-815499  34K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed
-rw-r--r-- 1 lconcia G-815499  31K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed
-rw-r--r-- 1 lconcia G-815499  31K Jul 21 18:16 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed


module load tacc-apptainer 


sed 's/-scaffold_[0-9]\{1,3\}//g' < test

sed 's/-scaffold_[0-9]\{1,3\}//g' < /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.fasta 
> /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta

###

time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed \
-fo   rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.fasta

real    0m9.478s
user    0m7.917s
sys 0m0.673s


time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed \
-fo   rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.fasta

real    0m1.144s
user    0m0.182s
sys 0m0.095s


time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed \
-fo   rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.fasta

real    0m1.158s
user    0m0.183s
sys 0m0.097s


time apptainer exec /work2/03302/lconcia/sif_files/bedtools_2.31.0.sif bedtools getfasta -fi /work2/03302/lconcia/references/maize/Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta \
-bed  rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed \
-fo   rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta

real    0m1.151s
user    0m0.164s
sys 0m0.094s


   1134 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta
   1112 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.fasta
   1214 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.fasta
   1394 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.fasta


grep -c ">" *fasta
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta:567
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.fasta:556
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.fasta:607
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.fasta:697




```bash
  
```


