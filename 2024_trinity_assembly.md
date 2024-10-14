# De novo trinity assembly for all allo (and separately for all muel) tad stage 50 RNAseq data

I did a denovo assembly for X. allofraseri and X. muelleri tad RNAseq using this sbatch script:
```
#!/bin/sh
#SBATCH --job-name=trinity
#SBATCH --nodes=1
#SBATCH --cpus-per-task=32
#SBATCH --exclusive
#SBATCH --time=166:00:00
#SBATCH --mem=0
#SBATCH --output=allo_trinity.%J.out
#SBATCH --error=allo_trinity.%J.err
#SBATCH --account=rrg-ben

#module load StdEnv/2020 gcc/9.3.0 openmpi/4.0.3 trinity/2.12.0 samtools/1.10 jellyfish/2.3.0
module purge
module load StdEnv/2020 gcc/9.3.0 salmon/1.7.0 samtools/1.17 jellyfish/2.3.0  trinity/2.14.0 python scipy-stack

Trinity --seqType fq --left allo_all__trim_R1.fq.gz --right allo_all__trim_R2.fq.gz --CPU "${SLURM_CPUS_PER_TASK}" \
   --full_cleanup --max_memory 110G --min_kmer_cov 2 --bflyCalculateCPU \
   --output allo_trinity_assembly_all_batches
```

Finished assembly for X. muel is here:
```
/home/ben/projects/rrg-ben/ben/2024_allo_muel_RNAseq/de_novo_assembly_trinity/muel_trinity_assembly_all_batches.Trinity.fasta
```
Finished assembly for X. allo is here:
```
/home/ben/projects/rrg-ben/ben/2024_allo_muel_RNAseq/de_novo_assembly_trinity/allo_trinity_assembly_all_batches.Trinity.fasta
```
