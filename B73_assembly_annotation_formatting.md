
# Assembly formatting 

#### 1) Download the assembly from NBCI 

```bash
$ wget -O - https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/902/167/145/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0/GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fna.gz | gunzip -d > GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta
```

#### 2) Verify the chromosome names 

```bash
$ grep ">" GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta
```
```
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


# Change the chromosome names but not the scaffold names

# first chanhge Mt and Chl with sed

sed 's/NC_007982.1/chrM/g; s/NC_001666.2/chrC/g' < GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta > 


## if reference (chromosome) names start with >NC (full chromosomes and Chl and Mt) print chromosome number (seventh field), othervise print full line.

## do a ladder of if-else 

#  awk '{ if ($1 ~ /mitochondrion/) { print "chrM" } else { if ($1 ~ /chloroplast/) print "chrC"} }' INPUT | head 

# change the manes of reference as follows :
## if the row containes "mitochondrion", print "chrM", else
## if the row containes "chloroplast",   print "chrC", else
## if the row starts with  ">NC_", print "chr" and field 7 (chromosome number), else
## if the row starts with  ">NW_", print $1 (scaffold accession), then "-", then $10 (scaffold name), else
## print full row (actual DNA sequence)

##  scaffold reference names in GFF and GFT are like this "NW_023367380.1", need to leave it in the name of scaffolds

##  to avoid printing the comma after the chromosome number and scaffold name (p.es "chr1" instad of "chr1,") 
##  use substr (substring) 
##  
##  awk '{ for (i = 1; i <= NF; i++) { printf "%s ", substr($i, 1, length($i)-1) } printf "\n" }' input_file
##  
##  substr(string,position,length)
  
##  awk '{ if ($0 ~ /mitochondrion/) print ">chrM" ; 
##  
##         else if ($0 ~ /chloroplast/) print ">chrC";
##                  
##               else if ($1 ~ /NC_/) print ">chr"substr($7, 1, length($7)-1);
##  
##                      else if ($1 ~ /NW_/) print $1"-"substr($10, 1, length($10)-1);
##                            
##                             else print;
##  
##                              }' test > test2
##  
##   #  ok !!


 awk '{ if ($0 ~ /mitochondrion/) print ">chrM" ; 

       else if ($0 ~ /chloroplast/) print ">chrC";
                
             else if ($1 ~ /NC_/) print ">chr"substr($7, 1, length($7)-1);

                    else if ($1 ~ /NW_/) print $1"-"substr($10, 1, length($10)-1);
                          
                           else print;

                            }' GCF_902167145.1_Zm-B73-REFERENCE-NAM-5.0_genomic.fasta > Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.fasta



                     extract from formatted genome assembly <br />  Zm-B73-REFERENCE-NAM-5.0_genomic.with_scaffolds.fasta <br /> using coordinates of rRNA subunits in formatted NCBI annotation <br />  Zm-B73-REFERENCE-NAM-5.0_genomic.no_scaffolds.gff        
