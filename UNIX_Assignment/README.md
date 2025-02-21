# Unix_assignment       Swathi Nadendla           Feb 19 2025 

## This is divided into data inspection and data processing 
----------------------------------------------------------------------------------------------------------------------------------------------------------
Description from understanding of two files provided in biological context 
1. fang_et_al genotypes needs transposition and filtering which is done by using awk script provided 
2. snp_position file needs to be joined with genotype data 

# Data inspection
__________________________________________________________________________________________________________________________________________________________
## Description

In data inspection I have majorly focused on getting the file sizes, no.of lines and columns and how to have a sneak peek of data in a large file. Commands used and the output are described below

## Check file sizes
du -h fang_et_al_genotypes.txt 

du -h snp_position.txt

du command is human readable ls -lha can also be used it also gave information about permissions (read,write,execute)

# Count lines and columns
Lines

wc -l fang_et_al_genotypes.txt

wc -l snp_position.txt

Columns 

head -n 1 fang_et_al_genotypes.txt | awk -F'\t' '{print NF}'

head -n 1 snp_position.txt | awk -F'\t' '{print NF}'

# Peek at the first few lines
head -n 10 fang_et_al_genotypes.txt

head -n 10 snp_position.txt

tail -n 10 snp-position.txt

cat snp_positions.txt

less fang_etal_genotypes.txt

# Count unique chromosome values in snp_position.txt

cut -f3 snp_position.txt | sort | uniq -c

----------------------------------------------------------------------------------------------------------------------------------------------------------
## Output
1.File size
6.7M    fang_et_al_genotypes.txt,

49K     snp_position.txt

2.Number of lines 

2783 fang_et_al_genotypes.txt

984 snp_position.txt

3.Number of columns 

986 for genotypes file

15 for snp_position

4.head,tail,cat, less, more options

head -n 10 gave first 10 lines 
tail command gave the last 10 lines in the respective files 
whereas cat command print the entire contents of the files 
qmore and less help in getting a glance of the files.

5.Unique values 

  155 1
     53 10
    127 2
    107 3
     91 4
    122 5
     76 6
     97 7
     62 8
     60 9
      1 Chromosome
      6 multiple
     27 unknown
I have got information about ? values as missing : red flag data might be missing 
     
6.I have also checked for non-ASCI characters using this command 

grep -oP "[^\x00-\x7F]" fang_et_al_genotypes.txt | wc -l

grep -oP "[^\x00-\x7F]" snp_position.txt | wc -l

Count observed is zero 
 
----------------------------------------------------------------------------------------------------------------------------------------------------------

# Data Processing 
__________________________________________________________________________________________________________________________________________________________
## Description
In data processing I have focused on extracting, transposing,joining and filtering by chromosome number and handling missing/unknown values 
__________________________________________________________________________________________________________________________________________________________
## Extraction and sorting SNPS

checked the first 10 lines of both files in inspection to know which columns needed to be extracted

## Extraction 
cut -f 1,3,4 snp_position.txt > snp_position_extracts.txt

Reason for cutting *1,3,4 as they have SNP_id, chromosome, position on which they need to be sorted further 

## sort these SNP's (prep for join)

grep -v "SNP_ID" snp_position_extracts.txt|sort-k1,1snp_position_extracts.txt >snp_position_sorted.txt
__________________________________________________________________________________________________________________________________________________________

Now I have seperated maize and  teosinte from each other based on information in *3rd column using grep by ID's mentioned

----------------------------------------------------------------------------------------------------------------------------------------------------------
## For maize genotypes 
1. grep -E -w "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt >maize_genotypes.txt
Transpose the maize genotypes
2. awk -f transpose.awk\tmaize_3Genotypes.txt>transposed_maize_3genotypes.txt
3. sort -k1,1 snp_position_sorted.txt > snp_position_sorted_sorted.txt
4. sort -k1,1 transposed_maize_genotypes.txt>transposed_maize_genotypes_sorted.txt
5. join -1 1 -2 1 -t$'\t'snp_position_sorted_sorted.txttransposed_maize_genotypes_sorted.txt > maize_joined.txt
7.  sed 's/unknown/?/g' maize_joined.txt > maize_SNP_joined.txt
8. sed 's/unknown/?/g'maize_SNP_joined.txt|sort-k3,3n>maize_q_sorted.txt
9. awk '$2~ /1/' maize_q_sorted.txt > maize_increasing_chr1.txt
 awk '$2~ /2/' maize_q_sorted.txt > maize_increasing_chr2.txt
 awk '$2~ /3/' maize_q_sorted.txt > maize_increasing_chr3.txt
 awk '$2~ /4/' maize_q_sorted.txt > maize_increasing_chr4.txt
 awk '$2~ /5/' maize_q_sorted.txt > maize_increasing_chr5.txt
 awk '$2~ /6/' maize_q_sorted.txt > maize_increasing_chr6.txt
 awk '$2~ /7/' maize_q_sorted.txt > maize_increasing_chr7.txt
 awk '$2~ /8/' maize_q_sorted.txt > maize_increasing_chr8.txt
 awk '$2~ /9/' maize_q_sorted.txt > maize_increasing_chr9.txt
 awk '$2~ /10/' maize_q_sorted.txt > maize_increasing_chr10.txt
## For sorting in reverse -r option is used 
8.sed 's/unknown/-/g' maize_SNP_joined.txt | sort -k 3 -r -n > maize_rev_sorted.txt
9. awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr10.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr9.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr8.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr7.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr6.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr5.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr4.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr3.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr2.txt
   awk '$2~ /1/' maize_rev_sorted.txt > maize_decreasing_chr1.txt
   
----------------------------------------------------------------------------------------------------------------------------------------------------------
## for unkmown genotypes
 grep -w "unknown" maize_SNP_joined.txt > maize_unknown.txt
__________________________________________________________________________________________________________________________________________________________

## For multiple position 
 grep -w "multiple" maize_SNP_joined.txt > maize_multiple.txt
 
 _________________________________________________________________________________________________________________________________________________________
 
 ## For teosinte genotypes
 1. grep -E -w "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)"fang_et_al_genotypes.txt> teosinte_genotypes.txt
 2. awk -f transpose.awkteosinte_genotypes.txt>transposed_teosinte_genotypes.txt
 3. grep -v "Sample_ID" transposed_teosinte_genotypes.txt | sort -k1,1 > transposed_teosinte_genotypes_sorted.txt
 4. join -1 1 -2 1 -t $'\t' snp_position_sorted_sorted.txt transposed_teosinte_genotypes_sorted.txt > teosinte_joined.txt
 5. sed 's/unknown/?/g' teosinte_joined.txt > teosinte_SNP_joined.txt
 6. sed 's/unknown/?/g' teosinte_SNP_joined.txt | sort -k3,3n > teosinte_q_sorted.txt
 7. awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr1.txt
 awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr2.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr3.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr4.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr5.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr6.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr7.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr8.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr9.txt
awk '$2~ /1/' teosinte_q_sorted.txt > teosinte_increasing_chr10.txt
## sorting in reverse -r 
sed 's/unknown/-/g' teosinte_SNP_joined.txt | sort -k 3 -r -n > teosinte_rev-sorted.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr10.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr9.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr8.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr7.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr6.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr5.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr4.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr3.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr2.txt
awk '$2~ /1/' teosinte_rev-sorted.txt > teosinte_decreasing_chr1.txt
__________________________________________________________________________________________________________________________________________________________

## For unknown positions
grep -w "unknown" teosinte_SNP_joined.txt > teosinte_unknown.txt

## For multiple positions
grep -w "multiple" teosinte_SNP_joined.txt > teosinte_multiple.txt
__________________________________________________________________________________________________________________________________________________________

### Learnings from Data processing 
1. Throughout the process it was easier to use tab for auto completion of command, checking history of commands used, use upper arrow for the same command, using ls to check whether file is created or not using head, cat for checking whether it's sorted correctly or not 
2. I have also used rm with wildcards to remove multiple files when the sorting was not proper without /t 
3. Also, I have realized the importance of giving uniform names to avoid errors when using in the subsequent steps 
4. I have given the same command since its only for 10 chromosomes but I later thought to include a for loop there 
# For loop for increasing order (1 to 10)
for chr in {1..10}; do
  # Extract SNPs for each chromosome 
  awk -F'\t' -v chr="chr$chr" '$2 == chr' maize_SNP_with_question_mark.txt > "chr${chr}_snps.txt"
  
  # Sort SNPs for each chromosome by position in increasing order 
  sort -k3,3n "chr${chr}_snps.txt" > "chr${chr}_sorted_increasing.txt"
done

# For loop for decreasing order (10 to 1)
for chr in {10..1}; do
  # Extract SNPs for each chromosome
  awk -F'\t' -v chr="chr$chr" '$2 == chr' maize_SNP_joined.txt > "chr${chr}_snps.txt"
  
  # Sort SNPs for each chromosome by position in decreasing order
  sort -k3,3nr "chr${chr}_snps.txt" > "chr${chr}_sorted_decreasing.txt"
done
 change 10 to 1 to 1--10 for increasing order
