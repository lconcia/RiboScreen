
# Assembly formatting 

#### 1) Download the assembly from NBCI 

```bash
$ wget -O - https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/902/167/145/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fna.gz | gunzip -d > GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta
```

#### 2) Verify the chromosome names 

```bash
$ grep ">" GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta
```
```bash
>NC_050096.1 Zea mays cultivar B73 chromosome 1, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050097.1 Zea mays cultivar B73 chromosome 2, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050098.1 Zea mays cultivar B73 chromosome 3, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050099.1 Zea mays cultivar B73 chromosome 4, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050100.1 Zea mays cultivar B73 chromosome 5, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050101.1 Zea mays cultivar B73 chromosome 6, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050102.1 Zea mays cultivar B73 chromosome 7, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050103.1 Zea mays cultivar B73 chromosome 8, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050104.1 Zea mays cultivar B73 chromosome 9, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NC_050105.1 Zea mays cultivar B73 chromosome 10, Zm-B73-REFERENCE-NAM-5.0, whole genome shotgun sequence
>NW_023366717.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_100, whole genome shotgun sequence
>NW_023366718.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_101, whole genome shotgun sequence
>NW_023366719.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_102, whole genome shotgun sequence
(...)
>NW_023367389.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_97, whole genome shotgun sequence
>NW_023367390.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_98, whole genome shotgun sequence
>NW_023367391.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_99, whole genome shotgun sequence
>NC_007982.1 Zea mays subsp. mays mitochondrion, complete genome
>NC_001666.2 Zea mays chloroplast, complete genome
```

#### 3) Edit the chromosome names 

```awk
 awk '{ if ($0 ~ /mitochondrion/) print ">chrM" ; 
        else if ($0 ~ /chloroplast/) print ">chrC";
             else if ($1 ~ /NC_/) print ">chr"substr($7, 1, length($7)-1);
                  else if ($1 ~ /NW_/) print $1"-"substr($10, 1, length($10)-1);
                       else print;
       }' GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta > \
Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.fasta
```

#### 4) Verify the new chromosome names 

```bash
$ grep ">" Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.fasta
```
```bash
