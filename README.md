```
  __  __      _   _     _____        __      
|  \/  |    | | | |   |  __ \      / _|     
| \  / | ___| |_| |__ | |__) |__ _| |_ ___  
| |\/| |/ _ \ __| '_ \|  _  // _` |  _/ _ \ 
| |  | |  __/ |_| | | | | \ \ (_| | || (_) |
|_|  |_|\___|\__|_| |_|_|  \_\__,_|_| \___/ 
```                                             
[![Build Status](https://travis-ci.org/phoenixding/methrafo.svg?branch=master)](https://travis-ci.org/phoenixding/methrafo)                                     		 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)	
			 
# INTRODUCTION


This python package is designed to estimate genome-wide absolute methylation 
level based on the MeDIP-Seq data. 

For R users, please refer to https://github.com/phoenixding/rmethrafo for R wrapped package. 

# SUPPORTED PLATFORMS

Linux, MacOS

# PREREQUISITES

python 2.7.x

It was installed by default for most Linux distributions and MAC.  
If not, please refer to https://www.python.org/downloads/ for installation 
instructions. 

Python packages dependencies:  
-- scikit-learn   
-- scipy  
-- numpy  
-- pyBigWig

The python setup.py script will try to install these packages automatically.
However, please install them manually if, by any reason, the automatic 
installation fails. 

External packages dependencies:
The software requires the following external packages to process bam files.
(The following packages must be installed if you want to calculate methylation
level based on raw bam files).

--samtools  
--bedtools  
--bedGraphToBigWig  

We provided a "pre_install" shell script to install those dependencies automatically. 
However, please install them manually if the automatic installation fails.

```
$./pre_install
```

Manual dependency installation (**skip this section if the automatic dependency installation succeeded**):  


* (1) samtools, bedtools  
	For debian/ubuntu based linux, you can install samtools,bedtools directly by:
	```
	$sudo apt-get install samtools
	$sudo apt-get install bedtools
	```
	For redhat/centos/fedora linux, you can install samtools, bedtools directly by:
	```
	$sudo yum install samtools
	$sudo yum install bedtools
	```
	If you can't install samtools,bedtools using the commands above, please refer to 
	the manual pages for installation instructions.   
	samtools: http://samtools.sourceforge.net/   
	bedtools: http://bedtools.readthedocs.io/en/latest/   

* (2) bedGraphToWigBig  
	First download the excutable file from UCSC web-site.

	For Linux, 
	http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/  
	For Mac os,
	http://hgdownload.cse.ucsc.edu/admin/exe/macOSX.x86_64/

	Second, add the script to os path.   
	Open a terminal, then type
	```
	$export PATH=$PATH:~/path_to_bedGraphToWigBig_Folder
	```
	If you need it permanently, add the above command to your ~/.bashrc.  

	Please refer to the following page for how to set $PATH on linux.  
	http://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux

# INSTALLATION

First, download the software package.  
Second, cd to the package directory.  
Third, run setup.py script to install.  


Linux, Mac:  
```
$ sudo python setup.py install 
```
Or install using pip   
```
$sudo pip install methrafo
```

USAGE
========================================================================


The following 4 commands were provided by the methrafo package:   
methrafo.bamScript, methrafo.download, methrafo.train, methrafo.predict.     

* *1) methrafo.download*   
	This command is used to download the genome files.  
	Files: chromosome sequences(e.g. chr1.fa.gz), chromosome sizes(e.g. hg19.chrom.sizes)
	```
	$methrafo.download <reference_genome_id> <output_directory>
	```
	example:  
	```
	$methrafo.download hg19 hg19
	```
	reference_genome_id represents the ID of the genome (e.g. hg19,mm10, et al.)

* *2) methrafo.bamScript*     
	This command is used to convert bam file to bigWig file (RPKM).  
	```
	$methrafo.bamScript <bam_file> <genome size file>
	```
	bam_file => bam file  
	genome size file => chromosome sizes .e.g. hg19.chrom.sizes.
	
	```
	chr1	249250621
	chr2	243199373
	chr3	198022430
	chr4	191154276
	chr5	180915260
	chr6	171115067
	chr7	159138663
	chrX	155270560
	chr8	146364022
	chr9	141213431
	chr10	135534747
	chr11	135006516
	chr12	133851895
	chr13	115169878
	chr14	107349540
	chr15	102531392
	chr16	90354753
	chr17	81195210
	chr18	78077248
	chr20	63025520
	chrY	59373566
	chr19	59128983
	chr22	51304566
	chr21	48129895
	```
	
	example:  
	```
	$methrafo.bamScript example/example_raw.bam hg19/hg19.chrom.sizes
	```

*  *3) methrafo.train*  
	This command is used to train the random forest model.
	```
	$methrafo.train <downloaded_reference_genome_folder> <MeDIPS.bigWig> <Bisulfite.bigWig> <output_prefix>   
	```
	<downloaded_reference_genome_folder>: The downloaded genome reference folder (e.g. hg19).    
	<MeDIPS.bigWig> : The bigWig file representing the RPKM on each position of the genome.    
	<Bisulfite.bigWig>: The bigWig file representing the Bisulfite-Seq methylation level (ground truth).     
	Note: if your input is .bam file, please use methrafo.bamScript to convert it to bigWig format first.  
	
	example:  
	```
	$methrafo.train hg19 example/example_medip.bw example/example_bisulfite.bw trained_model
	```

* *4) methrafo.predict*  
	This command is used to predict the methylation level based on MeDIP-Seq data.
	
	example:  
	```
	$methrafo.predict hg19 example/example_medips.bw trained_model.pkl example_out
	```

## Command description:

* (1) methRafo accepts the following formats as the input:  
   .bam  => mapped reads from MeDIP-Seq result.   
   .bw   => bigWig file from MeDIP-Seq (representing the RPKM value).   
   
   If the input is in .bam format, you need to use methrafo to convert 
   it to bigWig format. Please refer to methrafo.bamScript command above for
   instruction. 
   
   You might need to use methrafo.download to download corresponding reference 
   genome. But you are also allowed to download the reference genome yourself.
   The reference genome folder needs to contain the following files:
   all chromosome sequences in fasta format; chromosome size file containing
   the length information of each chromosome. 
   
* (2) We provided a trained model on human (based on a breast luminal cells dataset 
   from roadmap database). We tested it on a few other datasets on human and it shows
   pretty good performance in terms of running time and correlation with Bisulfite-Seq data.
   You can use the trained model we provided or you can use the methrafo.train script
   to build your own model. Please refer to methrafo.train command for complete details. 
   
* (3) With the provided trained model (or your own trained model), we provided the command methrafo.predict to 
   predict the genome wide methylation level. The output file is a Wiggle (.wig) format file. You can visualize
   it using IGV or UCSC genome browser. You can also get the methylation level for any given genomic region
   easily from the generated wig file. 
   
# TEST EXAMPLE
We provided a test example inside the "test" folder. "test" is the bash script to perform the test on the example dataset.

```
# download reference genome (if the server is not responding, please download them manually)
methrafo.download hg19 hg19

#predict based on provided MeDIP-Seq bam 
methrafo.bamScript example_medip.bam hg19/hg19.chrom.sizes
methrafo.predict hg19 example_medip.bam.sort.bam.bedGraph.sort.bw ../model/rr.pkl e1_out

# predict based on provided MeDIP-Seq bigwig 
methrafo.predict hg19 example_medip.bw ../model/rr.pkl e2_out

```
  
# CREDITS

This software was developed by ZIV-system biology group @ Carnegie Mellon University  
Implemented by Jun Ding


# LICENSE 

This software is under MIT license.  
see the LICENSE.txt file for details. 


# CONTACT

zivbj at cs.cmu.edu  
jund  at andrew.cmu.edu




                                 
                                 
                                 
                                 
                                 

                                                         
