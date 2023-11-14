# Sort and Assemble
[![Open Source Starter Files](https://github.com/nrminor/sort-and-assemble/actions/workflows/open-source-starter.yaml/badge.svg)](https://github.com/nrminor/sort-and-assemble/actions/workflows/open-source-starter.yaml) [![Docker CI](https://github.com/nrminor/sort-and-assemble/actions/workflows/docker-image.yaml/badge.svg)](https://github.com/sort-and-assemble/ALPINE/actions/workflows/docker-image.yaml) [![Go](https://github.com/nrminor/sort-and-assemble/actions/workflows/go.yml/badge.svg)](https://github.com/nrminor/sort-and-assemble/actions/workflows/go.yml)

This pipeline takes Oxford Nanopore reads from B-Cell IgM amplicons, sorts out the reads from each amplicon, and assembles them into the most likely haplotypes using somatic variants.

In short, the pipeline does the following:
1. Merges compressed Nanopore FASTQs based on sample barcodes with `seqkit scat`.
2. Discovers adapter sequences with `bbmerge` for usage in `bbduk` in step 4.
3. Splits each sample's FASTQ into one FASTQ for each primer of interest, as well as an unprimed FASTQ. To do this, it uses `seqkit grep` to search for the forward or reverse primer sequence in each read.
4. Uses `bbduk` to trim to a minimum read length, minimum quality, and to remove adapter and primer sequences.
5. Uses `seqkit stats` and `seqkit amplicon` to output statistics for each sample's reads and amplicons.
6. Uses `csvtk` to visualize some of those statistics.
7. Runs `vsearch --cluster_fast` on the reads for each primer of interest to provide a FASTA of IgM heavy and light haplotype consensus sequences.
8. Searches IgBLAST for each of those consensus sequences to find the closest matches.

An example command after cloning this repo would be:
```
nextflow run . \
--fastq_dir "~/Documents/fastq_pass"
--primer_table "resources/primers.csv"
```
