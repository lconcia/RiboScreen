#### This file describe the identification of rRNA sequences from B73 genome assembly
### test4
#### test3
##### test2
###### test1

#### 1) Extract rRNA features from the annotation

```bash
$ awk ' $3=="rRNA" '  Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```
##### Inspect the output

```bash
$ wc -l rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
2428
```
```bash
head -n 5 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```
```bash
chr1 cmsearch rRNA  45918126  45918244 . + . ID=rna-XR_004855769.1;Parent=gene-LOC111590732;Dbxref=GeneID:111590732,RFAM:RF00001,GenBank:XR_004855769.1;Name=XR_004855769.1;gbkey=rRNA;gene=LOC111590732;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855769.1
chr1 cmsearch rRNA  78523371  78523489 . + . ID=rna-XR_004855692.1;Parent=gene-LOC111590622;Dbxref=GeneID:111590622,RFAM:RF00001,GenBank:XR_004855692.1;Name=XR_004855692.1;gbkey=rRNA;gene=LOC111590622;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855692.1
chr2 cmsearch rRNA 227610721 227610839 . + . ID=rna-XR_004856556.1;Parent=gene-LOC111590902;Dbxref=GeneID:111590902,RFAM:RF00001,GenBank:XR_004856556.1;Name=XR_004856556.1;gbkey=rRNA;gene=LOC111590902;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856556.1
chr2 cmsearch rRNA 227611040 227611158 . + . ID=rna-XR_004856667.1;Parent=gene-LOC111591002;Dbxref=GeneID:111591002,RFAM:RF00001,GenBank:XR_004856667.1;Name=XR_004856667.1;gbkey=rRNA;gene=LOC111591002;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856667.1
chr2 cmsearch rRNA 227611359 227611477 . + . ID=rna-XR_004856752.1;Parent=gene-LOC111591093;Dbxref=GeneID:111591093,RFAM:RF00001,GenBank:XR_004856752.1;Name=XR_004856752.1;gbkey=rRNA;gene=LOC111591093;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856752.1
```



```bash
  
```
#### 1) Split the file by rRNA species ( "product")


## also, delete ChrM and ChrC --> should not be there

```bash
cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff | grep -v 'chrC\|chrM' |  sed 's/product=/\t/g; s/ribosomal /\t/g' | cut -f 10 | sort | uniq -c 
```

   1 17S 
 566 18S 
   1 26S 
 556 28S 
 607 5.8S 
 697 5S 

```bash
cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff | sed 's/product=/\t/g; s/ribosomal /\t/g' | gawk '{print >> "rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds."$11".gff"; close($11)}'
```

   566 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff
   556 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff
   607 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff
   697 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff
     2 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.transcript.gff

  2428 total




less rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.transcript.gff

chr6    BestRefSeq      rRNA    17356482        17392299        .       -       .       ID=rna-NR_028022.2;Parent=gene-LOC100275739;Dbxref=GeneID:100275739,GenBank:NR_028022.2;Name=NR_028022.2;Note=The RefSeq transcript has 30 substitutions%2C 7 non-frameshifting indels compared to this genomic sequence;exception=annotated by transcript or proteomic data;gbkey=rRNA;gene=LOC100275739;inference=similar to RNA sequence (same species):RefSeq:NR_028022.2;  26S     RNA gene;transcript_id=NR_028022.2
NW_023366867.1  BestRefSeq      rRNA    2       1413    .       -       .       ID=rna-NR_036655.1;Parent=gene-LOC100502571;Dbxref=GeneID:100502571,GenBank:NR_036655.1;Name=NR_036655.1;Note=The RefSeq transcript has 5 substitutions%2C 5 non-frameshifting indels and aligns at 77%25 coverage compared to this genomic sequence;exception=annotated by transcript or proteomic data;gbkey=rRNA;gene=LOC100502571;inference=similar to RNA sequence (same species):RefSeq:NR_036655.1;partial=true; 17S     RNA gene;start_range=.,2;transcript_id=NR_036655.1


###    the 26s -> add to 28S  ->> no!  it is long 35-kb,  DO NOT ADD 

 mv rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff.tmp

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff.tmp <(grep 26S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.transcript.gff) | \
sort -k1,1 -k4,4n >> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff

   557 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff
   556 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff.tmp





###    the 17s -> add to 18S
   
mv rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff.tmp

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff.tmp <(grep 17S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.transcript.gff) | sort -k1,1 -k4,4n >> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff



   567 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff
   556 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff
   607 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff
   697 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff

     2 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.transcript.gff


## OK!!


############################################################################################################################################################
############################################################################################################################################################


### transform al to BED files 

chr6    cmsearch    rRNA    16797219    16799029    .   -   .   ID=rna-XR_004851007.1;Parent=gene-LOC118472744;Dbxref=GeneID:118472744,RFAM:RF01960,GenBank:XR_004851007.1;Name=XR_004851007.1;gbkey=rRNA;gene=LOC118472744;inference=COORDINATES: profile:INFERNAL:1.1.1;  18S RNA;transcript_id=XR_004851007.1




cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed

cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff | sed 's/;/\t/g' | awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' - > \
rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed


   567 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed
   556 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed
   607 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed
   697 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed



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


