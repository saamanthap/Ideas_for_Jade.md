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
 awk '{a[$1]++} END{for(b in a) print b}' fem_region_DE_genez_to_XL_genome.out
```
Now grab the sequences associated with each transcript name and store them in a new fasta file: 
```
for i in $(cat ../2021_XL_v10_refgenome/unique_names_fem_region_transcripts_muel.txt); do grep -i -A1 "$i " ./DE_genez.fasta >> fem_DE_genez.fasta;done
```
## Filtering by alignment length  
One way to ensure that transcripts match genes and not repetitive elements is to filter by the ration of alignment length to query length. First generate an outfile that contains the query length (qlen): 
```
blastn -query DE_genez.fasta -db XL_v10.1_concatenatedscaffolds.fa_blastable -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore qlen" -out DE_genez_to_XL_genome.out
```
Now add another column at the end with the ratio alignment length/query length: (STILL TESTING... NEED TO FIX DELIMITERS)
```
awk -F'\t' '{$NF = $NF "\t" ($4 / $13); print}' qlen_DE_genez_to_XL_transcriptome.out > test.txt
```
# Getting annotations 
In order to get gene annotation info, blast the shortlist of fasta files against the human transcriptome:  

*Note: only one gene found any matches - the human and muelleri transcriptomes may be too diverged? Or I may have filtered the transcripts too stringently*
```
module load StdEnv/2020  gcc/9.3.0 blast+/2.14.0
blastn -query ../muel/fem_DE_genez.fasta -db gencode.v42.transcripts.fa_blastable -outfmt 6 -out fem_DE_genez_to_human_transcriptome.out
```
Or, try blasting against the laevis transcriptome (which contains annotations!):
```
blastn -query ../muel/fem_DE_genez.fasta -db xlaevisMRNA.fasta_blastable -outfmt 6 -out fem_DE_genez_to_XL_transcriptome.out
```
In order to print out unique gene IDs (use whatever blast outfile you want IDs from): 
```
awk -F '|' '{a[$4]++} END{for(b in a) print b}' fem_DE_genez_to_XL_transcriptome.out
```
I copy and pasted these gene IDs into https://www.pantherdb.org/ which finds genes associated with these transcripts (although not all IDs found matches). I exported all the matches as a text file and copy and pasted them into a file called "GO_fem_genes.txt" so that I can parse them to pull out the gene acronyms.    

Each gene is on its own line with the gene acronym in the second filed (pipe delimited). Example of the format:
```
XENLA|Gene=dab2.S|UniProtKB=Q6PAV7      NM_001089785,BC060027       MGC68686 protein;dab2.S;PTN009211979;orthologs  DISABLED HOMOLOG 2 (PTHR47695:SF5)          Xenopus laevis
XENLA|Gene=abl1.L|UniProtKB=V5NT95      XM_018241388    Tyrosine-protein kinase;abl1.L;PTN009099795;orthologs       TYROSINE-PROTEIN KINASE ABL1 (PTHR24418:SF438)      non-receptor tyrosine protein kinase(PC00168)       Xenopus laevis
```
Get all the gene names:   
```
grep -oP 'Gene=\K[^|]+' GO_fem_genes.txt

Notes: 
-o means that only the matched parts of the line are printed 
P allows you to use the \K option 
Match the string Gene= 
\K means that everything matched so far (meaning the string 'Gene=' is not printed out with the final output 
[^] specifies to match all the characters that are not a pipe (this way it will stop matching as soon as it hits the pipe 
+ means you can match any number of characters before the pipe
```
Pipe the command into sed to make it into a comma-separated list, which could be more easily searchable?: 
```
grep -oP 'Gene=\K[^|]+' GO_fem_genes.txt | sed 's/$/,/g' | tr '\n' ' ' > newfile.txt
```
