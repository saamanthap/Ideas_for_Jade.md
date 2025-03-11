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
# Map reads
```
#!/bin/sh
#SBATCH --job-name=bwa_align
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=4
#SBATCH --time=128:00:00
#SBATCH --mem=32gb
#SBATCH --output=bwa_align.%J.out
#SBATCH --error=bwa_align.%J.err
#SBATCH --account=def-ben

# run by passing an argument like this (in the directory with the files)
# sbatch 2020_align_paired_fq_to_ref.sh pathandname_of_ref path_to_paired_fq_filez
# sbatch 2020_align_paired_fq_to_ref.sh /home/ben/projects/rrg-ben/ben/2018_Austin_XB_genome/Austin_genome/Xbo.v1.fa.gz pathtofqfilez

module load bwa/0.7.17
module load StdEnv/2023  gcc/12.3 samtools/1.20



for file in ${2}/*_trim_R1.fq.gz ; do         # Use ./* ... NEVER bare *    
#for file in ${2}/*_allR1.fq.gz ; do
if [ -e "$file" ] ; then   # Check whether file exists.
	bwa mem ${1} ${file::-14}_trim_R1.fq.gz ${file::-14}_trim_R1.fq.gz -t 16 | samtools view -Shu - | samtools sort - -o ${file::-14}_sorted.bam
	samtools index ${file::-14}_sorted.bam
  fi
done
```

# DeDup
```
#!/bin/sh
#SBATCH --job-name=picard_dedup
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=128gb
#SBATCH --output=picard_dedup.%J.out
#SBATCH --error=picard_dedup.%J.err
#SBATCH --account=rrg-ben

# run by passing an argument like this
# sbatch 2021_picard_dedup.sh pathtobamfile/bamfile_prefix

module load java
export JAVA_TOOL_OPTIONS="-Xmx100g"
export _JAVA_OPTIONS="-Xms100g -Xmx100g"

# must first add readgroups with picard
module load StdEnv/2020 picard/2.23.3

java -jar $EBROOTPICARD/picard.jar MarkDuplicates \
     REMOVE_DUPLICATES=true \
     I=${1}.bam \
     O=${1}_dedup.bam \
     M=marked_dup_metrics.txt
```
