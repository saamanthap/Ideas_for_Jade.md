# PacBio assembly 

We are collaborating with Adam Session on some Xenopus genome assemblies using PacBio data.

As a preliminary trial, we can try doing an assembly with canu.
First unzip the data:
```
unzip /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/m84066_250106_232136_s1_fastq.zip
```
```
unzip /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/m84066_250114_225413_s2_fastq.zip
```

I was able to get canu running on info113 (but not info2020). I had to edit this file:
```
/home/ben/2025_canu/canu-2.3/lib/perl5/site_perl/canu/Defaults.pm
```
and set the `$java` variable to 1.8 around line 1111 like this:
```
        $version=1.8;
        last  if ($version >= 1.8);
```
This is because the software was failing to recognize the version of java on info (which is way more recent that v1.8). Then I ran this command:
```
canu -p allo_canu genomeSize=3.4g -maxThreads=8 -pacbio /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/m84066_250106_232136_s1.fastq /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/m84066_250114_225413_s2.fastq
```
maybe add this 'mhapPipe=false purgeOverlaps=false saveOverlaps=true' according to:https://github.com/marbl/canu/issues/2070
