# Formatting of the BLAST queries 
This file describe the identification of rRNA sequences from B73 genome assembly

##  rRNAs (ribosomal  subunits)
#### 1) Extract rRNA features from the annotation
```bash
$ awk '$3=="rRNA" ' Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff

##### expected output
chr1 cmsearch rRNA  45918126  45918244 . + . ID=rna-XR_004855769.1;Parent=gene-LOC111590732;Dbxref=GeneID:111590732,RFAM:RF00001,GenBank:XR_004855769.1;Name=XR_004855769.1;gbkey=rRNA;gene=LOC111590732;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855769.1
chr1 cmsearch rRNA  78523371  78523489 . + . ID=rna-XR_004855692.1;Parent=gene-LOC111590622;Dbxref=GeneID:111590622,RFAM:RF00001,GenBank:XR_004855692.1;Name=XR_004855692.1;gbkey=rRNA;gene=LOC111590622;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004855692.1
chr2 cmsearch rRNA 227610721 227610839 . + . ID=rna-XR_004856556.1;Parent=gene-LOC111590902;Dbxref=GeneID:111590902,RFAM:RF00001,GenBank:XR_004856556.1;Name=XR_004856556.1;gbkey=rRNA;gene=LOC111590902;inference=COORDINATES: profile:INFERNAL:1.1.1;product=5S ribosomal RNA;transcript_id=XR_004856556.1
(...)
```
 
#### 2) Extract specific rRNA species 
```bash
$ grep  18S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff
$ grep  28S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff
$ grep   5S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff
$ grep 5.8S rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff
```
 
#### 3) convert from GFF to BED files 
```bash
$ cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.gff | sed 's/;/\t/g' | awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed
$ cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.bed
$ cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.gff | sed 's/;/\t/g' |  awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }' > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.bed
$ cat rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.gff | sed 's/;/\t/g' | awk '{OFS="\t"}{print $1,$4,$5,$17"-"$9,$6,$7 }'  > rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.bed

##### expected output (rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.bed)
chr1	45918126	45918244	5S-ID=rna-XR_004855769.1	.	+
chr1	78523371	78523489	5S-ID=rna-XR_004855692.1	.	+
chr2	227610721	227610839	5S-ID=rna-XR_004856556.1	.	+
chr2	227611040	227611158	5S-ID=rna-XR_004856667.1	.	+
(...)
```
#### 4) Extract the corresponding rRNA sequences from the genome 
```bash
for f in rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.*.bed
do
ls $f
bedtools getfasta -fi Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta -bed  $f -fo $(basename $f bed)fasta
done

##### expected output
==> rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta <==
>chr6:16749900-16751710
aatgatccttccgcaggttcacctacggaaaccttgttacgacttctccttcctctaaatgataaggttcaatggacttctcgcgacgtcgggggcggcgaaccgcccccgtcgccgcgatccgaacacttcaccggaccattcaatcggtaggagcgacgggcggtgtgtacaaagggcagggacgtagtcaacgcgagctgatgactcgcgcttactaggcattcctcgttgaagaccaacaattgcaatgatctatccccatcacgatgaaatttcccaagattacccgggcctgtcggccaaggctatatactcgttggatacatcagtgtagcgcgcgtgcggcccagaacatctaagggcatcacagacctgttattgcctcaaacttccgtggcctaaacggccatagtccctctaagaagctaactacggagggatggctccgcatagctagttagcaggctgaggtctcgttcgttaacggaattaaccagacaaatcgctccaccaactaagaacggccatgcaccaccacccatagaatcaagaaagagctctcagtctgtcaatccttgctatgtctggacctggtaagtttccccgtgttgagtcaaattaagccgcaggctccacgcctggtggtgcccttccgtcaattcctttaagtttcagccttgcgaccatactccccccggaacccaaagactttgatttctcataaggtgccagcggggtcctattagtaacacccgctgatccctggtcggcatcgtttatggttgagactaggacggtatctgatcgtcttcgagcccccaactttcgttcttgattaatgaaaacatccttggcaaatgctttcgcagttgttcgtctttcataaatccaagaatttcacctctgactatgaaatacgaatgcccccgactgtccctattaatcattactccgatcccgaaggccaacacaataggaccggaatcctatgatgttatcccatgctaatgtatccagagcgatggcttgctttgagcactctaatttcttcaaagtaacggcgccggaggcacgacccggccagttaaggccaggagcgcatcgccggcagaagggtcgagccggtcggttctcgccgtgaggcggaccggccggcccggcccaaggtccaactacgagctttttaactgcaacaacttaaatatacgctattggagctggaattaccgcggctgctggcaccagacttgccctccaatggatcctcgttaagggatttagattgtactcattccaattaccagacactaacgcgcccggtattgttatttattgtcactacctccccgtgtcaggattgggtaatttgcgcgcctgctgccttccttggatgtggtagccgtttctcaggctccctctccggaatcgaaccctaattctccgtcacccgtcaccaccatggtaggcccctatcctaccatcgaaagttgatagggcagaaatttgaatgatgcgtcgccggcacgaaggccgtgcgatccgtcaagttatcatgaatcatcggatcggcgggcagagcccgcgtcagccttttatctaataaatgcgcccctcccggaagtcggggtttgttgcacgtattagctctagaattactacggttatccgagtagcacgtaccatcaaacaaactataactgatttaatgagccattcgcagtttcacagttcgaattagttcatacttgcacatgcatggcttaatctttgagacaagcatatgactactggcaggatcaaccaggta
>chr6:16758696-16760506
aatgatccttccgcaggttcacctacggaaaccttgttacgacttctccttcctctaaatgataaggttcaatggacttctcgcgacgtcgggggcggcgaaccgcccccgtcgccgcgatccgaacacttcaccggaccattcaatcggtaggagcgacgggcggtgtgtacaaagggcagggacgtagtcaacgcgagctgatgactcgcgcttactaggcattcctcgttgaagaccaacaattgcaatgatctatccccatcacgatgaaatttcccaagattacccgggcctgtcggccaaggctatatactcgttggatacatcagtgtagcgcgcgtgcggcccagaacatctaagggcatcacagacctgttattgcctcaaacttccgtggcctaaacggccatagtccctctaagaagctaactacggagggatggctccgcatagctagttagcaggctgaggtctcgttcgttaacggaattaaccagacaaatcgctccaccaactaagaacggccatgcaccaccacccatagaatcaagaaagagctctcagtctgtcaatccttgctatgtctggacctggtaagtttccccgtgttgagtcaaattaagccgcaggctccacgcctggtggtgcccttccgtcaattcctttaagtttcagccttgcgaccatactccccccggaacccaaagactttgatttctcataaggtgccagcggggtcctattagtaacacccgctgatccctggtcggcatcgtttatggttgagactaggacggtatctgatcgtcttcgagcccccaactttcgttcttgattaatgaaaacatccttggcaaatgctttcgcagttgttcgtctttcataaatccaagaatttcacctctgactatgaaatacgaatgcccccgactgtccctattaatcattactccgatcccgaaggccaacacaataggaccggaatcctatgatgttatcccatgctaatgtatccagagcgatggcttgctttgagcactctaatttcttcaaagtaacggcgccggaggcacgacccggccagttaaggccaggagcgcatcgccggcagaagggtcgagccggtcggttctcgccgtgaggcggaccggccggcccggcccaaggtccaactacgagctttttaactgcaacaacttaaatatacgctattggagctggaattaccgcggctgctggcaccagacttgccctccaatggatcctcgttaagggatttagattgtactcattccaattaccagacactaacgcgcccggtattgttatttattgtcactacctccccgtgtcaggattgggtaatttgcgcgcctgctgccttccttggatgtggtagccgtttctcaggctccctctccggaatcgaaccctaattctccgtcacccgtcaccaccatggtaggcccctatcctaccatcgaaagttgatagggcagaaatttgaatgatgcgtcgccggcacgaaggccgtgcgatccgtcaagttatcatgaatcatcggatcggcgggcagagcccgcgtcagccttttatctaataaatgcgcccctcccggaagtcggggtttgttgcacgtattagctctagaattactacggttatccgagtagcacgtaccatcaaacaaactataactgatttaatgagccattcgcagtttcacagttcgaattagttcatacttgcacatgcatggcttaatctttgagacaagcatatgactactggcaggatcaaccaggta
>chr6:16770207-16772017
aatgatccttccgcaggttcacctacggaaaccttgttacgacttctccttcctctaaatgataaggttcaatggacttctcgcgacgtcgggggcggcgaaccgcccccgtcgccgcgatccgaacacttcaccggaccattcaatcggtaggagcgacgggcggtgtgtacaaagggcagggacgtagtcaacgcgagctgatgactcgcgcttactaggcattcctcgttgaagaccaacaattgcaatgatctatccccatcacgatgaaatttcccaagattacccgggcctgtcggccaaggctatatactcgttggatacatcagtgtagcgcgcgtgcggcccagaacatctaagggcatcacagacctgttattgcctcaaacttccgtggcctaaacggccatagtccctctaagaagctaactacggagggatggctccgcatagctagttagcaggctgaggtctcgttcgttaacggaattaaccagacaaatcgctccaccaactaagaacggccatgcaccaccacccatagaatcaagaaagagctctcagtctgtcaatccttgctatgtctggacctggtaagtttccccgtgttgagtcaaattaagccgcaggctccacgcctggtggtgcccttccgtcaattcctttaagtttcagccttgcgaccatactccccccggaacccaaagactttgatttctcataaggtgccagcggggtcctattagtaacacccgctgatccctggtcggcatcgtttatggttgagactaggacggtatctgatcgtcttcgagcccccaactttcgttcttgattaatgaaaacatccttggcaaatgctttcgcagttgttcgtctttcataaatccaagaatttcacctctgactatgaaatacgaatgcccccgactgtccctattaatcattactccgatcccgaaggccaacacaataggaccggaatcctatgatgttatcccatgctaatgtatccagagcgatggcttgctttgagcactctaatttcttcaaagtaacggcgccggaggcacgacccggccagttaaggccaggagcgcatcgccggcagaagggtcgagccggtcggttctcgccgtgaggcggaccggccggcccggcccaaggtccaactacgagctttttaactgcaacaacttaaatatacgctattggagctggaattaccgcggctgctggcaccagacttgccctccaatggatcctcgttaagggatttagattgtactcattccaattaccagacactaacgcgcccggtattgttatttattgtcactacctccccgtgtcaggattgggtaatttgcgcgcctgctgccttccttggatgtggtagccgtttctcaggctccctctccggaatcgaaccctaattctccgtcacccgtcaccaccatggtaggcccctatcctaccatcgaaagttgatagggcagaaatttgaatgatgcgtcgccggcacgaaggccgtgcgatccgtcaagttatcatgaatcatcggatcggcgggcagagcccgcgtcagccttttatctaataaatgcgcccctcccggaagtcggggtttgttgcacgtattagctctagaattactacggttatccgagtagcacgtaccatcaaacaaactataactgatttaatgagccattcgcagtttcacagttcgaattagttcatacttgcacatgcatggcttaatctttgagacaagcatatgactactggcaggatcaaccaggta
(...)
```
##  Signal recognition particles (SRPs)
#### 1) Download the available sequences
```bash
wget https://rth.dk/resources/rnp/SRPDB/rna/sequences/srprna_seqs.zip
````
#### concatenate the maize sequences
```bash
cat \
Zea.mays._AY108846.fasta \
Zea.mays._CB179518.fasta \
Zea.mays._CD526459.fasta \
Zea.mays._CF602412.fasta \
Zea.mays._CF919998.fasta \
Zea.mays._CF974802.fasta \
Zea.mays._CK327537.fasta \
Zea.mays._CK700970.fasta \
Zea.mays._SDB-381124-1.fasta \
Zea.mays._SDB-381124-2.fasta \
Zea.mays._SDB-381124-3.fasta \
Zea.mays._SDB-381124-4.fasta \
Zea.mays._SDB-381124-5.fasta \
Zea.mays._SDB-381124-6.fasta \
Zea.mays._SDB-381124-7.fasta \
Zea.mays._SDB-381124-8.fasta \
Zea.mays._X14661.fasta > \
SRP_RNA.fasta
```  
#### linearize the multifasta
```awk
cat SRP_RNA.fasta | \
awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;}
{printf("%s",$0);} END
{printf("\n");}' 
```
  
* 3) 

replace U with T in case-insensitive mode, and capitalize sequences (even lines)

```
| \
sed -e 's/-//g' | \
tr "\t" "\n"  | \
sed -e'2\~2 s/a/A/g' -e '2\~2 s/c/C/g' -e '2\~2 s/g/G/g' -e '2\~2 s/[ut]/T/g' > SRP_RNA.lin.fasta 
```

 
* 3) count the characters in the sequences (even rows) and add it to the sequence name (previous odd row)

```bash
#!/bin/bash

# Input file name
input_file=$1

## fasta_lenght.sh

# Remove the file extension from the input file
file_extension="${input_file##*.}"
file_name_without_extension="${input_file%.*}"

# Append the characters count to the filename
new_file_name="${file_name_without_extension}.lenght.${file_extension}"

# Temporary file to store the modified content
# output_file=$new_file_name

# Loop through each line in the input file
line_number=0
while IFS= read -r line || [[ -n "$line" ]]; do
    ((line_number++))

    # Check if the line number is odd
    if ((line_number % 2 == 1)); then
        # Calculate the length of the following line
        next_line=$(sed -n $((line_number + 1))p "$input_file")
        next_line_length=${#next_line}

        # Add the length of the following line to the current line
        line+=":1-$next_line_length"
    fi

    # Append the modified line to the output file
    echo "$line" >> "$new_file_name"
done < "$input_file"
```


* run the script 
```bash
fasta_lenght.sh SRP_RNA.lin.fasta
```

* 4) verify

```bash
head   /work2/03302/lconcia/references/maize/sortMeRNA_db/SRP_db/fasta/SRP_RNA.lin.lenght.fasta
```

## PASTE THE CORRECT FASTA
>Zea.mays._AY108846:1-287
TGTAACCCGAGAGUGGGGGCATTAAGGUGGUGCGGAUGCUUTGCGATGGCUUUCTGGGCCUGGGCUCGCTTGUGCCTTTGGCCGGCCUGCCCGUCCCAAGUUGGTGGUGGCUGGCGGAGGCCTTAGCGGAAGCUTTGGUCUCUCC

>Zea.mays._CB179518:1-307
CCGAGCUCAGTTGCGAGAGCUUGTAACCCGAGCGGGGGCATTAAAGGUGGUGCGGAUGCUUTCUGACGGCUUCTGGGCCUGGGCCCATATGUGCCACTAGGCUGGACUGUCCAUCCCAAGUUGGAAGUGUCUGGCGCGGAUUAGG

>Zea.mays._CD526459:1-308
CCGAGUUCUGTAGCUAGAGCUUGTAACCCGAGCGGGGGCATTAAGGUGGCGCGGAUGCUUTGCGATGGUUUCTGGGCCUGGGCUCGTTGnGACTCTAGCCGGCUUTGCCCAUCCCAAGUUGGTTGUGUCUGGCGGGGCUCTAGCG$

>Zea.mays._CF602412:1-303
CCGAGCUCAGTAGCGAGAGCUUGTAACCUAAGCAGGGGCATTAAGGUGGUGTGGAUGUUUCCUGACGGAUUATGGGCCUGGGCCUGTATGUGCCACTTGCCGGCCUGCCCGUUCCGAGUUGGTTGUGGCUGGTGGGGCUCGGGCG$






