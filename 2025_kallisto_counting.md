# Directory
```
/home/ben/projects/rrg-ben/ben/2024_allo_muel_RNAseq/ben_scripts
```

# Sex genotypes
Samantha Potts genotyped the sex of all ten tads using two markers; both were completely consistent:
```
In total, there are seven females and three males.
Females: tadpoles 31, 32, 34, 35, 36, 37,42
Males: tadpoles 33, 38, 39
```

# Generating pseudo counts for de novo transcriptome assemblies

I used trinity to generate de novo transcriptome assemblies for X. muelleri and X. allofraseri tad stage 50 gonads.

# First index the de novo assemblies:
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

# Count
```
#!/bin/sh
#SBATCH --job-name=kallisto
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=32gb
#SBATCH --output=kallisto.%J.out
#SBATCH --error=kallisto.%J.err
#SBATCH --account=def-ben

# run by passing an argument like this
# sbatch ./2021_kallisto_count_withBoot.sh ref_transcriptome_indexfile.idx folderwithfqfilez

module load StdEnv/2020 intel/2020.1.217 kallisto/0.46.1

#  Always use for-loop, prefix glob, check if exists file.
for file in $2/*__trim_R1.fq.gz ; do         # Use ./* ... NEVER bare *
    if [ -e "$file" ] ; then   # Check whether file exists.
        kallisto quant -b 100 -i ${1} -o ${file::-15}_kallisto_boot_out ${file::-15}__trim_R1.fq.gz ${file::-15}__trim_R2.fq.gz # I don't think we can use paired and single reads concurrently
	# ${file::-15}__trim_single_R1.fq.gz ${file::-15}__trim_single_R2.fq.gz
  fi
done
```
