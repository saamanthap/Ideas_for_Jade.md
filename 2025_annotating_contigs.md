# Annotating contigs

I am using XL CDS to annotate contigs in allo. I made a fasta file of all CDS from XL here:
```
/home/ben/projects/rrg-ben/ben/2021_XL_v10_refgenome/XL_CDS_only.fasta
```

And I can use this as a query to blast individual contigs. First extract the contig with bedtools:
```
bedtools getfasta -fi allo.fasta.contigs_nobubbles.fasta.gz -bed tig00008587_1_10920835.bed -fo tig00008587_1_10920835.fasta
```

Now I need to make a blast db of the contig of interest (I'm going to do this for 7 contigs that map to Chr7L):
```
makeblastdb -in tig00008587_1_10920835.fasta -dbtype nucl -out tig00008587_1_10920835.fasta_blastable
```

And now blast:
```
blastn -query ../../2021_XL_v10_refgenome/XL_CDS_only.fasta -db tig00008587_1_10920835.fasta_blastable -outfmt 6 -out XL_CDS_to_tig00008587_1_10920835.out
```
