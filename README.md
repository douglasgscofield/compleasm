## Getting Started
```sh
# download compleasm and its dependencies (miniprot and hmmsearch)
wget https://github.com/huangnengCSU/compleasm/releases/download/v0.2.2/compleasm-0.2.2_x64-linux.tar.bz2
tar -jxvf compleasm-0.2.2_x64-linux.tar.bz2

# Install pandas if necessary
pip3 install pandas                               # or conda install pandas

# Run compleasm if lineage is known
compleasm_kit/compleasm.py download primates      # download data to mb_download/
compleasm_kit/compleasm.py run -t16 -l primates -a hg38.fa -o hg38-mb  # run the pipeline

# Automatically detect lineage (requiring sepp)
conda install -c bioconda sepp                    # if sepp hasn't been installed
compleasm_kit/compleasm.py run --autolineage -a hg38.fa -o hs38-mb  
```

## Contents
- [Getting Started](#getting-started)
- [Installation](#installation)
  - [Conda Installation](#conda-installation)
  - [Docker Installation](#docker-installation)
  - [Singularity Installation](#singularity-installation)
  - [Release Installation](#release-installation)
  - [Manual Installation](#manual-installation)
- [Running](#running)
  - [Main Modules](#main-modules)
  - [Using `run` submodule to evaluate genome completeness from genome assembly](#using-run-submodule-to-evaluate-genome-completeness-from-genome-assembly)
  - [Using `analyze` submodule to evaluate genome completeness from provided miniprot alignment](#using-analyze-submodule-to-evaluate-genome-completeness-from-provided-miniprot-alignment)
  - [Using `download` submodule to download lineage](#using-download-submodule-to-download-lineage)
  - [Using `miniprot` submodule to run miniprot alignment](#using-miniprot-submodule-to-run-miniprot-alignment)
  - [Using `list` submodule to show local or remote Busco lineages](#using-list-submodule-to-show-local-or-remote-busco-lineages)
  - [Using `protein` submodule to assess the completeness of input proteins](#using-protein-submodule-to-assess-the-completeness-of-input-proteins)


## Installation
Compleasm is developed on python3.
- Prequisites:  
      [python3.*](https://www.python.org)  
      [miniprot](https://github.com/lh3/miniprot)   
      [hmmer](http://hmmer.org/)  
      [sepp](https://github.com/smirarab/sepp)  
- Dependencies:  
  [pandas](https://pandas.pydata.org/docs/getting_started/install.html#installing-from-pypi)

### Conda Installation
Compleasm can be installed with conda. If you don't have conda, please install [miniconda](https://docs.conda.io/en/latest/miniconda.html) or [anaconda](https://www.anaconda.com/products/individual) first. Then you can create a new environment with compleasm installed.
```angular2html
conda create -n <your_env_name> -c conda-forge -c bioconda compleasm
conda activate <your_env_name>
compleasm -h
```

### Docker Installation
Compleasm can be installed with docker. If you don't have docker, please install [docker](https://docs.docker.com/get-docker/) first. Then you can pull the docker image with compleasm installed.
```angular2html
VERSION=0.2.2
docker run huangnengcsu/compleasm:v${VERSION} compleasm -h
```

### Singularity Installation
Compleasm can be installed with singularity. If you don't have singularity, please install [singularity](https://docs.sylabs.io/guides/3.9/user-guide/quick_start.html#quick-installation-steps) first. Then you can pull the singularity image with compleasm installed.
```angular2html
VERSION=0.2.2
singularity exec docker://huangnengcsu/compleasm:v${VERSION} compleasm -h
```

### Release Installation
```angular2html
wget https://github.com/huangnengCSU/compleasm/releases/download/v0.2.2/compleasm-0.2.2_x64-linux.tar.bz2
tar -jxvf compleasm-0.2.2_x64-linux.tar.bz2
compleasm_kit/compleasm.py -h
```


### Manual Installation

#### Get compleasm:
```angular2html
git clone https://github.com/huangnengCSU/compleasm.git
```
You can run the `compleasm.py` script directly or copy it to other locations then run it.

#### Install miniprot:
```angular2html
git clone https://github.com/lh3/miniprot
cd miniprot && make
```

#### Install hmmer:
```angular2html
wget http://eddylab.org/software/hmmer/hmmer.tar.gz 
tar zxf hmmer.tar.gz
cd hmmer-3.3.2
./configure --prefix /your/install/path
make
make check
make install
```

#### Install sepp:
```angular2html
git clone https://github.com/smirarab/sepp.git
cd sepp
python setup.py config -c
python setup.py install
```

## Running

### Main Modules:

```angular2html
run             Run compleasm including miniprot alignment and completeness evaluation
analyze         Evaluate genome completeness from provided miniprot alignment
download        Download specified BUSCO lineage
list            List local or remote BUSCO lineages
miniprot        Run miniprot alignment
protein         Evaluate the completeness of provided protein sequences
```

### Using `run` submodule to evaluate genome completeness from genome assembly:

This will download the specified lineage (or automatically search for the best lineage with autolineage mode), align the
protein sequences in the lineage file to the genome sequence with miniprot, and parse the miniprot alignment result to
evaluate genome completeness.
#### Usage:
```angular2html
python compleasm.py run [-h] -a ASSEMBLY_PATH -o OUTPUT_DIR [-t THREADS] 
                        [-l LINEAGE] [-L LIBRARY_PATH] [-m {lite,busco}] [--specified_contigs SPECIFIED_CONTIGS [SPECIFIED_CONTIGS ...]] 
                        [--miniprot_execute_path MINIPROT_EXECUTE_PATH] [--hmmsearch_execute_path HMMSEARCH_EXECUTE_PATH] 
                        [--autolineage] [--sepp_execute_path SEPP_EXECUTE_PATH] 
                        [--min_diff MIN_DIFF] [--min_identity MIN_IDENTITY] [--min_length_percent MIN_LENGTH_PERCENT] 
                        [--min_complete MIN_COMPLETE] [--min_rise MIN_RISE]
```

#### Important parameters:
```angular2html
  -a, --assembly_path        Input genome file in FASTA format
  -o, --output_dir           The output folder
  -t, --threads              Number of threads to use
  -l, --lineage              Specify the name of the BUSCO lineage to be used. (e.g. eukaryota, primates, saccharomycetes etc.)
  -L, --library_path         Folder path to download lineages or already downloaded lineages. 
                             If not specified, a folder named "mb_downloads" will be created on the current running path by default to store the downloaded lineage files.
  -m, --mode                 The mode of evaluation. Default mode is busco. 
                             lite:  Without using hmmsearch to filtering protein alignment.
                             busco: Using hmmsearch on all candidate predicted proteins to purify the miniprot alignment to improve accuracy.
  --specified_contigs        Specify the contigs to be evaluated, e.g. chr1 chr2 chr3. If not specified, all contigs will be evaluated.
  --outs                     output if score at least FLOAT*bestScore [0.95]
  --miniprot_execute_path    Path to miniprot executable file. 
                             If not specified, compleasm will search for miniprot in the directory where compleasm.py is located, the current execution directory, and system environment variables.
  --hmmsearch_execute_path   Path to hmmsearch executable file.
                             If not specified, compleasm will search for hmmsearch in the directory where compleasm.py is located, the current execution directory, and system environment variables.
  --autolineage              Automatically search for the best matching lineage without specifying lineage file.
  --sepp_execute_path        Path to sepp executable file. This is required if you want to use the autolineage mode.
```

#### Threshold parameters:
```angular2html
  --min_diff               The thresholds for the best matching and second best matching. default=0.2
  --min_identity           The identity threshold for valid mapping results. default=0.4
  --min_length_percent     The fraction of protein for valid mapping results. default=0.6
  --min_complete           The length threshold for complete gene. default=0.9
```

#### Example:
```angular2html
# with lineage specified
python compleasm.py run -a genome.fasta -o output_dir -l eukaryota -t 8

# autolineage mode
python compleasm.py run -a genome.fasta -o output_dir -t 8 --autolineage

# with custom specified already downloaded lineage folder
python compleasm.py run -a genome.fasta -o output_dir -l eukaryota -t 8 -L /path/to/lineages_folder

# specify contigs
python compleasm.py run -a genome.fasta -o output_dir -l eukaryota -t 8 --specified_contigs chr1 chr2 chr3 chr4 chr5 chr6 chr7 chr8 chr9 chr10 chr11 chr12 chr13 chr14 chr15 chr16 chr17 chr18 chr19 chr20 chr21 chr22
```


### Using `analyze` submodule to evaluate genome completeness from provided miniprot alignment:

This will directly parse the provided miniprot alignment result to evaluate genome completeness. The execute command of miniprot should be like `miniprot --trans -u -I --outs=0.95 --gff -t 8 ref-file protein.faa > output.gff`.
#### Usage:
```angular2html
python compleasm.py analyze [-h] -g GFF -l LINEAGE -o OUTPUT_DIR [-t THREADS] [-L LIBRARY_PATH] 
                            [-m {lite,busco}] [--hmmsearch_execute_path HMMSEARCH_EXECUTE_PATH]
                            [--specified_contigs SPECIFIED_CONTIGS [SPECIFIED_CONTIGS ...]] 
                            [--min_diff MIN_DIFF] [--min_identity MIN_IDENTITY] [--min_length_percent MIN_LENGTH_PERCENT] 
                            [--min_complete MIN_COMPLETE] [--min_rise MIN_RISE]
```
#### Important parameters:
```angular2html
  -g, --gff                 Miniprot output gff file
  -l, --lineage             BUSCO lineage name
  -o, --output_dir          Output analysis folder
  -t, --threads             Number of threads to use
  -L, --library_path        Folder path to stored lineages.
  -m, --mode                The mode of evaluation. Default mode is fast. 
                            lite:  Without using hmmsearch to filtering protein alignment.
                            busco: Using hmmsearch on all candidate predicted proteins to purify the miniprot alignment to improve accuracy.
  --hmmsearch_execute_path  Path to hmmsearch executable
                            If not specified, compleasm will search for hmmsearch in the directory where compleasm.py is located, the current execution directory, and system environment variables.
  --specified_contigs       Specify the contigs to be evaluated, e.g. chr1 chr2 chr3. If not specified, all contigs will be evaluated.
```
Threshold parameters are same as `run` module.

#### Example:
```angular2html
# analysis with miniprot output gff file
python compleasm.py analyze -g miniprot.gff -o output_dir -l eukaryota -t 8

# specify contigs
compleasm analyze -g miniprot.gff -o output_dir -l eukaryota -t 8 --specified_contigs chr1 chr2 chr3 chr4 chr5 chr6 chr7 chr8 chr9 chr10 chr11 chr12 chr13 chr14 chr15 chr16 chr17 chr18 chr19 chr20 chr21 chr22
```


### Using `download` submodule to download lineage:

This will download the specified lineages and save to the specified folder.
#### Usage:
```angular2html
python compleasm.py download [-h] [-L LIBRARY_PATH] lineages [lineages ...]
```

#### Parameters:
```angular2html
positional arguments:  
  lineages                Specify the names of the BUSCO lineages to be downloaded. (e.g. eukaryota, primates, saccharomycetes etc.)

optional arguments:
  -L, --library_path      The destination folder to store the downloaded lineage files.
                          If not specified, a folder named "mb_downloads" will be created on the current running path by default.
```

#### Example:
```angular2html
python compleasm.py download saccharomycetes primates brassicales -L /path/to/lineages_folder
```
or
```angular2html
python compleasm.py download saccharomycetes,primates,brassicales -L /path/to/lineages_folder
```


### Using `miniprot` submodule to run miniprot alignment:

This will run miniprot alignment and output the gff file.
#### Usage:
```angular2html
python compleasm.py miniprot [-h] -a ASSEMBLY -p PROTEIN -o OUTDIR [-t THREADS] [--miniprot_execute_path MINIPROT_EXECUTE_PATH]
```

#### Important parameters:
```angular2html
  -a, --assembly             Input genome file in FASTA format
  -p, --protein              Input protein file
  -o, --outdir               Miniprot alignment output directory
  -t, --threads              Number of threads to use
  --outs                     output if score at least FLOAT*bestScore [0.95]
  --miniprot_execute_path    Path to miniprot executable file. 
                             If not specified, compleasm will search for miniprot in the directory where compleasm.py is located, the current execution directory, and system environment variables.
```

#### Example:
```angular2html
python compleasm.py miniprot -a genome.fasta -p protein.faa -o output_dir -t 8
```


### Using `list` submodule to show local or remote Busco lineages:

This will list the local or remote BUSCO lineages.
#### Usage:
```angular2html
python compleasm.py list [-h] [--remote] [--local] [-L LIBRARY_PATH]
```

#### Important parameters:
```angular2html
  --remote             List remote BUSCO lineages
  --local              List local BUSCO lineages
  -L, --library_path   Folder path to stored lineages.
```

#### Example
```angular2html
# list local lineages
python compleasm.py list --local -L /path/to/lineages_folder

# list remote lineages
python compleasm.py list --remote
```


### Using `protein` submodule to assess the completeness of input proteins:

This will evaluate the completeness of input proteins.

```angular2html
python compleasm.py protein [-h] -p PROTEINS -l LINEAGE -o OUTDIR [-t THREADS]
                            [-L LIBRARY_PATH]
                            [--hmmsearch_execute_path HMMSEARCH_EXECUTE_PATH]
```

#### Important parameters:

```angular2html
-p, --proteins             Input protein file
-l, --lineage              BUSCO lineage name
-o, --outdir               Output analysis folder
-t, --threads              Number of threads to use
-L, --library_path         Folder path to stored lineages
--hmmsearch_execute_path   Path to hmmsearch executable.
                           If not specified, compleasm will search for miniprot in the directory where compleasm.py is located, the current execution directory, and system environment variables.
```

#### Example:

```angular2html
python compleasm.py protein -p input.faa -l eukaryota -t 8 -o output_dir
```

