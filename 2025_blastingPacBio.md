# Blasting PacBio assembly

First make a blast database out of the assembly:
```
makeblastdb -in allo.fasta.contigs_nobubbles.fasta.gz -dbtype nucl -out allo.fasta.contigs_nobubbles.fasta.gz_blastable
```

Using the notch4 seq that Jade generated as a query (also do the laevis seq and then align output from both):
```
blastn -query allo_notch4_region.fa -db allo.fasta.contigs_nobubbles.fasta.gz_blastable -outfmt 6 -out allo_notch4_to_pacbio6.out
```
Make a bed file from output
```
cut -f2,9,10 allo_notch4_to_pacbio6.out > allo_notch4_to_pacbio6.bed
```
Now manually adjust the bed file so the start is lower than the stop.

Extract fasta seqs
```
bedtools getfasta -fi m84066_250106_232136_s1.fasta -bed allo_notch4_to_pacbio6.bed -fo allo_notch4_to_pacbio6.fasta
```
Align the fasta seqs
```
 mafft --adjustdirectionaccurately allo_notch4_to_pacbio6.fasta > allo_notch4_to_pacbio6_align.fasta
```
