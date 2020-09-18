# COPLA
 A taxonomic classifier of plasmids

## How to install COPLA

### Dependencies

COPLA needs several third party programs and python libraries for its operation. The following list details the specific versions used while developing COPLA:

- BLAST+ 2.9.0
- Prodigal v2.6.3
- HMMER v3.3
- PlasmidFinder 2.1
- MacSyFinder 1.0.5
- graph-tool 2.33

### Installation

First we need to download from GitHub the scripts and databases of the project. Please, change PROJECT_ROOT_DIRECTORY variable to suit your preferences:

~~~bash
$ PROJECT_ROOT_DIRECTORY=copla
$ git clone https://github.com/santirdnd/COPLA ${PROJECT_ROOT_DIRECTORY}
$ cd ${PROJECT_ROOT_DIRECTORY}
~~~

A conda environment has been provided for an easy installation of COPLA requirements. Just run the next command:

~~~bash
$ conda env create -f copla.environment.yml -n copla
~~~

Or, if you want to use the exact environment used while developing COPLA, use this other command:

~~~bash
$ conda create --file copla.spec-file.txt -n copla
~~~

MacSyFinder, the program used for MPF typing, is a bit trickier to install as it is a Python2 program and has not been updated to the latest trends in Python development. A specific conda environment is used to facilitate its installation. Run the following commands to install MacSyFinder:

~~~bash
$ conda env create -f macsyfinder.environment.yml -n macsyfinder
$ conda activate macsyfinder
$ cd ${CONDA_PREFIX}
$ wget https://github.com/gem-pasteur/macsyfinder/archive/macsyfinder-1.0.5.tar.gz
$ tar zxf macsyfinder-1.0.5.tar.gz
$ wget https://github.com/gem-pasteur/macsyview/archive/macsyview-1.0.1.tar.gz
$ tar zxf macsyview-1.0.1.tar.gz
$ mv macsyfinder-macsyfinder-1.0.5 src
$ mv macsyview-macsyview-1.0.1 src/macsyview
$ cd src
$ python setup.py build
$ python setup.py test -vv
$ python setup.py install --prefix=${CONDA_PREFIX}
$ conda deactivate
$ cd ${PROJECT_ROOT_DIRECTORY}
~~~

COPLA database was built using NCBI RefSeq plasmid database (release 84). As the sequences of this dataset weight around 1GB once decompressed, they have been compressed (using xz format) and split into 100MB chunks (using split UNIX utility) so they could be uploaded to Github. We need to xtract this dataset before first COPLA use, just run the following commands:

~~~bash
$ cd databases/Copla_RS84
$ cat RS84f_seqs.fna.tar.xz.* | tar xJf -
$ cd ../..
~~~

Other relevant info:

- MOBscan relaxase database was downloaded from https://castillo.unican.es/mobscan_about
- CONJScan, the database for MPF typing used by MacSyFinder, was downloaded at 2019-05-30 from https://github.com/gem-pasteur/Macsyfinder_models and updated using MOBscan relaxase profiles
- PlasmidFinder replicon database was downloaded at 2019-07-31 from https://bitbucket.org/genomicepidemiology/plasmidfinder_db.git

## Usage

COPLA can be used to predict the PTU to which a plasmid belongs. For this just its nucleotide sequence is needed:

~~~bash
$ bin/copla.py test/NZ_CP028167.1.fna \
    databases/Copla_RS84/RS84f_sHSBM.pickle \
    databases/Copla_RS84/CoplaDB.lst \
    test/NZ_CP028167.1_output
~~~

Draft plasmids are supported through a simple concatenation of their contigs.

If the plasmid ORFeome is available it can be used by COPLA (-a option) to obtain better MOB and MPF typings.

Knowledge of the query topology is recommended for a better MPF typing. If not provided COPLA defaults to a circular topology. Multifasta input sequences are always processed using a linear topology.

Changes in PTU host range due to query plasmid can be highlighted through the use of the taxonomic flags (-tk, -tp, -tc, -to, -tf, -tg, and -ts)

Next command shows a complete example of COPLA use:

~~~bash
$ bin/copla.py test/NZ_CP028329.1.fna \
    databases/Copla_RS84/RS84f_sHSBM.pickle \
    databases/Copla_RS84/CoplaDB.lst \
    test/NZ_CP028329.1_output \
    -a test/NZ_CP028329.1.faa \
    -t circular \
    -tk Bacteria \
    -tp Firmicutes \
    -tc Bacilli \
    -to Lactobacillales \
    -tf Lactobacillaceae \
    -tg Lactobacillus \
    -ts 'Lactobacillus sp. D1501'
~~~

## How COPLA works

Just as sHSBM, COPLA infers the PTU membership from the similarity (ANI) relationships among the plasmids of the database. After calculating the ANI similarity between the query and database plasmids, COPLA inserts the query into the reference network and performs an statistical search for similar plasmids.  

### Troubleshooting


## References

If you find COPLA useful please cite the following papers:

- PTU concept and graded plasmid host range definition:

	[1] Redondo-Salvo, S., Fernández-López, R., Ruiz, R., Vielva, L., de Toro, M., Rocha, E.P.C., Garcillán-Barcia, M.P., de la Cruz, F. “Pathways for Horizontal Gene Transfer in Bacteria Revealed by a Global Map of Their Plasmids.” *Nat Commun* **11**, 3602 (2020). https://doi.org/10.1038/s41467-020-17278-2

- COPLA algorithm description:

	[2] Redondo-Salvo, S., Bartomeus, R., Vielva, L., Tagg, K.A., Webb, H.E., Fernández-López, R., de la Cruz, F. “COPLA, a taxonomic classifier of plasmids.” (2020).
