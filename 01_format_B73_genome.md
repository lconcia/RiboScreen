
# Format the genome assembly  

#### 1) Download the assembly from NCBI 

```bash
$ wget -O - https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/902/167/145/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fna.gz | gunzip -d > GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta
```

#### 2) Verify the chromosome names of the assembly

```bash
$ grep ">" GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta

#### expected output
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
(...)
>NW_023367390.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_98, whole genome shotgun sequence
>NW_023367391.1 Zea mays cultivar B73 unplaced genomic scaffold, Zm-B73-REFERENCE-NAM-5.0 scaffold_99, whole genome shotgun sequence
>NC_007982.1 Zea mays subsp. mays mitochondrion, complete genome
>NC_001666.2 Zea mays chloroplast, complete genome
```

#### 3) Edit the chromosome names of the assembly

```awk
 awk '{ if ($0 ~ /mitochondrion/) print ">chrM" ; 
        else if ($0 ~ /chloroplast/) print ">chrC";
             else if ($1 ~ /NC_/) print ">chr"substr($7, 1, length($7)-1);
                  else if ($1 ~ /NW_/) print $1;
                       else print;
       }' GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta > \
                          Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta
```

#### 4) Verify the new chromosome names of the assembly

```bash
$ grep ">" Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta

#### expected output
>chr1
>chr2
>chr3
>chr4
>chr5
>chr6
>chr7
>chr8
>chr9
>chr10
>NW_023366717.1
>NW_023366718.1
(...)
>NW_023367390.1
>NW_023367391.1

>chrM
>chrC
```

# Format the genome  annotation 

#### 1) Download the annotation from NCBI 

```bash
$ wget -O - https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/902/167/145/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.gff.gz | gunzip -d > GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.gff
```

#### 2) Verify the chromosome names of the annotation

```bash
$ cut -f 1 GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.gff | grep -v \# | sort | uniq 

#### expected output
NC_001666.2
NC_007982.1
NC_050096.1
NC_050097.1
NC_050098.1
NC_050099.1
NC_050100.1
NC_050101.1
NC_050102.1
NC_050103.1
NC_050104.1
NC_050105.1
NW_023366717.1
NW_023366718.1
(...)
NW_023367390.1
NW_023367391.1
```

#### 3) Edit the chromosome names of the annotation

```bash
$ cat GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.gff | sed \
's/NC_050096.1/chr1/g; 
 s/NC_050097.1/chr2/g; 
 s/NC_050098.1/chr3/g; 
 s/NC_050099.1/chr4/g; 
 s/NC_050100.1/chr5/g; 
 s/NC_050101.1/chr6/g; 
 s/NC_050102.1/chr7/g; 
 s/NC_050103.1/chr8/g; 
 s/NC_050104.1/chr9/g; 
 s/NC_050105.1/chr10/g; 
 s/NC_007982.1/chrM/g; 
 s/NC_001666.2/chrC/g' | grep -v 'chrC\|chrM' >  Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.gff
```
