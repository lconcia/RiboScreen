# SRP

# concatenate al maize SRP sequences

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

 
# linearize the multifasta, replace U with T in case-insensitive mode, and capitalize sequences (even lines)

cat SRP_RNA.fasta | awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' | \
sed -e 's/-//g' | tr "\t" "\n"  | sed -e'2~2 s/a/A/g' -e '2~2 s/c/C/g' -e '2~2 s/g/G/g' -e '2~2 s/[ut]/T/g' > SRP_RNA.lin.fasta 


# count the characters in the sequences (even rows) and add it to the sequence name (previous odd row)

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
