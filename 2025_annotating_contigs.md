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
blastn -query ../../2021_XL_v10_refgenome/XL_CDS_only.fasta -db tig00008587_1_10920835.fasta_blastable -outfmt 6 -out XL_CDS_to_tig00008587_1_10920835_annotation_out.txt
```

This produces a file that can be downloaded, opened with excel, and sorted by column 9 (sstart, "I") and column 3 (pident "C"). Then one can check the region of interest. For example, around 1Mb on contig tig00011029, the cuzd1.2 appears to be located. 

We can check the coverage of the exons of this locus in the WGS bam files like this:

```
samtools depth -aa -b ./allo_fam20_CDS.bed Z23732_sorted_rg_dedup.bam > Z23732_sorted_rg_dedup.bam_fam20_CDS_coverage.out
```
