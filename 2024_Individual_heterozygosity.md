# Calculating pi in windows for each genome
I'm using angsd for this.

bam files are here:
```
/home/ben/scratch/2024_allo_muel_RNAseq
```

First extract a new bam file that has only chr7 for each sample:
```
sbatch /home/ben/projects/rrg-ben/ben/2022_Liberia/2023_XT_genomz/ben_scripts/2023_samtools_subset_bam.sh  /home/ben/projects/rrg-ben/ben/2020_XT_WW_WZ_WY/raw_data/XT7_WY_trim_noadapters/XT7_WY_no_adapt__sorted.bam_rg.bam XT7_WY
```
Try using exons only to reduce noise from mismapped reads and repettive regions
```
/home/ben/projects/rrg-ben/ben/2022_Liberia/2023_XT_genomz/ben_scripts/2023_samtools_subset_bam_using_bed.sh
```
make a file (mellobam_path.txt) that has the path to the bam file (do this for each one).
```
/home/ben/projects/rrg-ben/ben/2022_Liberia/2023_XT_genomz/ben_scripts/2023_angsd.sh
```
```
#!/bin/sh
#SBATCH --job-name=angsd
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=2:00:00
#SBATCH --mem=2gb
#SBATCH --output=angsd.%J.out
#SBATCH --error=angsd.%J.err
#SBATCH --account=def-ben


module load StdEnv/2020 angsd/0.939

#angsd -bam ${1}.txt -doSaf 1 -anc /home/ben/projects/rrg-ben/ben/2020_XT_v10_refgenome/XENTR_10.0_genome_scafconcat.fasta -G
L 1 -out ${1}_angsd_out

angsd -bam ${1}.txt -doSaf 1 -anc /home/ben/projects/rrg-ben/ben/2020_XT_v10_refgenome/XENTR_10.0_genome_scafconcat.fasta -GL
 1 -out ${1}_out
realSFS ${1}_out.saf.idx -P 24 -fold 1 > ${1}_out.sfs
realSFS saf2theta ${1}_out.saf.idx -sfs ${1}_out.sfs -outname ${1}_out
thetaStat do_stat ${1}_out.thetas.idx -win 50000 -step 10000  -outnames ${1}_theta.thetasWindow.gz
```

This generated an output file with the suffix ".pestPG" that has the windowed stats.

I need to divide these by the number of sites to get the pi per site...

