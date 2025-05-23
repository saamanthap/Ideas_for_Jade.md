# Checking heterozygosity in allofraseri assembly

We need to design primers so we can genotype the sex of tadpoles. We have an assembly from a male and it should be possible to assess heterozygosity in all of the exons in the "hotspot" region between 15-25Mb.

First step: extract annotations from XL gtf file:
```
grep 'Chr7L' XENLA_10.1_Xenbase_longest.gtf | grep 'exon' > XL_Chr7L_exon.gtf
```

Then I downloaded this file and extracted the coordinates:
```
cut -f1,4,5 XL_Chr7L_exon_hotspot_15_to_25Mb.gtf > XL_Chr7L_exon_hotspot_15_to_25Mb.bed
```
Then I edited this so that the lower coordinate is first. Then I uploaded this back to graham so I could pull out the XL seqs like this:
```
bedtools getfasta -fi XENLA_10.1_genome.fa -bed XL_Chr7L_exon_hotspot_15_to_25Mb.bed -fo XL_Chr7L_hotspot_for_allo.fa
```
I moved this to this directory:
```
/home/ben/projects/rrg-ben/ben/2025_allo_PacBio_assembly/Adam_allo_genome_assembly
```
and blasted this against the allofraseri assembly *with* bubbles
```
blastn -query XL_Chr7L_hotspot_for_allo.fa -db allo.fasta.contigs.fasta.gz__blastable -outfmt 6 -out XL_Chr7L_hotspot_for_allo_to_allo_withbubbles.txt
```
I am now focusing only on exons that are at least 1000bp. So I filtered the blast output based on the alignment length column 4 like this:
```
awk -F "    " '$4>1000' XL_Chr7L_hotspot_for_allo_to_allo_withbubbles.txt > XL_Chr7L_hotspot_for_allo_to_allo_withbubbles_onlybigexons.txt
```
In the command above, we need to specify that the delimiter is a tab, so this tab has to be inserted between the double quotes using control-v and then tab.
