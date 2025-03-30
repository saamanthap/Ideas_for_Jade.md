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
# Overlap
Here is a perl script to identify positions that have zero overlap in five different bam files
```perl
#!/usr/bin/env perl
use strict;
use warnings;

# This program reads in five files and extracts values with information in all of them
# Specifically combinations of chromosome and position data.
# This can be used, for example, to see what positions identified by samtools
# have no coverage in all males or all females

# ./Gets_overlap_of_two_filez.pl input1 input2 input3 input4 input5 output
# input1 should be an output file from samtools
# input2 should be an output file from samtools
# input3 should be an output file from samtools
# input4 should be an output file from samtools
# input5 should be an output file from samtools
# output should be the output file 

my $input1 = $ARGV[0];
my $input2 = $ARGV[1];
my $input3 = $ARGV[2];
my $input4 = $ARGV[3];
my $input5 = $ARGV[4];
my %hash1; # the first two columns of input1 will be the key
my %hash2; # the first two columns of input2 will be the key
my %hash3; # the first two columns of input3 will be the key
my %hash4; # the first two columns of input4 will be the key
my %hash5; # the first two columns of input5 will be the key

my $outputfile = $ARGV[5];
my @columns;
my $size;

unless (open(OUTFILE, ">$outputfile"))  {
	print "I can\'t write to $outputfile   $!\n\n";
	exit;
}
print "Creating output file: $outputfile\n";

print OUTFILE "Chr\tpos\n";


# input 1
if ($input1 =~ /.gz$/) {
	open(DATAINPUT, "gunzip -c $input1 |") || die "can’t open pipe to $input1";
}
else {
	open DATAINPUT, $input1 or die "Could not read from $input1: $!";
}

while ( my $line = <DATAINPUT>) {
	$line =~ s/[\r\n]+//g; # removes carriage return
	@columns=split("	",$line);
	$hash1{$columns[0]."_".$columns[1]} = $columns[6];
} # end while
close DATAINPUT;

print "Done with file 1\n";
$size = keys %hash1;
print "Size of hash1 ",$size,"\n";

# input 2
if ($input2 =~ /.gz$/) {
	open(DATAINPUT2, "gunzip -c $input2 |") || die "can’t open pipe to $input2";
}
else {
	open DATAINPUT2, $input2 or die "Could not read from $input2: $!";
}

while ( my $line = <DATAINPUT2>) {
	$line =~ s/[\r\n]+//g; # removes carriage return
	@columns=split("	",$line);
	if(exists($hash1{$columns[0]."_".$columns[1]})){
		$hash2{$columns[0]."_".$columns[1]} = $columns[6];
	}
} # end while
close DATAINPUT2;
print "Done with file 2\n";
$size = keys %hash2;
print "Size of hash2 ",$size,"\n";

# input 3
if ($input3 =~ /.gz$/) {
	open(DATAINPUT3, "gunzip -c $input3 |") || die "can’t open pipe to $input3";
}
else {
	open DATAINPUT3, $input3 or die "Could not read from $input3: $!";
}

while ( my $line = <DATAINPUT3>) {
	$line =~ s/[\r\n]+//g; # removes carriage return
	@columns=split("	",$line);
	if(exists($hash2{$columns[0]."_".$columns[1]})){
		$hash3{$columns[0]."_".$columns[1]} = $columns[6];
	}
} # end while
close DATAINPUT3;

print "Done with file 3\n";
$size = keys %hash3;
print "Size of hash3 ",$size,"\n";

# input 4
if ($input4 =~ /.gz$/) {
	open(DATAINPUT4, "gunzip -c $input4 |") || die "can’t open pipe to $input4";
}
else {
	open DATAINPUT4, $input4 or die "Could not read from $input4: $!";
}

while ( my $line = <DATAINPUT4>) {
	$line =~ s/[\r\n]+//g; # removes carriage return
	@columns=split("	",$line);
	if(exists($hash3{$columns[0]."_".$columns[1]})){
		$hash4{$columns[0]."_".$columns[1]} = $columns[6];
	}
} # end while
close DATAINPUT4;

print "Done with file 4\n";
$size = keys %hash4;
print "Size of hash4 ",$size,"\n";

# input 5
if ($input5 =~ /.gz$/) {
	open(DATAINPUT5, "gunzip -c $input5 |") || die "can’t open pipe to $input5";
}
else {
	open DATAINPUT5, $input5 or die "Could not read from $input5: $!";
}

while ( my $line = <DATAINPUT5>) {
	$line =~ s/[\r\n]+//g; # removes carriage return
	@columns=split("	",$line);
	if(exists($hash4{$columns[0]."_".$columns[1]})){
		$hash5{$columns[0]."_".$columns[1]} = $columns[6];
		print OUTFILE $columns[0],"\t",$columns[1],"\n";
	}
} # end while
close DATAINPUT5;
print "Done with file 5\n";
$size = keys %hash5;
print "Size of hash5 ",$size,"\n";


close OUTFILE;
```
