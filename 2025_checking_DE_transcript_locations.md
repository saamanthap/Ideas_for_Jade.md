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

```
Now use this file to grep the headers:
```
for i in `cat ./example_list_of_differentially_expressed_transcripts.txt `; do grep -i $i ../muel/de_novo_assembly_trinity/muel_trinity_assembly_all_batches.Trinity.fasta >> names.txt;done
```
Now remove the greater than sign:

sed -i 's/>//g' names.txt
Now use seqtk to extract the fasta entries

module load StdEnv/2020 seqtk/1.3
seqtk subseq ../XL_v10_transcriptome/XENLA_10.1_GCF_XBmodels.transcripts.fa ccdc_kallisto_edgeR_sequence_file > ccdc_kallisto_edgeR_sequence_file_output.fasta
