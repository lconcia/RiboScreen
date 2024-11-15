# 04_BLAST_over_B73_AGPv5

#### 

#### 1) Install ab-BLAST

AB-BLAST is available <ins> at no cost</ins> for academic users at this address https://blast.advbiocomp.com/ 

#### 2) Create the BLAST database
```bash
xdformat -n Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta

##### expected output
 11K   Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta.xnd
521M   Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta.xns
6.2K   Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta.xnt
``` 
#### 3) BLAST the queries against the database

```bash
##### tRNAs 
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Zea_mays.Nuclear.fa cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links hspsepQmax=30 hspsepSmax=30 sump pingpong warnings > tRNAs.Nuclear_vs_AGPv5.reblast.out 

##### Mitochondrial genome 
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Mt.NC_007982.1.fasta cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings >  Mt.vs_AGPv5.reblast.out 

##### Chloroplast genome
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Chl.NC_001666.2.fasta cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings > Chl.vs_AGPv5.reblast.out

##### SRP
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta SRP_RNA.lin.lenght.fasta cpus=48 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings > SRP.Zea_Mays_vs_AGPv5.reblast.out  

##### 28S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 28S.Zea_Mays_vs_AGPv5.reblast.out

##### 18S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 18S.Zea_Mays_vs_AGPv5.reblast.out  

##### 5.8S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 5.8S.Zea_Mays_vs_AGPv5.reblast.out  

##### 5S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 5S.Zea_Mays_vs_AGPv5.reblast.out  

```
