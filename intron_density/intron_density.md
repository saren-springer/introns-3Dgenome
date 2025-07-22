
# Intron Density Analysis
This document provides instructions for calculating intron density across the genome using `R`.

## Objective
Quantify intron density per 250 kb genomic window across chromosomes and by intron class. The analysis computes:

- Counts and density per intron class
- Counts for all class combinations
- Total intron density
- Associated gene symbols per window

## Prior Use
This method was used in Springer-Fleck et al. to compute intron density by class and visualize genome-wide distribution patterns. The resulting density plots are shown in Figure 1G and H as well as Figure S2.

## Input Files
- CSV file of all introns with:

  - `Chromosome`
  - `Start`
  - `Stop`
  - `Class`
  - `Symbol`  
  
  <br>
  Example: `data/all_human_introns.csv`

## Dependencies
- **R version 4.0 or greater**
- R packages:

  - `dbscan`
  - `ggplot2`
  - `dplyr`
  - `writexl`
  - `tidyr`

## Installing Packages (if needed)
```r
install.packages(c("dbscan", "ggplot2", "dplyr", "writexl", "tidyr"))

```

## Running the Code
```r
# Load libraries
library(dbscan)
library(ggplot2)
library(dplyr)
library(writexl)
library(tidyr)

#load data
introns <- read.csv("data/all_human_introns.csv")

#set window size (in bp)
intron_classes <- c("Minor", "Minor-like", "Hybrid", "Major-like", "Major", "Non-canonical")
window_size <- 250000

#generate all combinations
all_combinations <- unlist(lapply(1:length(intron_classes), 
                                  function(x) combn(intron_classes, x, simplify = FALSE)), recursive = FALSE)

#create a function to compute density per window
calculate_windows <- function(introns, window_size, all_combinations) {
  results <- list()
  
  for (chr in unique(introns$Chromosome)) {
    chr_introns <- introns %>% filter(Chromosome == chr)
    max_position <- max(chr_introns$Stop, na.rm = TRUE)
    
    windows <- data.frame(
      Chromosome = chr,
      Window_Start = seq(1, max_position, by = window_size),
      Window_End = seq(window_size, max_position + window_size, by = window_size)
    )
    
    for (class in intron_classes) {
      windows[[paste0(class, "_Count")]] <- sapply(windows$Window_Start, function(start) {
        sum(chr_introns$Start >= start & chr_introns$Start < (start + window_size) & chr_introns$Class == class)
      })
      windows[[paste0(class, "_Density")]] <- windows[[paste0(class, "_Count")]] / window_size
    }
    
    for (combo in all_combinations) {
      combo_name <- paste(combo, collapse = "_")
      windows[[paste0(combo_name, "_Count")]] <- sapply(windows$Window_Start, function(start) {
        sum(chr_introns$Start >= start & chr_introns$Start < (start + window_size) & chr_introns$Class %in% combo)
      })
      windows[[paste0(combo_name, "_Density")]] <- windows[[paste0(combo_name, "_Count")]] / window_size
    }
    
    windows$Total_Intron_Count <- rowSums(windows[grep("_Count$", names(windows))])
    windows$Total_Density <- windows$Total_Intron_Count / window_size
    
    results[[chr]] <- windows
  }
  
  return(bind_rows(results))
}

#calculate density
window_results <- calculate_windows(introns, window_size, all_combinations)

#save results
write_xlsx(window_results, "intron_density_results.xlsx")


```

## Output
The script saves:

- `intron_density_results.xlsx` containing:

  - Chromosome
  - Window Start
  - Window End
  - Intron Count per Class
  - Intron Density per Class
  
<br><br>
