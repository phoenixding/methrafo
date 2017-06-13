```
  __  __      _   _     _____        __      
|  \/  |    | | | |   |  __ \      / _|     
| \  / | ___| |_| |__ | |__) |__ _| |_ ___  
| |\/| |/ _ \ __| '_ \|  _  // _` |  _/ _ \ 
| |  | |  __/ |_| | | | | \ \ (_| | || (_) |
|_|  |_|\___|\__|_| |_|_|  \_\__,_|_| \___/ 
```                                             
                                       		 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)	
			 
# INTRODUCTION


This software is designed to estimate genome-wide absolute methylation 
level based on the MeDIP-Seq data. 

# SUPPORTED PLATFORMS

Linux, MacOS

# PREREQUISITES

python 2.7.x

It was installed by default for most Linux distributicons and MAC
If not, please refer https://www.python.org/downloads/ for installation 
instructions. 

Python packages dependencies:  
-- scikit-learn   
-- scipy  
-- numpy  
-- pyBigWig

The python setup.py script will try to install these packages automatically.
However, please install them manually if, by any reason, the automatic 
installation fails. 

external packages dependencies:
The software requires the following external packages to process bam files.
(The following packages must be installed if you want to calculate methylation
level based on raw bam files) 

--samtools  
--bedtools  
--bedGraphToBigWig  

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
	If you can't install samtools,bedtools using the command above, please refer 
	the manual page for installation instructions. 
	samtools: http://samtools.sourceforge.net/   
	bedtoos: http://bedtools.readthedocs.io/en/latest/   

* (2) bedGraphToWigBig  
	First download the excutable from UCSC web-site.

	For Linux, 
	http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/  
	For Mac os,
	http://hgdownload.cse.ucsc.edu/admin/exe/macOSX.x86_64/

	Second, add the script to os path. 
	Open a terminal, then type
	```
	$export PATH=$PATH:~/path_to_bedGraphToWigBig_Folder
	```
	If you need it permanently, add the above command in your ~/.bashrc

	Please refer the following page for help about how to set $PATH on linux
	http://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux

# INSTALLATION

First, download the software package.  
Second, cd to the package directory.  
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
Please check the example dataset provided in the example folder at:    
http://www.cs.cmu.edu/~jund/methrafo/example/   

The following 4 commands were provided by the methrafo package:   
methrafo.bamScript, methrafo.download, methrafo.train, methrafo.predict   

* *1) methrafo.download*   
	This command was used to download the genome files 
	Files: chromosome sequences(e.g. chr1.fa.gz), chromosome sizes(e.g. hg19.chrom.sizes)
	```
	$methrafo.download <reference_genome_id> <output_directory>
	```
	e.g.
	```
	$methrafo.download hg19 hg19
	```
	reference_genome_id represents the ID of the genome (e.g. hg19,mm10, et al.)

* *2) methrafo.bamScript*     
	This command was used to convert bam file to bigWig file (RPKM)   
	```
	$methrafo.bamScript <bam_file> <genome size file>
	```
	bam_file => bam file  
	genome size file => chromosome sizes .e.g. hg19.chrom.sizes (This file can be found in the downloaded reference genome folder e.g. hg19_out)  
	e.g.    
	```
	$methrafo.bamScript example/example_raw.bam hg19/hg19.chrom.sizes
	```

*  *3) methrafo.train*  
	This command was used to train the model
	```
	$methrafo.train <downloaded_reference_genome_folder> <MeDIPS.bigWig> <Bisulfite.bigWig> <output_prefix>   
	```
	<downloaded_reference_genome_folder>: The downloaded genome reference folder hg19   
	<MeDIPS.bigWig> : The bigWig file representing the RPKM on each position of the genome.    
	<Bisulfite.bigWig>: The bigWig file representing the Bisulfite-Seq methylation level.    
	Note: if your input is .bam file, please use methrafo.bamScript to convert it to bigWig format.   
	e.g.
	```
	$methrafo.train hg19 example/example_medip.bw example/example_bisulfite.bw trained_model
	```

* *4) methrafo.predict*  
	This command was used to predict the methylation level based on MeDIP-Seq data.
	```
	$methrafo.predict hg19 example/example_medips.bw trained_model.pkl example_out
	```

## Command descriptions:

* (1) methRafo accept the following formats as the input:  
   .bam  => mapped reads from MeDIP-Seq result  
   .bw   => bigWig file from MeDIP-Seq (representing the RPKM value)  
   
   If the input is in .bam format, you need to use methrafo to convert 
   it to bigWig format. Please refer methrafo.bamScript command above for
   conversion instruction. 
   
   You might need to use methrafo.download the download corresponding reference 
   genome. But you are also allowed to download the reference genome yourself.
   The reference genome folder needs to contain the following files:
   all chromosome sequences in fasta format; chromosome size file containing
   the length information of the chromosome. 
   
* (2) We provided a trained model on human (Based on breast luminal cells dataset 
   from roadmap database). We tested it  on a few other datasets on human and it shows
   pretty good performance in terms of running time and correlation with Bisulfite-Seq data.
   You can use the trained model we provided or you can use the methrafo.train script we provide
   to build your own model by specifying MeDIP-Seq input (in bigWig format) and Bisulfite-Seq output (in bigWig format)
   Please refer methrafo.train command for complete details. 
   
* (3) With the provided trained model (or your own trained model), we provided the command methrafo.predict to 
   predict the genome wide methylation level. The output file is a Wiggle (.wig) format file. You can visualize
   it using IGV or UCSC genome browser. You can also get the methylation level for any given genomic location
   easily from the generated wig file. 
   
  
# CREDITS

This software was developed by ZIV-system biology group @ Carnegie Mellon University  
Implemented by Jun Ding


# LICENSE 

This software is under MIT license.  
see the LICENSE.txt file for details. 


# CONTACT

zivbj at cs.cmu.edu  
jund  at andrew.cmu.edu




                                 
                                 
                                 
                                 
                                 

                                                         
