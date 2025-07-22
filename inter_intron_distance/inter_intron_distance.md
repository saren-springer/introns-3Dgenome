
# Inter-Intron Distance Calculation
This document provides instructions for calculating the distance between consecutive introns in BED files using `Python`.

## Objective
Compute the distance between two neighboring genomic introns on the same chromosome. Distances are calculated using BED files for different intron classes and summarized into a combined CSV file.


## Prior Use
The following code was previously leveraged in Springer-Fleck et al. to calculate the inter-intron distance for rare intron classes. The resulting distributions can be found in Figure. 1C. The relationship between inter-intron distance and intron count can be found in Figure. 1D. 


## Input Files
- BED files with feature annotations located in: `data/`  
  Each file should follow the pattern:  
  `Homo_sapiens.GRCh38.Ens99.*_introns.bed`

## Dependencies
- Python version 3.7 or greater
- Python packages:
  - `pandas`
  - `biopython` (if needed for additional operations)

## Installing Packages (if needed)
```bash
pip install pandas biopython

```

## Running the Code
```python
import glob
import os
import pandas as pd

#define input
bedfiles_path = "data"
bedfile_pattern = f"{bedfiles_path}/Homo_sapiens.GRCh38.Ens99.*_introns.bed"

#collect BED files
bedfile_list = glob.glob(bedfile_pattern)
print("Found BED files:", bedfile_list)

df_list = []

for bedfile in bedfile_list:
    try:
        df = pd.read_csv(bedfile, sep="\t", header=None, 
                         names=["Chromosome", "Start", "End", "Gene_Transcript", "Score", "Strand"])
        
        #extract intron class from filename
        intron_class = os.path.basename(bedfile).split(".")[-2].replace("_introns", "")
        if intron_class in ["minor_hybrid", "major_hybrid"]:
            intron_class = "hybrid"
        
        df["Intron_Class"] = intron_class
        df = df.sort_values(by=["Chromosome", "Start"])
        
        #calculate inter-intron distances
        df["Prev_End"] = df.groupby("Chromosome")["End"].shift(1)
        df["Distance"] = df["Start"] - df["Prev_End"]
        
        #remove overlaps and NaNs
        df = df[df["Distance"] >= 0].drop(columns=["Prev_End"])
        
        df_list.append(df)
    except Exception as e:
        print(f"Error loading {bedfile}: {e}")

#combine and save output
if df_list:
    final_df = pd.concat(df_list, ignore_index=True)
    output_csv = "intron_distances.csv"
    final_df.dropna().to_csv(output_csv, index=False)
    print(f"Results saved to {output_csv}")
else:
    print("No valid files were loaded")

```

## Output
The script saves:

- `intron_distances.csv` containing:

  - Chromosome
  - Start
  - End
  - Gene_Transcript
  - Strand
  - Intron_Class
  - Distance

<br><br>
