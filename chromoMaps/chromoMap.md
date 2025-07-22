
# chromoMap Visualization
This document provides instructions for generating a chromoMap using the `chromoMap` R package (Anand, 2022). 

Citation: L. Anand, C. M. Rodriguez Lopez, ChromoMap: an R package for interactive visualization of multi-omics data and annotation of chromosomes. BMC Bioinformatics 23, 33 (2022). 

## Objective
Generate a chromoMap to visualize features of interest in each chromosome based on an input chromosomes.bed file and a text file with annotations of interest.

## Prior Use
The following code was previously leveraged in Springer-Fleck et al. to visualize the distribution of rare intron classes per chromosome. The resulting chromoMap can be found in Figure. 1E. 


## Input Files
Input file formats can be found in the `data` folder.

- **Chromosome BED file:** `data/Homo_sapiens.GRCh38.Ens99.chromosomes.bed`
- **Introns annotation file:** `data/Homo_sapiens.GRCh38.Ens99.All_introns.txt`

## Dependencies
- R (version 4.0 or greater)
- [chromoMap](https://cran.r-project.org/web/packages/chromoMap/index.html)

## Installing Packages (if needed)
```r
install.packages("chromoMap")

```

## Running the Code
```r
#load packages
library(chromoMap)

#define input files
chr_file <- "data/Homo_sapiens.GRCh38.Ens99.chromosomes.bed"
features_file <- "data/Homo_sapiens.GRCh38.Ens99.All_introns.txt"

#create chromoMap 
chromoMap(chr_file, features_file, 
          chr.2D.plot = T, 
          n_win.factor = 1,
          win.summary.display = F,
          plot_filter = list(c("col", "byCategory")), 
          ch2D.cat.order = c("minor","minor-like","minor_hybrid", "major_hybrid","major-like","major","non-canonical"),
          ch2D.colors = c("#D22033", "#B559A2", "#654486", "#654486", "#ffffff", "#ffffff","#BDBDC0"))

```
<br><br>

