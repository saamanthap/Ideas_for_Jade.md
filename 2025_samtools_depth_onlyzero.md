# Output positions with zero depth 

We want to identify scaffolds and portions of scaffolds that have zero depth in one sex. Those only in males are candidates for Y-specific seqs. Those only in females are probably just random because males should have an X.

directory:
```
/home/ben/projects/rrg-ben/ben/2025_allo_PacBio_assembly/allo_WGS
```

Here is a script to output only the positions with zero depth:
```sh
#!/bin/sh
#SBATCH --job-name=samtools_depth
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=samtools_depth.%J.out
#SBATCH --error=samtools_depth.%J.err
#SBATCH --account=rrg-ben

module load StdEnv/2023  gcc/12.3 samtools/1.20
samtools depth -aa ${1} | grep '	0' >> ${1}_zerodepth_aa.txt
```
