ARTIC Viral Pipeline Analysis Project
===================================
A project-based analysis using the `Artic Fieldbioinformatics Pipeline` for viral pathogen analysis.


Contents
========

* [About the Artic Pipeline](#about-the-artic-pipeline)
* [Installation](#installation)
* [Usage](#usage)
* [Getting Started](#getting-started)
   * [Structure of Input Data](#structure-of-input-data)
   * [Configuration of Input Data](#configuration-of-input-data)
      * [Sequenced Data](#sequenced-data)
      * [Samplesheet](#samplesheet)
      * [Primer Scheme](#primer-scheme)
         * [Primer Scheme Reference (.reference.fasta) Structure](#primer-scheme-reference-referencefasta-structure)
         * [Primer Scheme Bed (.scheme.bed) Structure](#primer-scheme-bed-schemebed-structure)


About the Artic Pipeline
========================
`artic` is a pipeline and set of accompanying tools for working with viral nanopore sequencing data, generated from tiling amplicon schemes.

It is designed to help run the `artic` bioinformatics protocols; for example the SARS-CoV-2 coronavirus protocol.

Features include:
* read filtering
* primer trimming
* amplicon coverage normalisation
* variant calling
* consensus building

There are 2 workflows baked into this pipeline, one which uses signal data (via nanopolish) and one that does not (via medaka).

Installation
============
Guidance on the installation of the Pipeline can be found at the author's github page: the [artic-network](https://github.com/artic-network/fieldbioinformatics)

Usage
=====
Documentation of the pipeline can be found at: [artic docs](http://artic.readthedocs.io/en/latest/?badge=latest)

Getting Started
===============
The bash script for the pipeline is slightly modified for this author's purpose of analysis.

## Structure of Input Data
The directory structure of the nanopore sequencing data may be maintained as in the default structure created by MinKNOW with the basecalling enabled. There is no need to rename or restructure the data generated by MinKNOW.

```
/data/
    experiment_group_1/
        sample_id_1/
             runid_1/
                 fastq_pass/
                       barcode01
                            A_A1.fastq
                            A_A2.fastq
                       barcode02
                            A_A1.fastq
                            A_A2.fastq
                       barcode03
                            A_A1.fastq
                            A_A2.fastq
                 fast5_pass/
                       barcode01
                            A_A1.fast5
                            A_A2.fast5
                       barcode02
                            A_A1.fast5
                            A_A2.fast5
                       barcode03
                            A_A1.fast5
                            A_A2.fast5
    experiment_group_2/
        sample_id_2/
             runid_2/
                 fastq_pass/
                       barcode01
                            A_A1.fastq
                            A_A2.fastq
                       barcode02
                            A_A1.fastq
                            A_A2.fastq
                       barcode03
                            A_A1.fastq
                            A_A2.fastq
                 fast5_pass/
                       barcode01
                            A_A1.fast5
                            A_A2.fast5
                       barcode02
                            A_A1.fast5
                            A_A2.fast5
                       barcode03
                            A_A1.fast5
                            A_A2.fast5                     
```

NOTE: In cases where the sequenced data were compressed, the files would have a ".gz" ending. Example. A_A1.fastq.gz

NOTE: The relevant folders for the `artic` analysis are the fastq_pass or fast5_pass directories depending on the workflow used in the analysis (medaka for fastq/basecalled data, and nanopolish for fast5/signal data). For the purpose of this repo, analysis using fastq data will be highlighted.

## Configuration of Input Data
### Sequenced Data
Compressed sequenced data from fastq_pass directory (fastq.gz) should be unzipped before they can be analysed using the pipeline. If the data is already uncompressed, this step can be skipped. The following command can be used to carry out the task:

```bash
cd /path/to/fastq_pass/
gunzip -r *
```


### Samplesheet
For a sequencing run with multiple samples that were barcoded, a sample sheet file named `samplesheet.csv` in the ".csv" format is required. This file must be generated by the user of this script. The sample sheet should contain two columns: "sample" and "barcode" columns.

NOTE: These are to be written as seen (with no whitespace, capitalised letters, etc).

An example samplesheet.csv file for a run with five multiplexed samples using barcodes (rapid or native) is shown below:

```
sample,barcode
sample1,34
sample2,35
sample3,38
sample4,39
sample5,46
```

The values of the two columns can have any of the following characters defining each sample and barcode: Barcodes require only numbers whilst sample names may have lowercase letters, uppercase letters, numbers, hyphen, underscore, and the full stop. Whitespaces are generally not allowed in the samplesheet.


### Primer Scheme
This directory contains the information about the primer scheme of interest and allows the `artic` pipeline to perform relevant downstream analysis. The `artic` pipeline comes along with preset primer schemes and also allows a user to use any custom primer scheme designed for viruses, given a few rules are followed.

For custom primers, kindly check the `artic` [primer-scheme.md](https://github.com/artic-network/fieldbioinformatics/blob/master/docs/primer-schemes.md) file for the most up to date descriptions of these formats.

For this demonstration, MPXV-2022 would be used as the virus name and it is best to be consistent with the naming conventions across folder and files. The following files are required for the `artic` pipeline:

```
MPXV-2022.reference.fasta
MPXV-2022.scheme.bed
```

Within the test-data directory of the fieldbioinformatics directory, the folder structure should be in the form: "primer-schemes/virus-name/version"

Here is an example:

```
fieldbioinformatics
└── test-data
    └── primer-schemes
        |-- MPXV-2022
        |   └── V1
        |       |-- MPXV-2022.reference.fasta
        |       └── MPXV-2022.scheme.bed
        |-- nCoV-2019                
        |   |-- V1
        |   |   |-- nCoV-2019.reference.fasta
        |   |   └── nCoV-2019.scheme.bed
        |   |-- V2
        |   |   |-- nCoV-2019.reference.fasta
        |   |   └── nCoV-2019.scheme.bed
        |   └── V3
        |       |-- nCoV-2019.reference.fasta
        |       └── nCoV-2019.scheme.bed
        └── midnight
            └── V1
                |-- midnight.reference.fasta
                └── midnight.scheme.bed
```

The output files in each version folder will increase when the pipeline is run. This is because the pipeline generates some index files for the primer scheme files.

The path to the primer scheme of interest should be placed into the script before execution.

#### Primer Scheme Reference (.reference.fasta) Structure
The reference file contains he reference genome of the target virus. A sample of the first few lines of the nCoV-2019.reference.fasta (reference genome for the `artic` SARS-CoV-2 primer scheme) is shown below:

```
>MN908947.3
ATTAAAGGTTTATACCTTCCCAGGTAACAAACCAACCAACTTTCGATCTCTTGTAGATCT
GTTCTCTAAACGAACTTTAAAATCTGTGTGGCTGTCACTCGGCTGCATGCTTAGTGCACT
CACGCAGTATAATTAATAACTAATTACTGTCGTTGACAGGACACGAGTAACTCGTCTATC
TTCTGCAGGCTGCTTACGGTTTCGTCCGTGTTGCAGCCGATCATCAGCACATCTAGGTTT
CGTCCGGGTGTGACCGAAAGGTAAGATGGAGAGCCTTGTCCCTGGTTTCAACGAGAAAAC
ACACGTCCAACTCAGTTTGCCTGTTTTACAGGTTCGCGACGTGCTCGTACGTGGCTTTGG
AGACTCCGTGGAGGAGGTCTTATCAGAGGCACGTCAACATCTTAAAGATGGCACTTGTGG
CTTAGTAGAAGTTGAAAAAGGCGTTTTGCCTCAACTTGAACAGCCCTATGTGTTCATCAA
ACGTTCGGATGCTCGAACTGCACCTCATGGTCATGTTATGGTTGAGCTGGTAGCAGAACT
```

#### Primer Scheme Bed (.scheme.bed) Structure
The primer scheme bed should be in the following form:
```
reference       start   stop    primer_name     pool
```

Here is the first few lines of the nCoV-2019.scheme.bed (artic V3 primer scheme.bed).
```
MN908947.3	30	54	nCoV-2019_1_LEFT	nCoV-2019_1	
MN908947.3	385	410	nCoV-2019_1_RIGHT	nCoV-2019_1	
MN908947.3	320	342	nCoV-2019_2_LEFT	nCoV-2019_2	
MN908947.3	704	726	nCoV-2019_2_RIGHT	nCoV-2019_2	
MN908947.3	642	664	nCoV-2019_3_LEFT	nCoV-2019_1	
MN908947.3	1004	1028	nCoV-2019_3_RIGHT	nCoV-2019_1	
MN908947.3	943	965	nCoV-2019_4_LEFT	nCoV-2019_2	
MN908947.3	1312	1337	nCoV-2019_4_RIGHT	nCoV-2019_2	
	
```

The scheme.bed file is always tab-delimited with the .bed file format.





