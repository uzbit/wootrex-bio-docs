# NGS Diversity Analysis - Website Tutorial

This tutorial will guide you through using the NGS Diversity Analysis platform to analyze genetic diversity in your sequencing data. By the end of this guide, you'll know how to upload files, run analyses, and interpret your results.

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Working with Projects](#working-with-projects)
4. [Uploading and Managing Files](#uploading-and-managing-files)
5. [File Types Reference](#file-types-reference)
6. [Running Analyses](#running-analyses)
   - [Demultiplexing](#demultiplexing)
   - [Clustering](#clustering)
   - [Diversity Analysis](#diversity-analysis)
7. [Monitoring Jobs and Viewing Results](#monitoring-jobs-and-viewing-results)
8. [Complete Workflow Example](#complete-workflow-example)
9. [Tips and Best Practices](#tips-and-best-practices)

---

## Introduction

The NGS Diversity Analysis platform provides a complete pipeline for analyzing genetic diversity in next-generation sequencing (NGS) data. The platform supports three main analysis types:

- **Demultiplexing**: Assign sequencing reads to their corresponding sample barcodes
- **Clustering**: Group similar sequences together based on sequence identity
- **Diversity Analysis**: Calculate diversity metrics (richness, Shannon index, Simpson index) for each sample

The platform is organized around **Projects**, which contain your uploaded **Files** and analysis **Jobs**.

### Pipeline Overview

```
FASTQ Reads ──────┐
                  ├──► Demultiplexing ──► Demux Results ─────┐
Barcode FASTA ────┘                                          │
                                                             ├──► Diversity Analysis
Reference FASTA ──────► Clustering ──────► Cluster DB ───────┘
```

---

## Getting Started

### Logging In

1. Navigate to the platform website
2. Click "Log In" to access your account
3. After logging in, you'll see the Projects overview page

### Understanding the Interface

The platform follows a simple hierarchy:

```
Projects
  └── Files (your uploaded data)
  └── Jobs (your analyses)
        └── Results (downloadable outputs)
```

The main navigation allows you to:
- View all your projects
- Access individual project details
- Monitor running jobs
- Download results

---

## Working with Projects

Projects help you organize related analyses together. For example, you might create separate projects for different experiments or sample sets.

### Creating a New Project

1. From the Projects page, click "New Project"
2. Enter a **Project Name** (required)
3. Optionally add a **Description**
4. Click "Create"

Your new project will appear in the project list.

### Managing Projects

Within each project, you can:

- **Rename**: Click on the project settings to change the project name
- **Delete**: Remove the entire project (requires typing "CONFIRM" for safety)

> **Warning**: Deleting a project will permanently remove all associated files, jobs, and results.

---

## Uploading and Managing Files

Before running any analysis, you need to upload your input files.

### Uploading Files

1. Navigate to your project
2. Click the "Files" tab
3. Click "Upload File"
4. Either drag and drop your file or click to browse
5. Select the appropriate **File Type** from the dropdown
6. Optionally add a **Description**
7. Click "Upload"

A progress bar will show the upload status. Large files may take several minutes.

### Managing Uploaded Files

In the Files tab, you can:

- **View** file details (name, type, size, upload date)
- **Download** files for local use
- **Delete** files you no longer need

> **Note**: Files associated with active or completed jobs cannot be deleted until those jobs are removed.

---

## File Types Reference

This section provides detailed information about each file type supported by the platform, including format requirements and where they are used in the analysis pipeline.

### Quick Reference

| Category | File Types | Compression | Used In |
|----------|-----------|-------------|---------|
| FASTQ Reads | Nanopore, Illumina, PacBio | Required (.gz or .zip) | Demultiplexing |
| FASTA Sequences | Barcodes, Target, Exclude, Adapter, Other | Optional | Demultiplexing, Clustering |
| Result Files | Demux Results, Cluster DB, Diversity Results | ZIP format | Diversity Analysis |

---

### FASTQ Read Files

FASTQ files contain your raw sequencing reads. These files **must be compressed** before uploading.

#### Supported Types

| File Type | Description | Platform |
|-----------|-------------|----------|
| FASTQ Reads (Nanopore) | Long-read sequencing data | Oxford Nanopore |
| FASTQ Reads (Illumina) | Short-read sequencing data | Illumina |
| FASTQ Reads (PacBio) | Long-read sequencing data | Pacific Biosciences |

#### File Requirements

- **Accepted Extensions**: `.fastq.gz`, `.fq.gz`, `.fastq.zip`, `.fq.zip`
- **Compression**: **REQUIRED** - Files must be gzip or zip compressed
- **Format**: Standard FASTQ format

#### FASTQ Format Example

Each read consists of 4 lines:

```
@Read_001
ACGTACGTACGTACGTACGTACGT
+
IIIIIIIIIIIIIIIIIIIIIII
```

- **Line 1**: Header starting with `@` followed by read identifier
- **Line 2**: DNA sequence
- **Line 3**: Plus sign (optionally followed by description)
- **Line 4**: Quality scores (ASCII-encoded, same length as sequence)

#### Preparing FASTQ Files

If you have multiple FASTQ files from the same sample, combine them before uploading. See our [FASTQ Concatenation Guide](fastq-concatenation-guide.md) for detailed instructions.

**Quick commands:**

```bash
# Combine and compress with gzip (Linux/macOS)
cat your_directory/*.fastq | gzip > combined_reads.fastq.gz

# Or use zip
cat your_directory/*.fastq > combined_reads.fastq
zip combined_reads.fastq.zip combined_reads.fastq
```

#### Used In
- **Demultiplexing**: Primary input - reads are assigned to barcodes

---

### FASTA Sequence Files

FASTA files contain sequence data for barcodes, reference sequences, or other purposes. These files can be uploaded compressed or uncompressed.

#### Supported Types

| File Type | Description | Used For |
|-----------|-------------|----------|
| ONT Barcodes | Barcode sequences for sample identification | Demultiplexing (required) |
| Target Sequences | Reference sequences to cluster against | Clustering |
| Exclude Sequences | Sequences to exclude from analysis | Clustering |
| Adapter Sequences | Adapter/primer sequences for filtering | Clustering |
| Other Sequences | General-purpose sequence files | Clustering |

#### File Requirements

- **Accepted Extensions**:
  - Uncompressed: `.fasta`, `.fa`, `.fna`
  - Compressed: `.fasta.gz`, `.fa.gz`, `.fna.gz`, `.fasta.zip`, `.fa.zip`, `.fna.zip`
- **Compression**: **OPTIONAL** - Files can be uploaded with or without compression
- **Format**: Standard FASTA format

#### FASTA Format Example

```
>Barcode_01
ACGTACGTACGT
>Barcode_02
TGCATGCATGCA
>Barcode_03
GCTAGCTAGCTA
```

- **Header Line**: Starts with `>` followed by sequence name/identifier
- **Sequence Lines**: DNA sequence (can span multiple lines)

#### Barcode File Best Practices

When creating barcode files for demultiplexing:

1. **Use unique names**: Each barcode header should have a unique, descriptive name
2. **Keep sequences clean**: Include only the barcode sequence, not adapters
3. **Verify sequences**: Double-check barcode sequences match your wet lab design
4. **One barcode per entry**: Each FASTA entry represents one sample barcode

**Example barcode file:**

```
>Sample_Control
ACACACAC
>Sample_Treatment_1
GTGTGTGT
>Sample_Treatment_2
TCTCTCTC
>Sample_Treatment_3
AGAGAGAG
```

#### Used In
- **Demultiplexing**: ONT Barcodes (required input)
- **Clustering**: Target, Exclude, Adapter, and Other sequences

---

### Result Files (ZIP Archives)

Result files are ZIP archives generated by analysis jobs. They can also be uploaded to reuse results from previous analyses.

#### Supported Types

| File Type | Generated By | Contains | Used As Input For |
|-----------|--------------|----------|-------------------|
| Demultiplex Results | Demultiplexing job | Per-barcode reads, statistics | Diversity Analysis |
| Clustered Database | Clustering job | Cluster definitions, centroids | Diversity Analysis |
| Diversity Analysis Results | Diversity Analysis job | Diversity metrics per sample | - |

#### File Requirements

- **Accepted Extension**: `.zip`
- **Format**: Valid ZIP archive

#### Demultiplex Results Contents

When you run a demultiplexing job, the output ZIP contains:
- Reads separated by barcode
- Summary statistics
- Per-barcode read counts

#### Clustered Database Contents

When you run a clustering job, the output ZIP contains:
- `clusters.json` - Cluster definitions (required)
- `clusters.fasta` - Representative sequences (optional)

The `clusters.json` file has this structure:

```json
{
  "clusters": [
    {
      "id": "cluster_001",
      "centroid": "ACGTACGT...",
      "sequences": [
        {"id": "seq_001"},
        {"id": "seq_002"}
      ]
    }
  ]
}
```

#### Uploading Result Files

You can upload result files from previous analyses to:
- Rerun diversity analysis with different parameters
- Use results from external tools
- Share results between projects

> **Note**: When uploading a Clustered Database ZIP, the system validates the `clusters.json` structure before accepting the file.

#### Used In
- **Diversity Analysis**: Requires both Demux Results and Cluster DB as inputs

---

### File Type Usage Matrix

This table shows which file types are used in each analysis job:

| File Type | Demultiplexing | Clustering | Diversity Analysis |
|-----------|:--------------:|:----------:|:------------------:|
| FASTQ Reads (Nanopore) | INPUT | - | - |
| FASTQ Reads (Illumina) | INPUT | - | - |
| FASTQ Reads (PacBio) | INPUT | - | - |
| ONT Barcodes (FASTA) | INPUT (required) | - | - |
| Target Sequences (FASTA) | - | INPUT | - |
| Exclude Sequences (FASTA) | - | INPUT | - |
| Adapter Sequences (FASTA) | - | INPUT | - |
| Other Sequences (FASTA) | - | INPUT | - |
| Demultiplex Results (ZIP) | OUTPUT | - | INPUT (required) |
| Clustered Database (ZIP) | - | OUTPUT | INPUT (required) |
| Diversity Results (ZIP) | - | - | OUTPUT |

---

## Running Analyses

From the "Jobs" tab in your project, click "Create Job" to start a new analysis.

### Demultiplexing

**Purpose**: Assign sequencing reads to their corresponding sample barcodes.

This is typically the first step when processing multiplexed sequencing data.

#### Required Inputs

| Input | File Type | Description |
|-------|-----------|-------------|
| Input FASTQ File | FASTQ Reads (Nanopore/Illumina/PacBio) | Your compressed sequencing reads |
| Barcode FASTA File | ONT Barcodes (FASTA) | FASTA file containing your sample barcodes |

#### Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Minimum Quality Score | 0-50 | 18.0 | Minimum average quality score for a read to be processed |
| Maximum Errors | 0-3 | 0 | Maximum number of errors allowed when matching barcodes |
| Check Reverse Complement | On/Off | On | Also check reverse complement of barcodes |

#### Outputs

- **Total Reads**: Number of reads processed
- **Assigned Reads**: Number of reads matched to a barcode
- **Assignment Rate**: Percentage of reads successfully assigned
- **Per-barcode counts**: Breakdown of reads per sample
- **Downloadable files**: Summary file and demux archive (ZIP)

---

### Clustering

**Purpose**: Group similar sequences together based on sequence identity.

Use this to cluster your reference sequences or to reduce redundancy in sequence databases.

#### Required Inputs

| Input | File Type | Description |
|-------|-----------|-------------|
| Input Reference FASTA | Target/Exclude/Adapter/Other Sequences | FASTA file containing sequences to cluster |

#### Basic Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Identity Threshold | 0.5-1.0 | 0.97 | Minimum sequence identity to cluster sequences together |

#### Advanced Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| K-mer Size | 5-31 | 15 | K-mer size for minimizer sketching |
| Window Size | 1-50 | 10 | Window size for minimizer selection |
| Min Shared Minimizers | 1-20 | 5 | Minimum shared minimizers for clustering |
| Output FASTA | On/Off | Off | Generate FASTA file with cluster representatives |

#### Outputs

- **Number of Clusters**: Total clusters created
- **Total Sequences**: Input sequence count
- **Unique Sequences**: Sequences after clustering
- **Compression Ratio**: Reduction achieved by clustering
- **Downloadable files**: Cluster archive (ZIP)

---

### Diversity Analysis

**Purpose**: Calculate genetic diversity metrics for each demultiplexed sample.

This analysis combines your clustering results with demultiplexing results to measure diversity.

#### Required Inputs

| Input | File Type | Description |
|-------|-----------|-------------|
| Cluster Result | Clustered Database (ZIP) | Output from a previous Clustering job |
| Demux Result | Demultiplex Results (ZIP) | Output from a previous Demultiplexing job |

#### Basic Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Identity Threshold | 0.5-1.0 | 0.95 | Identity threshold for mapping reads to clusters |
| Min Shared Minimizers | 1-20 | 5 | Minimum shared minimizers for mapping |
| Max Candidates | 1-100 | 10 | Maximum candidate clusters to consider per read |
| Max Reads | 0+ | 0 (unlimited) | Limit number of reads to process (0 = all) |

#### Advanced Feature: Read Pre-clustering

Enable "Pre-cluster reads before mapping" to group similar reads together before analysis. This can:
- Reduce computational workload
- Improve accuracy for high-error-rate sequencing (e.g., nanopore)

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Read Cluster Identity Threshold | 0.85-1.0 | 0.95 | Similarity threshold for grouping reads |

#### Outputs

For each barcode/sample:

| Metric | Description |
|--------|-------------|
| Total Reads | Number of reads for this sample |
| Read Clusters | Number of read clusters (if pre-clustering enabled) |
| Duplicates Removed | Reads removed by pre-clustering |
| Mapped Reads | Reads successfully mapped to reference clusters |
| Mapping Rate | Percentage of reads mapped |
| Richness | Number of unique sequences detected |
| Shannon Index | Diversity measure (higher = more diverse) |
| Simpson Index | Probability two random reads are different |
| Evenness | How evenly reads are distributed |

---

## Monitoring Jobs and Viewing Results

### Job Status

Jobs progress through these states:

| Status | Description |
|--------|-------------|
| Queued | Job is waiting to start |
| Running | Job is actively processing |
| Completed | Job finished successfully |
| Failed | Job encountered an error |
| Cancelled | Job was manually stopped |

### Tracking Progress

While a job is running:
- A progress bar shows completion percentage
- Status messages describe current operations
- Duration is displayed in hours, minutes, seconds

The page automatically updates every few seconds.

### Viewing Results

Once a job completes:

1. Navigate to the job details page
2. View the results summary with key metrics
3. Download output files using the download buttons

### Job Actions

- **Cancel**: Stop a running or queued job
- **Delete**: Remove a job and its results

---

## Complete Workflow Example

Here's a typical end-to-end analysis workflow:

### Step 1: Create a Project

1. Click "New Project"
2. Name it "My Diversity Analysis"
3. Click "Create"

### Step 2: Upload Your Files

1. Go to the Files tab
2. Upload your compressed FASTQ reads (e.g., `reads.fastq.gz`) - select "FASTQ Reads (Nanopore)"
3. Upload your barcode FASTA file (e.g., `barcodes.fasta`) - select "ONT Barcodes"
4. Upload your reference sequences (e.g., `reference.fasta`) - select "Target Sequences"

### Step 3: Run Demultiplexing

1. Go to Jobs tab, click "Create Job"
2. Select "Demultiplexing"
3. Choose your FASTQ file and barcode file
4. Use default parameters or adjust as needed
5. Click "Create Job"
6. Wait for completion

### Step 4: Run Clustering

1. Create another job
2. Select "Clustering"
3. Choose your reference FASTA file
4. Set identity threshold (e.g., 0.97 for species-level)
5. Click "Create Job"
6. Wait for completion

### Step 5: Run Diversity Analysis

1. Create another job
2. Select "Diversity Analysis"
3. Select your completed Cluster Result
4. Select your completed Demux Result
5. Adjust parameters if needed
6. Click "Create Job"
7. Wait for completion

### Step 6: Download Results

1. View your diversity analysis results
2. Download the summary file and analysis archive
3. Examine diversity metrics for each sample

---

## Tips and Best Practices

### File Preparation

- **Combine FASTQ files** from the same sample before uploading (see [FASTQ Concatenation Guide](fastq-concatenation-guide.md))
- **Compress large files** using gzip for faster uploads
- **Use descriptive names** for your files to easily identify them later
- **Verify barcode sequences** match your experimental design

### Parameter Tuning

**For Demultiplexing:**
- Increase Max Errors (1-2) if your barcode assignment rate is low
- Adjust Minimum Quality Score based on your sequencing platform

**For Clustering:**
- Use 0.97 identity for species-level grouping
- Use 0.99+ for strain-level resolution
- Lower thresholds create fewer, larger clusters

**For Diversity Analysis:**
- Enable read pre-clustering for nanopore data (high error rate)
- Use Max Reads to test parameters on a subset before full analysis

### Troubleshooting

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Low barcode assignment rate | Barcodes not matching | Check barcode sequences, try reverse complement, increase max errors |
| Job fails immediately | Invalid file format | Verify file is correctly formatted FASTQ/FASTA |
| Upload fails | File not compressed | Ensure FASTQ files are .gz or .zip compressed |
| Very slow processing | Large file size | Use Max Reads to limit processing, or wait for completion |
| No diversity results | No reads mapped | Check identity threshold, ensure reference matches your samples |
| Cluster upload rejected | Invalid clusters.json | Verify ZIP contains properly formatted clusters.json |

### Best Practices

1. **Start with default parameters** and adjust based on results
2. **Run a test analysis** on a small subset before processing full datasets
3. **Keep your projects organized** with clear naming conventions
4. **Download results promptly** as file URLs may expire
5. **Delete old jobs and files** to keep your project clean

---

## Need Help?

If you encounter issues not covered in this tutorial:

1. Check that your input files are correctly formatted
2. Review the job error messages for specific issues
3. Try running with default parameters first
4. Contact support with your job ID for assistance
