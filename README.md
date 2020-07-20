RTMerge: Correlate assay data from complex mixtures with sequence identity by merging output from CycLS and AutoPAMPA. 
=====================

Chad Townsend - University of California Santa Cruz - 2020
---------------------------------------------------
*RTMerge* is a python script designed to take output from [CycLS](https://github.com/LokeyLab/CycLS) and [AutoPAMPA](https://github.com/LokeyLab/AutoPAMPA) (whether PAMPA data or other supported assay types) and associate sequences with chromatographic peaks by retention time. Using these three tools together allows for a full pipeline to convert sequencing and assay data acquired from complex mixtures into assay data on individual compounds. Similarly to AutoPAMPA, *RTMerge* is controlled mainly through a configuration excel file and also outputs in excel format. In addition to combining CycLS and AutoPAMPA output, *RTMerge* also generates simple statistics to aid any downstream analysis.

More information on *RTMerge* is available in the supporting information of *this* paper (link pending).

The purpose of this readme is to help you install and use *RTMerge*.

## Table of Contents

-   [Installation](#installation)
-   [Usage](#usage)

    > -   [Required arguments](#required-arguments)
    > -   [Optional arguments](#optional-arguments)
    > -   [Input file preparation](#input-file-preparation)
    > -   [Interpreting output](#interpreting-output)

-   [Known Bugs and Issues](#known-bugs-and-issues)
-   [Bug Reports](#bug-reports)

## Installation:

Run the source code using Python from the command line.

### Requirements:

The easiest way to get the packages required to run *RTMerge* is to install the [Anaconda Python distribution](https://www.anaconda.com/) from Continuum Analytics, then install [openpyxl](https://bitbucket.org/openpyxl/openpyxl) using 
the "conda install package-name" command and [RDkit](https://github.com/rdkit/rdkit) as the its readme instructs.
The current version of *RTMerge* has been tested with the following versions of the packages mentioned above: Anaconda 4.3.1, openpyxl 2.4.1, and RDKit 2016.09.4

## Usage:

### Required arguments:

*config*: The file path to a specially formatted excel file containing most of the input parameters for *RTMerge*. An example configuration file is included in this repository. A thorough explanation of the parameters contained can be found below.

### Optional arguments:

*-o, --out*: Sets the prefix of the output files, ending in "\_Merged.xlsx". If not set, the output file is simply named "Merged.xlsx".

### Input File Preparation:

*RTMerge* expects a configuration file, which directs further input of assay and sequencing data in the form of the excel file output of AutoPAMPA and CycLS. An example file is included to demonstrate the proper format. Using it as a reference while reading the below is recommended.

#### Global Parameters
This worksheet is composed of settings which are "global" in the sense of affecting the entire job. Parameters are read in from columns one and two, with parameters recognized by name in column one.

*Library Constraint* A string representing the composition of the library using residue names from the amino acid database file. This is the same format as the CycLS constraint string, with amino acids present at the same position separated by commas and positions separated by semicolons. This string must cover all possible sequences for *RTMerge* to generate full SMILES strings for all reported sequences.

*Cyclic Library?* A boolean value, with True representing a cyclic library and False representing a linear library. Required for accurate SMILES string generation.

*Amino Acid Database File* Expects a string representing the file path to an amino acid database text file in the same format as used by CycLS, with each row containing an amino acid name and SMILES string separated by a tab. The SMILES strings must be of N-to-C format for the SMILES string generation and other statistics to be accurate. Ex: L    N\[C@@H\](CC(C)C)C(=O)O

*Mass Precision (m/z)* A float value representing the maximum difference between assay and sequencing exact masses before a match is refused. As CycLS attempts to get a high resolution mass for each cluster of MS<sup>2</sup> data, the acquisition precision should be the same for both assay and sequencing.

*Time Precision (s)* A float value representing the maximum difference between assay and sequencing retention times in seconds before a match is refused. 

#### Assay Data
This worksheet is used to specify the file paths to the assay data files and their assay types (columns one and two). The same file can be specified multiple times for different assay types as needed (types: PAMPA, Ratio, Integrate). If multiple assays types are merged simultaneously, columns for all assay types will be present for each row. More details on that in the next section.

#### Experiments
This worksheet is used to specify each separate experiment (defined here as a set of wells containing the same compounds) by name and the file path of the sequencing data corresponding to each experiment (columns one and two). Additionally, columns three and four contain offsets to the assay data retention times and masses to allow them to match to sequencing data in the presence of chromatographic drift or poor calibration. Despite these offsets, the retention times from the assay data are output rather than the sequencing retention times because the multi-event structure of the sequencing results in a low time-resolution.

If multiple experiments of the same name are given, the later mention will clobber the earlier one. If an experiment is present in multiple assay types, peaks will be labeled in the initial four columns (including retention time) by the first assay listed there is a match to. A sequence-peak match to any assay is sufficient and does not need to occur across all assays.

#### Interpreting Output:

*RTMerge* outputs a single excel sheet with two levels of column headers. The top level indicates the source of that column (sequencing, newly generated by *RTMerge*, or an assay data file) while the second level indicates the column contents. Only sequence-peak matches are output. Most sequencing statistics from CycLS and nearly all assay data columns are incorporated, and can be interpreted as suggested in their respective readme.

*RTMerge* generates additional columns including SMILES strings, RDKit's molAlogP statistic, a breakdown of residue identity, stereochemistry, and N-alkylation by position, and one column each for short-form stereochemical and N-alkylation patterns.

## Known Bugs and Issues:

Non-linear retention time drift between sequencing and assay data runs can cause poor retention time matching in the less-aligned regions.

## Bug Reports:

Please submit an issue or email me if you find a bug or find part of this Readme unclear!
