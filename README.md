# CNVkit RNA-seq Analysis Pipeline

## Overview
This pipeline automates copy number variation (CNV) analysis based on RNA sequencing (RNA-seq) data using CNVkit. It processes input sample files, detects CNVs, performs segmentation, and generates visualizations using a Snakemake workflow.

## Dependencies
The following software and Python libraries are required:

- **CNVkit**: Copy number variation analysis tool
- **Snakemake**: Workflow management system
- **Python**: Required for Snakemake and CNVkit execution
- **Pandas**: For sample data processing
- **NumPy**: For numerical operations
- **OS module**: For path handling

## Pipeline Structure
The pipeline consists of the following Snakemake rules:

### 1. `rule all`
Defines the final output, ensuring that `.pdf` files for all samples are generated in the `cnv_output` directory.

### 2. `rule cnvkit_RNAseq`
Processes RNA-seq data using CNVkit.
- **Input**: RNA-seq sample files (`.txt`) located in `cnv_input_dir`
- **Output**:
  - Summary file: `output.txt`
  - Copy number ratio files (`.cnr` files)
- **Command**:
  ```bash
  cnvkit.py import-rna {params.options} -o {output.summary} --output-dir {params.out_dir} {input}
  ```

### 3. `rule cnvkit_segment`
Segments the CNV results.
- **Input**: `.cnr` file
- **Output**: `.cns` segmented CNV file
- **Command**:
  ```bash
  cnvkit.py segment {input} -o {output}
  ```

### 4. `rule cnvkit_plot`
Generates visualizations of CNV analysis results.
- **Input**:
  - `.cns` segmented file
  - `.cnr` CNV ratio file
- **Output**: `.pdf` CNV scatter plots
- **Command**:
  ```bash
  cnvkit.py scatter {input.cnr} -s {input.cns} -o {output}
  ```

## Configuration
The `config.yaml` file should specify input paths, output directories, and CNVkit options. Example:

```yaml
samples: "samples.tsv"
path:
  data: "./data"
  pipeline: "./pipeline"
  input: "./input"
cnvkit:
  options: "--threshold 0.2"
```

## Execution
To run the pipeline, execute:
```bash
snakemake --cores <num_cores>
```
Replace `<num_cores>` with the number of available CPU cores.

## Output Files
- **Processed CNV files** (`.cnr`, `.cns`)
- **Summary Report**: `output.txt`
- **Visualization Files**: `.pdf` scatter plots
- **Log Files**: Stored in `log_dir`

## Troubleshooting
- Ensure that paths and options in `config.yaml` are correctly set.
- Verify that `cnvkit.py` is installed and executable.
- Check `log/cnvkit.log` for errors.


