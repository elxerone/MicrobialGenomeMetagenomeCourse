## From microbial genomes to metagenomes  
*Antti Karkman, 2017* 

# Installations
Log into Taito, either with ssh (Mac/Linux) or PuTTy (Windows)  

**FastQC & MultiQC**  
Two programs for sequence data quality control. Both will be installed using Bioconda package management tool that can be found from CSC.  
When using Bioconda at CSC, everything needs to be installed in virtual enviroments. You can create the virtual environment called `QC_env` and install the packages with one command.  
```
module load bioconda/3
conda create -n QC_env multiqc fastqc
```

The environment can be activate with the command `source activate QC_env`. And deactivated with `source deactivate`.  
For now, just create the environenment, we will need it soon.

**_STOP HERE AND GO TO THE QC & TRIMMING PART_**

**Anvi'o**  
Create a virtual environment for Anvi'o and install all dependencies using Bioconda. (takes 5–10 min)  
```
module load bioconda/3
conda create -n anvio3 -c bioconda -c conda-forge python=3.5.4 gsl anvio`  
```

Let's test it  
```
# Activate the Anvi'o virtual environment
source activate anvio3
# Run the mini test
anvi-self-test --suite mini
```
We will also need to install NCBIs COG databases and reformat them so they can be used later. The formatting step includes changing reorganizing information in raw files, serializing a very large text file into binary Python object for fast access while converting protein IDs to COGs, and finally generating BLAST and DIAMOND search databases.

```
anvi-setup-ncbi-cogs --num-threads 4
```
All should be good and we can close the virtual environment.  
`source deactivate`  

**CheckM**  
For assessing the quality of recovered genomes
```
conda create -n checkm pplacer checkm-genome numpy python=2
```

**Centrifuge**  
For taxonomic annotation of contigs in Anvi'o. Go again to the application folder and get the programs from GitHub using command `git`. Anvi'o relies on an older version ("branch") of the program, so we need to checkout the branch specified.  
You can read more about Centrifuge from the website where we clone it.
```
cd $USERAPPL
git clone https://github.com/infphilo/centrifuge
cd centrifuge
# We need a certain version, so checkout the branch specified
git checkout 30e3f06ec35bc83e430b49a052f551a1e3edef42
make
# Test it, should be version 1.0.2-beta  
`./centrifuge --version`  
```
Download the pre-computed indexes for centrifuge (Can take 10 min).  
Since they are very big, it's better to put them in the `$WRKDIR`, since the home directory is quite small and not meant for storage for large file.  
```
cd $WRKDIR
# make a folder for the indices and download the indices there
mkdir centrifuge_db
cd centrifuge_db
wget ftp://ftp.ccb.jhu.edu/pub/infphilo/centrifuge/data/p+h+v.tar.gz
# After download, unpack the files and remove the tar file.
tar -zxvf p+h+v.tar.gz && rm -rf p+h+v.tar.gz
```

Set an environmental variable pointing to the centrifuge folder.
You need to change the path to _**your**_ centrifuge folder.
```
export CENTRIFUGE_BASE="/wrk/YOURUSERNAME/centrifuge_db"
echo $CENTRIFUGE_BASE
# Needs to be done every time after logging out
```
**Optional**  
######################################################  
OR you can add it to your `.bashrc`.  
Go to home folder and open `.bashrc` with a text editor.  
Add things after the `# User specific aliases and functions`. Make sure they pointy to the right place on your own folders.  
```
export CENTRIFUGE_BASE="$WRKDIR/centrifuge_db"
# You can add also the centrifuge executable to your PATH
export PATH=$PATH:$USERAPPL/centrifuge
```
If you did set the env variable before, you can remove it first and then set it thru `.bashrc`.  
```
unset CENTRIFUGE_BASE
echo $CENTRIFUGE_BASE # This should give an empty row at this point

#Then run
source .bashrc
# And test that it worked.
centrifuge --version
echo $CENTRIFUGE_BASE
```
######################################################  

**Metaxa2**  
For taxonomic profiling of the samples using the trimmed reads.
```
cd $USERAPPL
wget http://microbiology.se/sw/Metaxa2_2.1.3.tar.gz
tar -xzvf Metaxa2_2.1.3.tar.gz
cd Metaxa2_2.1.3
```
Test it, you will need to load the biokit first, because Metaxa2 uses HMMER3 and BLAST.
```
module load biokit
./metaxa2 -i test.fasta -o TEMP --plus
```
Then you can remove the results  
`rm TEMP*`  

You can also add Metaxa2 to your PATH (go to --> `.bashrc`)  

**DIAMOND parser**  
Python script to parse the diamond output into a count table.  
You can put it to your `scripts` folder.  
```
cd scripts
git clone https://github.com/karkman/parse_diamond.git
```
