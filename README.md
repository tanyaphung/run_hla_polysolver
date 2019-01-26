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

* I use conda environment for this. I used miniconda. For instructions on how to install miniconda, see https://conda.io/en/master/miniconda.html

* We will create a conda environment called polysolver and install the necessay dependencies. 

```
conda create --name polysolver
conda install -c anaconda perl 
conda install -c bioconda samtools=1.9 novoalign=3.06.05 perl-array-utils=0.5 perl-parallel-forkmanager=2.02 perl-list-moreutils=0.428 perl-bioperl
conda install -c cyclus java-jdk
```
* Activate the conda environment:
```
conda activate polysolver
```

* Download picard
```
wget https://github.com/broadinstitute/picard/releases/download/1.120/picard-tools-1.120.zip
unzip picard-tools-1.120.zip
```

## Step 3: Edit the config file

* The default `config.sh` file (can be found in `scripts/`) use `setenv` but this commmand is for (t)csh. I am using bash so I have changed the config file to use `export` instead. 
* The default `config.sh` file asks to give the directory to GATK. It actually looks for picard so you should give the path to the picard file that you downloaded in step 2. 
* The config file I use can be found in this repo.

## Step 4: Edit some other paths
* Type this into the terminal:
```
sed -i "s|/usr/bin/perl|$(which perl)|" scripts/*
```
* Without this, an error called undefined symbol: Perl_xs_handshake will be invoked. 

## Step 5: Edit the script `shell_first_allele_calculations`
* With the version of polysolver that I downloaded, there is a bug  in the script `shell_first_allele_calculations`. 
  - In the line `SAMTOOLS sort -n $outDir/temp.$i.bam $outDir/nv.complete.chr6region.R0k6.csorted.REF_$i`, the output symbol `>` is missing and the output file should be `.bam`.
  - To fix this, do:

  ```
  vim scripts/shell_first_allele_calculations
  $SAMTOOLS sort -n $outDir/temp.$i.bam >  $outDir/nv.complete.chr6region.R0k6.csorted.REF_$i.bam
  ```
  - If you don't do this, an error will be invoked, such as saying that the file nv.complete.chr6region.R0k6.csorted.REF_hla_c_18_06 does not exist

## Step 6: Run

```
source scripts/config.sh
PERL5LIB=/home/tphung3/softwares/miniconda3/envs/polysolver/lib/site_perl/5.26.2/ scripts/shell_call_hla_type test/test.bam Unknown 1 hg19 STDFQ 0 out
```











