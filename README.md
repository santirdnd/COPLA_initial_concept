# COPLA
 A taxonomic classifier of plasmids

## How to install COPLA

### Dependencies

COPLA needs several third party programs and python libraries for its operation. Following list details the program versions used while developing COPLA:

- BLAST+ 2.9.0
- Prodigal v2.6.3
- HMMER v3.3
- PlasmidFinder 2.1
- MacSyFinder 1.0.5
- graph-tool 2.33

### Installation

First we need to download the COPLA scripts and databases from GitHub (change PROJECT_ROOT_DIRECTORY to suit your preferences):

~~~bash
$ PROJECT_ROOT_DIRECTORY=copla
$ git clone https://github.com/santirdnd/COPLA ${PROJECT_ROOT_DIRECTORY}
$ cd ${PROJECT_ROOT_DIRECTORY}
~~~

A conda environment has been provided for easy installation of COPLA requirements. Just run the next command:

~~~bash
$ conda env create -f copla.environment.yml -n copla
~~~

Or, if you want to use the exact environment used when developing COPLA, use this other command:

~~~bash
$ conda create --file copla.spec-file.txt -n copla
~~~

MacSyFinder, the program used for MPF typing, is a bit trickier to install as it is a Python2 program and has not been updated to use the latest trends in Python development. To easy its installation a specific conda environment has been provided. Run the following commands to install MacSyFinder:

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

COPLA database was built using NCBI RefSeq plasmid database (version 84). As the sequences of this dataset weight around 1GB once decompressed, they have been compressed (using xz format) and split into 100MB chunks (using split UNIX utility) so they could be uploaded to Github. To decompress them for local use just run the following commands:

~~~bash
$ cd databases/Copla_RS84
$ cat RS84f_seqs.fna.tar.xz.* | tar xJf -
$ cd ../..
~~~

Other info:

- MOBscan relaxase database was downloaded from https://castillo.unican.es/mobscan_about
- CONJScan, the database for MPF typing used by MacSyFinder, was downloaded at 2019-05-30 from https://github.com/gem-pasteur/Macsyfinder_models and updated using MOBscan relaxase profiles
- PlasmidFinder replicon database was downloaded at 2019-07-31 from https://bitbucket.org/genomicepidemiology/plasmidfinder_db.git

## Usage

COPLA can be used to predict the PTU to which a plasmid belongs. For this just its nucleotide sequence is needed:

~~~bash
$ bin/copla.py ${PLASMID_SEQ} databases/Copla_RS84/RS84f_sHSBM.pickle databases/Copla_RS84/CoplaDB.lst ${OUTPUT_DIRECTORY}
~~~

Draft plasmids are supported through a simple concatenation of their contigs.

If the plasmid ORFeome is available it can be used by COPLA (-a option) to obtain better MOB and MPF typings.

Changes in PTU host range due to query plasmid can be highlighted through the use of the taxonomic flags (-tk, -tp, -tc, -to, -tf, -tg, and -ts)

Knowledge of the plasmid topology is recommended for a better MPF typing. If not provided COPLA defaults to a circular topology. Multifasta input sequences are always processed using a linear topology.

## How COPLA works

Just as sHSBM, COPLA infers the PTU membership from the similarity (ANI) relationships among the plasmids of the database. After calculating the ANI similarity between the query and database plasmids, COPLA inserts the query into the reference network and performs an statistical search for similar plasmids.

## References

If you find COPLA useful please cite the following papers:

- PTU concept and graded plasmid host range definition:

	[1] Redondo-Salvo, S., Fernández-López, R., Ruiz, R., Vielva, L., de Toro, M., Rocha, E.P.C., Garcillán-Barcia, M.P., de la Cruz, F. “Pathways for Horizontal Gene Transfer in Bacteria Revealed by a Global Map of Their Plasmids.” *Nat Commun* **11**, 3602 (2020). https://doi.org/10.1038/s41467-020-17278-2

- COPLA algorithm description:

	[2] Redondo-Salvo, S., Bartomeus, R., Vielva, L., Tagg, K.A., Webb, H.E., Fernández-López, R., de la Cruz, F. “COPLA, a taxonomic classifier of plasmids.” (2020).
