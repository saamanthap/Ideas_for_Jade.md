# bamCoverage 

directory:
```
/home/ben/projects/rrg-ben/ben/2025_allo_PacBio_assembly/Adam_allo_genome_assembly/deepTools/deeptools
```
bamCoverage is a tool in the package "deepTools" that calculates coverage in windows from bam files. Here is a script that runs it:
```sh
#!/bin/sh
#SBATCH --job-name=bamCoverage
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=bamCoverage.%J.out
#SBATCH --error=bamCoverage.%J.err
#SBATCH --account=rrg-ben

module load StdEnv/2023 python/3.13.2

/home/ben/projects/rrg-ben/ben/2025_allo_PacBio_assembly/Adam_allo_genome_assembly/deepTools/deeptools/vcf_env/bin/bamCoverage -b ${1} --outFileFormat “bedgraph” --binSize 100000 --ignoreDuplicates --minMappingQuality 30 -o ${1}_bamCoverage.bw
```
