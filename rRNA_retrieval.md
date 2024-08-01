## This file describe the identification of rRNA sequences from B73 genome assembly

```bash
$ awk ' $3=="rRNA" '  Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```

```bash
$ wc -l rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
2428
```





```bash
head -n 5 rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```
```bash
chr1	cmsearch	rRNA	45918126	45918244	.	+	.
ID=rna-XR_004855769.1;Parent=gene-LOC111590732;Dbxref=GeneID:111590732,RFAM:RF00001,GenBank:XR_004855769.1;Name=XR_004855769.1;gbkey=rRNA;gene=LOC111590732;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855769.1
chr1	cmsearch	rRNA	78523371	78523489	.	+	.	ID=rna-XR_004855692.1;Parent=gene-LOC111590622;Dbxref=GeneID:111590622,RFAM:RF00001,GenBank:XR_004855692.1;Name=XR_004855692.1;gbkey=rRNA;gene=LOC111590622;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855692.1
chr2	cmsearch	rRNA	227610721	227610839	.	+	.	ID=rna-XR_004856556.1;Parent=gene-LOC111590902;Dbxref=GeneID:111590902,RFAM:RF00001,GenBank:XR_004856556.1;Name=XR_004856556.1;gbkey=rRNA;gene=LOC111590902;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856556.1
chr2	cmsearch	rRNA	227611040	227611158	.	+	.	ID=rna-XR_004856667.1;Parent=gene-LOC111591002;Dbxref=GeneID:111591002,RFAM:RF00001,GenBank:XR_004856667.1;Name=XR_004856667.1;gbkey=rRNA;gene=LOC111591002;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856667.1
chr2	cmsearch	rRNA	227611359	227611477	.	+	.	ID=rna-XR_004856752.1;Parent=gene-LOC111591093;Dbxref=GeneID:111591093,RFAM:RF00001,GenBank:XR_004856752.1;Name=XR_004856752.1;gbkey=rRNA;gene=LOC111591093;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856752.1
```



```bash
  
```


############### split by type or rDNA species == "product"

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




