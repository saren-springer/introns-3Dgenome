
# Analysis of Rare Intron Organization and Splicing in the 3D Genome 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.txt)

---

## Table of Contents
- [Source Publication](#source-publication)
- [Repository Structure](#repository-structure)
- [Reproducing the Analyses](#reproducing-the-analyses)
- [Citation](#citation)

---

## Source Publication
### ***Disentangling spatial organization and splicing of rare intron classes in the human genome***

*Saren M. Springer<sup>1,#</sup>, Katherine Fleck<sup>2,3,#</sup>, Kaitlin N. Girardini<sup>1</sup>, Sean M. Riccard<sup>2,3</sup>, Jelena Erceg<sup>2,3,4,\*</sup>, Rahul N. Kanadia<sup>1,3,\*</sup>*

*<sup>1</sup>Department of Physiology and Neurobiology, University of Connecticut, Storrs, CT 06269, USA  
<sup>2</sup>Department of Molecular and Cell Biology, University of Connecticut, Storrs, CT 06269, USA  
<sup>3</sup>Institute for Systems Genomics, University of Connecticut, Storrs, CT 06269, USA  
<sup>4</sup>Department of Genetics and Genome Sciences, University of Connecticut Health Center, Farmington, CT 06030, USA*  

<sup>#</sup>These authors contributed equally to this work.  
<sup>*</sup>Co-corresponding authors.

<br>

## Repository Structure
This repository contains scripts for chromoMap visualization, inter-intron distance calculation, intron density analysis, and bootstrapping and pollution analysis. See sections below for detailed contents and links.

```plaintext
introns-3Dgenome/
|-- README.md
|-- LICENSE.txt
|-- chromoMaps/
|   |-- chromoMap.md
|   `-- data/
|       |-- Homo_sapiens.GRCh38.Ens99.chromosomes.bed
|       `-- Homo_sapiens.GRCh38.Ens99.All_introns.txt
|-- inter_intron_distance/
|   |-- inter_intron_distance.md
|   `-- data/
|       |-- Homo_sapiens.GRCh38.Ens99.major_introns.bed
|       |-- Homo_sapiens.GRCh38.Ens99.major-like_introns.bed
|       |-- Homo_sapiens.GRCh38.Ens99.hybrid_introns.bed
|       |-- Homo_sapiens.GRCh38.Ens99.minor-like_introns.bed
|       |-- Homo_sapiens.GRCh38.Ens99.minor_introns.bed
|       `-- Homo_sapiens.GRCh38.Ens99.non-canonical_introns.bed
|-- intron_density/
|   |-- intron_density.md
|   `-- data/
|       |-- all_human_introns.zip
|-- bootstrapping_pollution/
    |-- bootstrapping_pollution.md
    `-- example_overlap_table_forPlotting.txt

```

## Reproducing the Analyses

### chromoMap Visualization
See [chromoMap.md](chromoMaps/chromoMap.md) for instructions on creating a chromoMap to visualize rare intron classes.

### Inter-Intron Distance Calculation
See [inter_intron_distance.md](inter_intron_distance/inter_intron_distance.md) for instructions on calculating distance between two neighboring genomic points on the same chromosome. In the above study we calculate inter-intron distance i.e. the distance between two consecutive introns of the same class.

### Intron Density Analysis
See [intron_density.md](intron_density/intron_density.md) for instructions on binning the human genome into equally sized bins. Download and unzip all_human_introns.zip to access all_human_introns.csv

### Bootstrapping and Pollution Analysis
See [bootstrapping_pollution.md](bootstrapping_pollution/bootstrapping_pollution.md) for instructions for performing bootstrapping and pollution analysis as described in Springer-Fleck et al.


## Citation

If you use these scripts, please cite:

**Springer S.M., Fleck K., Girardini K.N., Riccard S.M., Erceg J., Kanadia R.N.**  
*Disentangling spatial organization and splicing of rare intron classes in the human genome.*  
Manuscript under review.

---

**Repository:**  
Springer S.M., Fleck K., Girardini K.N., Riccard S.M., Erceg J., Kanadia R.N.  
*Analysis of rare intron organization and splicing in the 3D genome.*  
GitHub: [https://github.com/saren-springer/introns-3Dgenome](https://github.com/saren-springer/introns-3Dgenome)

<br><br>
