# Installation and usage

This page provds the instructions to install the software environment used for the analysis of SARS-CoV-2 origins, and some basic instructions to run the analyses. 

## Collaborators

- Etienne Decroly
- Jacques van Helden (<https://orcid.org/0000-0002-8799-8584>)
- Erwan Sallard	(<erwan.sallard@ens.psl.eu>)
- José Halloy
- Didier Casane



## Installation

### Getting a clone of the github repository

````{bash}
git clone https://github.com/jvanheld/coronavirus_insertions.git
```

The code can then be updated as follows

```{bash}
cd coronavirus_insertions
git pull
```


### Installing the software environment

The whole software environment required to reproduce these analyses can be easily installed with miniconda.


```
## List the targets
make -f scripts/makefiles/01_software-environment.mk

## Install the environment
make -f scripts/makefiles/01_software-environment.mk install_env

```

The software environment can then be loaded with the command

```
conda activate covid-19
```

Additional tasks are described in the help message

```
make -f scripts/makefiles/01_software-environment.mk 
```

## Usage

### Phylogenetic inference

The commands to run the phylogenetic analysis of coronavirus genomic sequences can be listed as follows. 

```
make -f scripts/makefiles/02_genome-analysis.mk
```

The script includes parameters that can be modified to address specific querries or to tune the computing according to your local configurtion. 


```
make -f scripts/makefiles/02_genome-analysis.mk  list_param
```

In particular the variable `PHYML_THREADS` should be adapted to the number of CPUs of your computer. 


#### Inferring the tree of virus strains from full genome alignments

```

```


### Analysis of the spike protein sequences

The aim of this program is to retrieve from uniprot all the available sequences of spike proteins belonging to the specified taxa, to align them and to identify the insertions in one of these proteins (by default the spike of SARS-CoV-2).

The commands are specified in the make file `make -f scripts/makefiles/03_protein-alignments.mk`. 

The list of targets can be obtained with the following command.

```
make -f scripts/makefiles/03_protein-alignments.mk
```

You should first run one of the "uniprot_" functions, then "align_muscle" and finally "identify_insertion". For example:

```
make -f scripts/makefiles/03_protein-alignments.mk uniprot_sarbecovirus
make -f scripts/makefiles/03_protein-alignments.mk align_muscle
make -f scripts/makefiles/03_protein-alignments.mk identify_insertion
```

Results will be found in the "results/spike_protein" folder (namely the multiple alignment file in several formats, and a .csv file describing the position of the insertions in the reference protein).

#### Colorizing the inserts on the 3D structural model

The color_insertions.pml program enables to visualize the insertions in SARS-CoV-2 spike on a 3D structural model.

```
pymol scripts/pymol/color_insertions.pml
```

#### Comparing ACE2 proteins

ACE2 is the receptor of SARS-CoV-2. To determine which animals are susceptible to be infected by SARS-CoV-2 or similar viruses, we gathered the ACE2 sequences of numerous animals in the data/ACE2/ACE2.fa file. Our aim is to align them, construct a phylogenetic tree, and to determine the similarity of each protein with the human protein on the residues involved in spike binding.

To build the multiple alignment (saved in fasta and phylip format) between the ACE2 sequences:
```
make -f scripts/makefiles/05_ACE2_analysis.mk align_muscle_fasta_phylip
```
Once this is done, the corresponding tree can be built with:
```
make -f scripts/makefiles/05_ACE2_analysis.mk phyml_ACE2
```
and the comparison with the human protein can be performed with:
```
make -f scripts/makefiles/05_ACE2_analysis.mk compare_ACE2_with_human
```


## Running the scripts on the IFB core cluster


### Opening the connection 

```
## Define your login on the IFB-core cluster
IFB_LOGIN=jvanhelden

## open a connection to the cluster
ssh ${IFB_LOGIN}@core.cluster.france-bioinformatique.fr

cd coronavirus_insertions

```

### Loading the environment


```
module load conda 
conda activate covid-19

```

### Running a single task via srun

**Never run the tasks on the login node!**


```
## Run genome alignments
srun --cpus=50  --mem=32GB   --partition=fast  \
  make -f scripts/makefiles/02_genome-analysis.mk  \
      PHYML_THREADS=50 TIME='' \
      Sgenes_around-cov2
```

