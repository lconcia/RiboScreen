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
##### 28S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.28S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 28S.Zea_Mays_vs_AGPv5.reblast.out

##### 18S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.18S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 18S.Zea_Mays_vs_AGPv5.reblast.out  

##### 5.8S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5.8S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 5.8S.Zea_Mays_vs_AGPv5.reblast.out  

##### 5S rDNA
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta rRNA.Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.5S.fasta cpus=24 M=+3 N=-7 E=1e-16  mformat=2 links hspsepQmax=100 hspsepSmax=3500 sump pingpong warnings > 5S.Zea_Mays_vs_AGPv5.reblast.out

##### SRP
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta SRP_RNA.lin.lenght.fasta cpus=48 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings > SRP.Zea_Mays_vs_AGPv5.reblast.out

##### tRNAs 
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Zea_mays.Nuclear.fa cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links hspsepQmax=30 hspsepSmax=30 sump pingpong warnings > tRNAs.Nuclear_vs_AGPv5.reblast.out 

##### Mitochondrial genome 
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Mt.NC_007982.1.fasta cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings >  Mt.vs_AGPv5.reblast.out 

##### Chloroplast genome
blastn Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.no_names.fasta Chl.NC_001666.2.fasta cpus=24 M=+3 N=-7 E=1e-16 mformat=2 links sump pingpong warnings > Chl.vs_AGPv5.reblast.out
```

#### 4) inspect the output


   70962 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Chl.vs_AGPv5.reblast.out
  118704 /work2/03302/lconcia/references/maize/sortMeRNA_db/ChlMt_db/Mt.vs_AGPv5.reblast.out
  150333 /work2/03302/lconcia/references/maize/sortMeRNA_db/tRNAs_db/tRNAs.Nuclear_vs_AGPv5.reblast.out


## filter ONLY by size
      
Chl     150 bp -> length of one read
Mt      150 bp -> length of one read
tRNAs    59 bp -> length of tRNA







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





