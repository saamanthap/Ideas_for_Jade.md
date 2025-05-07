# Directory
```
/home/ben/projects/rrg-ben/ben/2024_allo_muel_RNAseq/ben_scripts
```

# Generating pseudo counts for de novo transcriptome assemblies

I used trinity to generate de novo transcriptome assemblies for X. muelleri and X. allofraseri tad stage 50 gonads.

Index the de novo assemblies:
```
#!/bin/sh
#SBATCH --job-name=kallisto
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=4:00:00
#SBATCH --mem=32gb
#SBATCH --output=kallisto.%J.out
#SBATCH --error=kallisto.%J.err
#SBATCH --account=def-ben

# run by passing an argument like this
# sbatch ./2021_kallisto_index_transcriptome_assembly.sh path_to_transcriptome_assembly/transcriptomeAssembly.fasta

module load StdEnv/2020 intel/2020.1.217 kallisto/0.46.1

kallisto index -i ${1}.idx ${1}
```
