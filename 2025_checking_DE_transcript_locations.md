# Checking gemomic locations of differentially expressed transcripts

We have a list of differentially expressed transcripts and we want to know where in the XL genome they are, and also what their annotations are.

In this directory on graham, I have set up databases of the XL genome seq, the XL transcriptome seqs, and the human transcriptome seqs, respectively:
```
/home/ben/projects/rrg-ben/for_Sam/2021_XL_v10_refgenome
/home/ben/projects/rrg-ben/for_Sam/2025_XL_transcriptome
/home/ben/projects/rrg-ben/for_Sam/human_transcriptome
```

In each of these directories I have made a blast database like this:
```
makeblastdb -in XL_v10.1_concatenatedscaffolds.fa -dbtype nucl -out XL_v10.1_concatenatedscaffolds.fa_blastable
makeblastdb -in xlaevisMRNA.fasta -dbtype nucl -out xlaevisMRNA.fasta_blastable
makeblastdb -in gencode.v42.transcripts.fa -dbtype nucl -out gencode.v42.transcripts.fa_blastable
```
This allows us to find the match in these databases of each sequence in a query.

# Prepare a query file with the seqs of the differentially expressed genes

Begin with a file (example_list_of_differentially_expressed_transcripts.txt) that has a list of names of the differentially expressed genes that looks like this:
```
TRINITY_DN119063_c0_g1_i1
TRINITY_DN119063_c0_g2_i1
```
Now use this file to grep the headers:
```
for i in $(cat ./example_list_of_differentially_expressed_transcripts.txt); do grep -i "$i " ../muel/de_novo_assembly_trinity/muel_trinity_assembly_all_batches.Trinity.fasta >> names.txt;done
```
Now remove the greater than sign:
```
sed -i 's/>//g' names.txt
```
Now use seqtk to extract the fasta entries
```
module load StdEnv/2020 seqtk/1.3
seqtk subseq ../muel/de_novo_assembly_trinity/muel_trinity_assembly_all_batches.Trinity.fasta names.txt > DE_genez.fasta
```
OK now we should have a file with the sequences of each of the differentially expressed genes that is called "DE_genez.fasta". This can be used to query a blast database such as the three above to get information about locations and annotations. For example:
```
module load StdEnv/2020  gcc/9.3.0 blast+/2.14.0
blastn -query DE_genez.fasta -db XL_v10.1_concatenatedscaffolds.fa_blastable -outfmt 6 -out DE_genez_to_XL_genome.out
```
Alternatively, use a longer command to include the gene name in the blast results (shown here using the laevis transcriptome): 
```
blastn -query ../muel/DE_genez.fasta -db xlaevisMRNA.fasta_blastable -outfmt "6 qseqid sseqid stitle pident length mismatch gapopen qstart qend sstart send evalue bitscore" -out DE_genez_to_XL_transcriptome.out
```
# Filtering the blast results 
Pull out the results that blast to Chr4L (muelleri sex chromosome) and write in a new file:
```
grep "Chr4L" DE_genez_to_XL_genome.out > Chr4L_DE_genez_to_XL_v10_refgenome
```
Now pull out the results that fall in the 110000000 - 147000000 region: 
(The 9th and 10th field of each line contain these values)
```
awk '$9 >= 110000000 && $10 <= 147000000' Chr4L_DE_genez_to_XL_v10_refgenome.out > fem_region_DE_genez_to_XL_genome.out
 ```
The last two commands could have been accomplished together (for next time):
```
grep "Chr4L" DE_genez_to_XL_genome.out | awk '$9 >= 110000000 && $10 <= 147000000' > fem_region_DE_genez_to_XL_genome.out
```
Since each transcript has many blast results, I want to print out unique transcript names. (I will copy them from the terminal and paste them into a file called "unique_names_fem_region_transcripts_muel.txt")
```
 awk 'NR>1{a[$1]++} END{for(b in a) print b}' fem_region_DE_genez_to_XL_genome.out
```
Now grab the sequences associated with each transcript name and store them in a new fasta file: 
```
for i in $(cat ../2021_XL_v10_refgenome/unique_names_fem_region_transcripts_muel.txt); do grep -i -A1 "$i " ./DE_genez.fasta >> fem_DE_genez.fasta;done
```
# Getting annotations 
In order to get gene annotation info, blast the shortlist of fasta files against the human transcriptome:  

*Note: only one gene found any matches - the human and muelleri transcriptomes may be too diverged? Or I may have filtered the transcripts too stringently*
```
module load StdEnv/2020  gcc/9.3.0 blast+/2.14.0
blastn -query ../muel/fem_DE_genez.fasta -db gencode.v42.transcripts.fa_blastable -outfmt 6 -out fem_DE_genez_to_human_transcriptome.out
```



