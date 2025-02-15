# PacBio assembly 

We are collaborating with Adam Session on some Xenopus genome assemblies using PacBio data.

As a preliminary trial, we can try doing an assembly with canu.
First unzip the data:
```
unzip /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/m84066_250106_232136_s1_fasta
```
```
unzip /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/m84066_250114_225413_s2_fasta
```

I was able to get canu running on info113 and info115 (but not info2020). Previously I had to edit this file (but not when I reinstalled from git):
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
canu -p allo_canu genomeSize=3.4g -maxThreads=8 -pacbio /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8776/outputs/m84066_250106_232136_s1.fasta /home/ben/2025_PacBio_allo_boum/PB1192_01_BJE4902-B_Xallofraseri_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8810/outputs/m84066_250114_225413_s2.fasta
```
maybe add this 'mhapPipe=false purgeOverlaps=false saveOverlaps=true' according to:https://github.com/marbl/canu/issues/2070


To run them independently I made another folder and installed canu separately for boum.
And for boum:
```
canu -p boum_canu genomeSize=6.8g -maxThreads=8 -pacbio /home/ben/2025_PacBio_allo_boum/PB1192_02_BJE4901-B_Xboumbaensis_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell1/Fasta_Fastq_8777/outputs/m84066_250114_225413_s2.fasta /home/ben/2025_PacBio_allo_boum/PB1192_02_BJE4901-B_Xboumbaensis_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell2/Fasta_Fastq_8817/outputs/m84066_250115_005718_s3.fasta /home/ben/2025_PacBio_allo_boum/PB1192_02_BJE4901-B_Xboumbaensis_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell3/Fasta_Fastq_8818/outputs/m84066_250115_030030_s4.fasta /home/ben/2025_PacBio_allo_boum/PB1192_02_BJE4901-B_Xboumbaensis_Frog_LBSS9kbLC_HiFiv3_Revio-SPRQ_cell4/8869_Fasta_Fastq/outputs/m84066_250205_075344_s1.fasta
```
