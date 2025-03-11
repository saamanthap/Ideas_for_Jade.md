# Mapping to PacBio assembly

Adam managed to run canu with the allofraseri PacBio data and generate an assembly. It is pretty good overall (e.g. the total length is ~3.4Gb) but it is not chromosome scale. I'm going to map the resequencing data to this assembly.

First prepare the reference genome. Use `bwa` to index it:
```
bwa index allo.fasta.contigs_nobubbles.fasta.gz
```
Use `GATK` to make a dictionary:
```
#!/bin/sh
#SBATCH --job-name=CreateSequenceDictionary
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=2:00:00
#SBATCH --mem=10gb
#SBATCH --output=CreateSequenceDictionary.%J.out
#SBATCH --error=CreateSequenceDictionary.%J.err
#SBATCH --account=rrg-ben

module load StdEnv/2023 gatk/4.4.0.0
gatk --java-options -Xmx10G CreateSequenceDictionary -R ${1}
```
Use `samtools` to make an fai file:
```
samtools faidx allo.fasta.contigs_nobubbles.fasta.gz
```
