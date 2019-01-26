# run_hla_polysolver

In this repo, I'm describing how to get the tool HLA polysolver (https://software.broadinstitute.org/cancer/cga/polysolver) to work. I spent a few hours figuring out how to get all the dependencies to run the software within a conda environment. I also found a couple of bugs in the codes (from the version that I downloaded) so I hope this step by step guide will help others as well.

## Step 1: Download POLYSOLVER

* After creating an account, navigate to https://software.broadinstitute.org/cancer/cga/polysolver_download. Scroll down to the end of the page, you will see an option to download the software. Since I will be running this on a cluster, I right-clicked to copy the link address. 

* Then, from your terminal, do:
```
wget https://software.broadinstitute.org/cancer/cga/sites/default/files/data/tools/polysolver/polysolver_v1.0.tar.gz
tar -xzvf polysolver_v1.0.tar.gz
```

* Now there should be a directory called `polysolver`. Change into that directory: 

```
cd polysolver/
```

## Step 2: Install the dependencies

* I use conda environment for this. To install conda, see: 

```
conda config --add channels bioconda
conda create --name polysolver
conda install -c anaconda perl 
conda install -c bioconda samtools=1.9 novoalign=3.06.05 perl-bio-db-sam=1.41 perl-array-utils=0.5 perl-parallel-forkmanager=2.02 perl-list-moreutils=0.428 perl-bioperl
conda install -c cyclus java-jdk
conda activate polysolver
```

* Download picard

```
wget https://github.com/broadinstitute/picard/releases/download/1.120/picard-tools-1.120.zip
unzip picard-tools-1.120.zip
```


## Step 3: Edit the config file

* The default `config.sh` file (can be found in `scripts/`) use `setenv` but this commmand is for (t)csh. I am using bash so I have changed the config file to use `export` instead. 
* The default `config.sh` file asks to give the directory to GATK. It actually looks for picard. 
* The config file I use can be found under 

## Edit some other paths

```
sed -i "s|/usr/bin/perl|$(which perl)|" scripts/*
```

## Edit the script

```
vim scripts/shell_first_allele_calculations
$SAMTOOLS sort -n $outDir/temp.$i.bam >  $outDir/nv.complete.chr6region.R0k6.csorted.REF_$i.bam
```

## Run

```
source scripts/config.sh
scripts/shell_call_hla_type test/test.bam Unknown 1 hg19 STDFQ 0 out_test
```











