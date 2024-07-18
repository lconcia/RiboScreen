### formatting of SRP fasta 

* 1) concatenate al maize SRP sequences

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
  
     
* 2) linearize the multifasta

```awk
cat SRP_RNA.fasta | \
awk
# If the line starts with the pattern ">" (fasta headers), print the line preceded by a newline (\n),
# unless is the first line.
'/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;}
# If the line doest NOT start with the pattern ">" (fasta sequences),
# prints each line of input exactly as it is read, without adding newlines (\n) in between.
# This will print all the sequences on a single line.
{printf("%s",$0);}
# after the last line is read (END) print a newline to make sure the output ends with a newline character,
END
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



