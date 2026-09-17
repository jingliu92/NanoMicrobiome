# Proposal

## Development of NanoMicrobiome: A Modular Oxford Nanopore Bioinformatics Platform for Microbiome Analysis and Foodborne Pathogen Detection

### Background

Microbiome analysis has become an essential tool for food safety, animal health, environmental monitoring, and microbial ecology. Rapid and accurate characterization of microbial communities is critical for detecting foodborne pathogens, monitoring microbial succession during food processing and storage, investigating contamination sources, and understanding the ecological dynamics of complex microbial ecosystems.

Conventional culture-based methods remain the regulatory standard for foodborne pathogen detection; however, they are labor-intensive, require several days to complete, and cannot detect viable but non-culturable microorganisms. High-throughput sequencing has transformed microbiome research by enabling culture-independent analysis of complex microbial communities. Amplicon sequencing of the bacterial 16S rRNA gene and fungal internal transcribed spacer (ITS) regions is widely used for microbial community profiling, while shotgun metagenomic sequencing enables strain-level taxonomic identification and functional characterization, including antimicrobial resistance (AMR) and virulence-associated genes.

Oxford Nanopore Technologies (ONT) has emerged as a powerful long-read sequencing platform capable of generating both full-length and targeted amplicon sequences as well as real-time shotgun metagenomic data. Nanopore sequencing supports full-length 16S rRNA analysis while also enabling sequencing of commonly targeted hypervariable regions such as V1–V3, V3–V4, and V4, providing flexibility for different experimental designs and compatibility with existing microbiome studies. Similarly, ONT supports fungal community profiling using full-length or targeted ITS amplicons in addition to culture-independent shotgun metagenomic sequencing.

Compared with conventional short-read sequencing technologies, Oxford Nanopore offers several advantages, including portable instrumentation, long-read capability, real-time data generation, and improved taxonomic resolution. These features make ONT particularly attractive for rapid microbiome characterization, foodborne pathogen surveillance, and field-deployable sequencing applications.

Although numerous bioinformatics tools exist for individual sequencing applications, most are designed for either amplicon or shotgun sequencing alone, and few provide an integrated framework optimized specifically for Oxford Nanopore data. Existing workflows often require substantial manual intervention, depend on multiple independent software packages, and lack standardized reporting. Furthermore, there is currently no unified, modular platform that supports targeted 16S sequencing, full-length 16S analysis, ITS fungal profiling, and Oxford Nanopore shotgun metagenomics within a single reproducible workflow.

To address these limitations, this project proposes the development of NanoMicrobiome, an open-source, modular bioinformatics platform specifically designed for Oxford Nanopore microbiome analysis. The platform will provide reproducible workflows for multiple sequencing applications while maintaining a consistent user interface, standardized reporting, and scalable computational architecture.

---

## Objective

The overall objective of this project is to develop NanoMicrobiome, a modular, reproducible bioinformatics platform for comprehensive microbiome analysis using Oxford Nanopore sequencing technology.

The platform will support multiple sequencing applications through independent but interoperable workflows, enabling users to analyze bacterial and fungal communities, detect foodborne pathogens, and characterize microbial functions using a unified computational framework.

Specific objectives include:

1. Develop a modular Oxford Nanopore bioinformatics platform using Snakemake to provide reproducible, scalable, and extensible microbiome analysis workflows.

2. Develop a 16S rRNA analysis workflow supporting both full-length 16S sequencing and targeted hypervariable regions (e.g., V1–V3, V3–V4, and V4), including:
- Basecalling and quality control
- Primer trimming
- Taxonomic classification
- Alpha and beta diversity analysis
- Differential abundance analysis
- Publication-quality visualization

3. Develop an ITS analysis workflow supporting fungal community profiling using Oxford Nanopore sequencing, including taxonomic classification, diversity analysis, and visualization based on the UNITE reference database.

4. Implement a shotgun metagenomic workflow for culture-independent detection and characterization of foodborne pathogens, including:
- Dorado basecalling
- Quality control
- Host read removal
- Taxonomic classification
- Genome assembly
- Functional annotation
- Antimicrobial resistance gene detection
- Virulence factor identification.
5. Develop an integrated reporting framework that automatically generates standardized summary reports, interactive visualizations, publication-quality figures, and analytical statistics for all supported workflows.
6. Design NanoMicrobiome as an extensible platform that can be expanded to support additional Oxford Nanopore sequencing applications, reference databases, and downstream analytical modules.
---


## Proposed Platform Architecture

<img width="475" height="431" alt="image" src="https://github.com/user-attachments/assets/d84a9902-7b85-4834-9a02-99bbeee5bab2" />


## Proposed Workflow

The proposed workflow consists of two major components:

### Laboratory Workflow

* DNA extraction
* DNA quality assessment
* Library preparation using the Oxford Nanopore Ligation Sequencing Kit V14
* Optional native barcoding for multiplexed sequencing
* Oxford Nanopore MinION sequencing

Sequencing generates raw POD5 files that serve as input for downstream computational analysis.
### Device and Kits

| Item                                                            | Purpose                                                                                                                                                                                                                                        |      Needed for Your Project?     |
| --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------: |
| **MinION Mk1D**                                                 | The sequencing device that connects to your computer and performs real-time nanopore sequencing.                                                                                                                                               |            ✅ Essential            |
| **R10.4.1 DNA Flow Cells (FLO-MIN114)**                         | The consumable chip containing thousands of nanopores. DNA molecules pass through these pores to generate sequencing data. One flow cell is typically used per sequencing run (although it can sometimes be washed and reused).                |            ✅ Essential            |
| **Ligation Sequencing Kit V14 (SQK-LSK114)**                    | Library preparation kit. It repairs DNA ends, adds sequencing adapters and motor proteins so DNA can enter the nanopores. This is the recommended kit for **shotgun metagenomics** because it produces the highest yield and longest reads.    |            ✅ Essential            |
| **Native Barcoding Kit 24 (SQK-NBD114.24)** *(optional add-on)* | Adds unique DNA barcodes to each sample, allowing multiple samples (e.g., 2–24) to be pooled and sequenced on the same flow cell. After sequencing, the software separates the reads by barcode (demultiplexing).                              |           ✅ Recommended           |
| **Flow Cell Wash Kit**                                          | Cleans residual DNA from a used flow cell so it can potentially be reused for another run if sufficient nanopores remain active. This helps reduce consumable costs.                                                                           |              ✅ Useful             |
| **Control Expansion Kit**                                       | Contains a control DNA sample (typically lambda phage DNA) used to verify that the sequencing chemistry, flow cell, and instrument are functioning properly during initial setup or troubleshooting. It is **not** used for your food samples. | ⚪ Optional (included in the pack) |
---
### Library Preparation 

Convert purified genomic DNA into a sequencing-ready library by attaching Oxford Nanopore sequencing adapters to DNA fragments, enabling their recognition and translocation through nanopores during sequencing.


<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/26b8f745-9119-4904-9928-813577599385" />

#### Step 1. DNA End Repair & dA-Tailing

   DNA extracted from samples often has uneven or damaged ends. This step repairs those ends and adds a single adenine (A) overhang to each DNA fragment, creating compatible ends for adapter ligation.

- Reagent: Included in the Ligation Sequencing Kit V14.

- Output: Uniform DNA molecules ready for adapter attachment.
#### Step 2. Adapter Ligation
  
  Oxford Nanopore sequencing adapters are ligated onto both ends of each DNA fragment.

These adapters contain:
- Motor proteins that regulate DNA translocation through the nanopore.
- Sequencing adapters recognized by the MinION instrument.

Without these adapters, DNA cannot be sequenced.
- Reagent: Included in the Ligation Sequencing Kit V14.
- Output Sequencing-ready DNA library.

#### Step 3. Magnetic Bead Cleanup

Purify the library by removing excess enzymes, free adapters, salts, and short DNA fragments. This step improves sequencing efficiency and data quality.

- Reagent:  AMPure XP beads (or equivalent magnetic beads).
- Output: Clean, high-quality sequencing library.
  
#### Step 4. Native Barcoding (Optional)

If sequencing multiple samples simultaneously, unique barcode sequences are ligated to each sample before adapter ligation (or according to the kit workflow).
| Sample             | Barcode |
| ------------------ | ------- |
| Chicken breast     | BC01    |
| Spinach            | BC02    |
| Milk               | BC03    |
| Environmental swab | BC04    |

After sequencing, reads are separated computationally based on these barcodes.

- Kit: Native Barcoding Kit 24 (SQK-NBD114.24)

**This library is then loaded onto an R10.4.1 flow cell for sequencing on the MinION Mk1D.**
### Bioinformatics Workflow

FoodNanoDetect will be implemented as a modular **Nextflow** workflow to ensure reproducibility, scalability, and portability across local workstations, high-performance computing clusters, and cloud environments.

The pipeline consists of the following computational modules:

**Module 1 – Basecalling**

Raw nanopore signal data (POD5) will be converted into nucleotide sequences using **Dorado**, Oxford Nanopore's official high-accuracy basecaller. When barcoded libraries are used, demultiplexing will also be performed during this step.

**Input:** POD5

**Output:** FASTQ

---

**Module 2 – Read Quality Control**

Sequencing reads will undergo quality filtering using NanoFilt to remove low-quality and short reads prior to downstream analysis.

Quality metrics will be summarized using NanoPlot.

**Output:** High-quality FASTQ files

---

**Module 3 – Host DNA Removal**

Host-derived sequences originating from food matrices (e.g., chicken, beef, pork, seafood, or plant tissues) will be removed by mapping reads against the corresponding reference genome using Minimap2.

Removing host DNA reduces computational requirements while improving microbial detection sensitivity.
| Sample       | Host Genome                |
| ------------ | -------------------------- |
| Chicken meat | *Gallus gallus*            |
| Beef         | *Bos taurus*               |
| Pork         | *Sus scrofa*               |
| Seafood      | Species-specific reference |
| Vegetables   | Plant reference genome     |


**Output:** Host-depleted microbial reads

---

**Module 4 – Taxonomic Classification**

Microbial reads will be taxonomically classified using Kraken2 against a comprehensive microbial reference database.

Bracken will subsequently estimate species abundance by correcting classification biases inherent to k-mer-based classification.

Outputs include:

* Species identification
* Relative abundance
* Read counts
* Taxonomic composition

---

**Module 5 – Genome Assembly**

Microbial reads will be assembled into draft genomes using Flye, an assembler specifically designed for long-read sequencing technologies.

Genome assembly enables strain-level characterization and downstream functional analyses.

---

**Module 6 – Consensus Polishing**

Draft assemblies will be polished using Medaka to improve consensus accuracy and reduce sequencing errors associated with Nanopore reads.

---

**Module 7 – Genome Annotation**

Polished genomes will be annotated using Prokka to identify coding sequences, rRNA genes, tRNA genes, and predicted protein functions.

---

**Module 8 – Antimicrobial Resistance Detection**

Annotated genomes will be screened against the Comprehensive Antibiotic Resistance Database (CARD) and/or ResFinder to identify antimicrobial resistance genes and associated resistance mechanisms.

---

**Module 9 – Virulence Factor Detection**

Virulence-associated genes will be identified using the Virulence Factor Database (VFDB), allowing rapid assessment of pathogen pathogenicity and potential public health risk.

---

**Module 10 – Automated Reporting**

The final module will automatically generate an interactive report summarizing:

* Sequencing quality metrics
* Taxonomic composition
* Pathogen identification
* Relative abundance profiles
* Genome assembly statistics
* Antimicrobial resistance genes
* Virulence factors
* Interactive Krona visualizations
* Exportable PDF and HTML reports

The report will provide an integrated summary suitable for routine food safety monitoring and outbreak investigations.

---

## Computational Framework

FoodNanoDetect will be developed using the Nextflow workflow management system. Individual analytical modules will be containerized using Docker or Conda environments to ensure reproducibility across different computational platforms.

The modular architecture allows independent updating of each analysis component while maintaining a standardized end-to-end workflow.

---

## Expected Deliverables

The proposed project will generate:

* A standardized Oxford Nanopore shotgun metagenomic workflow for food safety applications.
* FoodNanoDetect, an open and reproducible bioinformatics pipeline for pathogen detection.
* Automated computational workflows for microbial classification, genome assembly, antimicrobial resistance analysis, and virulence profiling.
* Standard operating procedures for laboratory and computational analyses.
* Automated reporting tools suitable for routine surveillance, shelf-life studies, environmental monitoring, and outbreak investigations.

---

## Expected Impact

FoodNanoDetect will provide a rapid, culture-independent platform for comprehensive microbial surveillance in food systems. By integrating Oxford Nanopore sequencing with automated bioinformatics analysis, the proposed platform will substantially reduce analysis time while improving the detection of unculturable microorganisms and enhancing strain-level characterization.

The modular design will facilitate future expansion to include machine learning-based spoilage prediction, contamination source tracking, quantitative microbial risk assessment, and predictive food safety analytics. Ultimately, FoodNanoDetect has the potential to become a standardized computational framework supporting food manufacturers, regulatory agencies, and research laboratories in modern food safety surveillance.


These advantages make ONT particularly suitable for food safety surveillance, environmental monitoring, and outbreak investigations.

# Run code:
```
cd /git/bioinf_MinION
```
```
./metagminion.sh \
    /data/pipelineInput/Nanopore/MG_09022026/09022026.fastq.gz \
    /data/pipelineOutput/Nanopore/MG_09022026 \
    ITS \
    minimap2 \
    /data/pipelineInput/Nanopore/MG_09022026/sample_map.tsv \
    --skip-demultiplex
```
