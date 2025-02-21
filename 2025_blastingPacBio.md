# Blasting PacBio

While we wait for the assembly to be done, we can blast areas of interest identified from WGS data against the PacBio data.

```
blastn -query allo_notch4_region.fa -db m84066_250106_232136_s1.fasta_blastable -outfmt 6 -out allo_notch4_to_pacbio6.out
```

```
cut -f2,9,10 allo_notch4_to_pacbio6.out > allo_notch4_to_pacbio6.bed
```
```
bedtools getfasta -fi m84066_250106_232136_s1.fasta -bed allo_notch4_to_pacbio6.bed -fo allo_notch4_to_pacbio6.fasta
```
