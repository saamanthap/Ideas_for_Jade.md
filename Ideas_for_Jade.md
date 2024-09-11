# A few ideas for Jade (original list)
* Mitonuclear interactions in Xenopus
* Mapping of sex determining regions in various Xenopus species (X. victorianus, X. fraseri)
* Germline regulation of sex-linked gene expression in Xenopus (especially X. borealis)
* Subgenome evolution of X. mellotropicalis

# NEW project for Jade (Sept, 2024)

Functional characterization of a recently evolved sex determining gene in X. allofraseri
* A previous study from our lab (now in review) found that a new male-determining gene exists in X. allofraseri on Chr7L around coordinate 18.2-19.6 bp in the XL reference genome.
* We have this species at McMaster and at the MBL (USA), so gene editing is possible
* I have submitted samples for RNAseq from tadpole stage 50 gonads; these data will help us identify genes with male-specific expression at this crucial stage of sexual differentiation
* I have also submitted 10 samples for whole genome sequencing; these data will help us further demarcate the margins of the male-specific region on Chr7L

  ## First steps - RNAseq
  * We hopefully will get the RNAseq data back in mid-September. Once we do, these are the steps we need to take:
    * Trim the reads for each sample with trimmomatic
    * Perform a de novo transcriptome assembly using all of the RNAseq data combined
    * Using STAR, which is a splicing-aware aligner, map the RNAseq data from each individual to (1) the de novo assembly and also to (2) the XL transcriptome
    * Call genotypes on the data mapped to the XL genome
    * Identify the sex of the individuals based on patterns of heterozygosity in the sex-linked region of Chr7L
    * Perform an analysis of differential expression; male-specific transcripts are candidate male-determining genes
    * Map the reads from the de novo assembly to the XL genome version 10; this will help us identify where the transcripts are
    * Characterize the distribution of differentially expressed reads on the XL genome
   
  ## First steps - WGS data
  *  We hopefully will get the WGS data back in early October. This is from 5 adult females and 5 adult males. We can use these data to identify the boundaries of the male-specific region which will potentially help us narrow down candidate loci for functional assessment/knockout experiments. THis will also help refine our evaluation of which tadpoles are male and which are female.
  *  Once we get these data, these are the steps we need to take:
      * Trim the reads for each sample (trimmomatic - same as the RNAseq)
      * Map the data for each individual to the XL v10 genome (this generates a bam file)
      * Genotype each individual with GATK (HaplotypeCaller, GenotypeGVCFs, etc)
      * Convert the genotypes to a tab-delimited file using vcftools
      * Use a pre-existing script from Ben (Parse_tab.pl) to identify position with heterozygosity in all males; we are particularly interested in the beginning of Chr7L (~8-20Mb) but also other genes that may have been duplicated.
