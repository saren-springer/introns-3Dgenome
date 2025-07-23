
# Bootstrapping and Pollution Analysis
This document provides instructions for generating bootstrapping and pollution control datasets for assessing intron distribution in genomic features using `bash`. Briefly, randomly selected major introns were matched to the total number of rare introns per class and sampled 1,000 times. In parallel, 1,000 datasets were generated, consisting of between 10% and 100% rare introns (in increments of 10%), with the remainder of introns consisting of major introns. The BED files produced from this analysis were then intersected with a master table of intron annotations in the 3D genome using bedtools intersect.For each bootstrapping and pollution dataset, we calculated the proportion of introns per class that fell into compartments, subcompartments, and nuclear domains. The resulting distribution of proportions was plotted as a series of boxplots using ggplot2.

## Objective
Create random control datasets for:

- **Bootstrapping:** Random sets of major introns matching the size of a target rare intron class.
- **Pollution Analysis:** Mixed datasets with increasing proportions (10% to 100%) of rare introns and remaining major introns.

These datasets can be intersected with 3D genome annotations to test enrichment patterns.

## Prior Use
This method was used in Springer, Fleck et al. for Figures 2D and Supplemental Figures 4 and 5.

## Input Files
BED files for each intron class:

- `Homo_sapiens.GRCh38.Ens99.major_introns.bed`
- `Homo_sapiens.GRCh38.Ens99.minor_introns.bed`
- `Homo_sapiens.GRCh38.Ens99.major-like_introns.bed`
- `Homo_sapiens.GRCh38.Ens99.hybrid_introns.bed`
- `Homo_sapiens.GRCh38.Ens99.minor-like_introns.bed`
- `Homo_sapiens.GRCh38.Ens99.non-canonical_introns.bed`

## Dependencies
- Linux environment with `bash`
- `shuf` command (GNU coreutils)

## Bootstrapping example
Generate **1000 bootstrap datasets** of major introns matching the size of the rare intron class (e.g., `minor` = 850 introns).
```bash
#!/bin/bash

# Paths
MAJOR_INTRONS="/path/to/Homo_sapiens.GRCh38.Ens99.major_introns.bed"
OUTPUT_DIR="/path/to/bootstrapping/minor"
mkdir -p $OUTPUT_DIR

# Bootstrap: sample 850 major introns 1000 times
for i in {1..1000}; do
    shuf -n 850 $MAJOR_INTRONS > "$OUTPUT_DIR/850_bootstrap_${i}.bed"
done

```

## Pollution example
Generate 1000 mixed datasets containing a fixed total number of introns (e.g., 850) with increasing proportions of the target intron class (e.g., minor), ranging from 10% to 100%, and the remainder filled with major introns.
```bash
#!/bin/bash

# Paths
MAJOR_INTRONS="/path/to/Homo_sapiens.GRCh38.Ens99.major_introns.bed"
MINOR_INTRONS="/path/to/Homo_sapiens.GRCh38.Ens99.minor_introns.bed"
OUTPUT_DIR="/path/to/pollution/minor"
mkdir -p $OUTPUT_DIR

# Pollution: 1000 datasets per percentage
for percent in {10..100..10}; do
    for i in {1..1000}; do
        if [ "$percent" -eq 100 ]; then
            cp "$MINOR_INTRONS" "$OUTPUT_DIR/850_pollution_${percent}_${i}.bed"
        else
            MAJOR_COUNT=$((850 - (percent * 850 / 100)))
            MINOR_COUNT=$((percent * 850 / 100))
            MAJOR_SAMPLE=$(shuf -n $MAJOR_COUNT "$MAJOR_INTRONS")
            MINOR_SAMPLE=$(shuf -n $MINOR_COUNT "$MINOR_INTRONS")
            { echo "$MAJOR_SAMPLE"; echo "$MINOR_SAMPLE"; } | shuf > "$OUTPUT_DIR/850_pollution_${percent}_${i}.bed"
        fi
    done
done

```

## Visualizing results in R
Once bootstrapping and pollution datasets are generated and intersected with annotation files, use the following R script to calculate proportions and create boxplots using `ggplot2`.

where "overlap_table_forPlotting.txt" contains:
  - Chromosome
  - Start Coordinate
  - Stop Coordinate
  - gene name
  - intron ID
  - 3D annotations (listed one per column as a 1-0 entry)

```r

#load libraries
library(dplyr)
library(ggplot2)
library(readr)

#set working directory
setwd("path/to/results")

#load master table of introns with annotations
df <- read_tsv("overlap_table_forPlotting.txt")

#function to process a list of introns and classify into compartments
process_introns <- function(intron_list, df, category, replicate) {
  df_filtered <- df %>% filter(Intron_ID %in% intron_list)
  df_filtered <- df_filtered %>%
    mutate(Compartment = case_when(
      A_comp == 1 ~ "A",
      B_comp == 1 ~ "B",
      TRUE ~ "Neither"
    ))
  
  proportions <- df_filtered %>%
    group_by(Compartment) %>%
    summarise(Count = n(), .groups = "drop") %>%
    mutate(Proportion = Count / sum(Count),
           Category = category,
           Replicate = replicate)
  
  return(proportions)
}

# Combine results from bootstrapping and pollution
all_results <- list()

#example for 1000 bootstrap replicates
for (i in 1:1000) {
  bootstrap_introns <- read_tsv(paste0("bootstrapping/minor/850_bootstrap_", i, ".bed"), col_names = FALSE)$X4
  all_results[[paste0("bootstrap_", i)]] <- process_introns(bootstrap_introns, df, "Bootstrap", i)
}

#example for pollution replicates
for (p in seq(10, 100, by = 10)) {
  for (i in 1:1000) {
    pollution_introns <- read_tsv(paste0("pollution/minor/850_pollution_", p, "_", i, ".bed"), col_names = FALSE)$X4
    all_results[[paste0("pollution_", p, "_", i)]] <- process_introns(pollution_introns, df, paste0("Pollution", p), i)
  }
}

results_df <- bind_rows(all_results, .id = "ID")

#define category order and colors
results_df$Category <- factor(results_df$Category, levels = c("Bootstrap", paste0("Pollution", seq(10, 100, 10))))
compartment_colors <- c("A" = "#AADDF3", "B" = "#F0A4BE", "Neither" = "#D2D3D4")

#plot
ggplot(results_df, aes(x = Category, y = Proportion, fill = Compartment)) +
  geom_boxplot(width = 0.8) +
  scale_fill_manual(values = compartment_colors) +
  labs(title = "Compartment Distribution Across Bootstrapping and Pollution Sets",
       x = "Category", y = "Proportion") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

```

## Output
- Bootstrapping datasets: `bootstrapping/{class}/850_bootstrap_*.bed`
- Pollution datasets: `pollution/{class}/850_pollution_{percent}_{rep}.bed`
- Summary plot: Compartment distribution boxplots

<br><br>
