# How to Concatenate FASTQ Files

This guide explains how to combine multiple FASTQ files from a directory (including all subdirectories) into a single compressed file.

## Linux / macOS

### For uncompressed FASTQ files (.fastq or .fq)

```bash
find /path/to/directory -name "*.fastq" -o -name "*.fq" | xargs cat | gzip > combined_reads.fastq.gz
```

Or using a subshell for better handling of special characters:

```bash
find /path/to/directory \( -name "*.fastq" -o -name "*.fq" \) -exec cat {} + | gzip > combined_reads.fastq.gz
```

### For already compressed FASTQ files (.fastq.gz or .fq.gz)

```bash
find /path/to/directory \( -name "*.fastq.gz" -o -name "*.fq.gz" \) -exec cat {} + > combined_reads.fastq.gz
```

### For a mix of compressed and uncompressed files

```bash
# First combine all uncompressed, then append decompressed .gz files
(find /path/to/directory \( -name "*.fastq" -o -name "*.fq" \) -exec cat {} + ; \
 find /path/to/directory \( -name "*.fastq.gz" -o -name "*.fq.gz" \) -exec zcat {} +) | gzip > combined_reads.fastq.gz
```

**Note for macOS:** Use `gzcat` instead of `zcat`:

```bash
(find /path/to/directory \( -name "*.fastq" -o -name "*.fq" \) -exec cat {} + ; \
 find /path/to/directory \( -name "*.fastq.gz" -o -name "*.fq.gz" \) -exec gzcat {} +) | gzip > combined_reads.fastq.gz
```

## Windows

### Using PowerShell

#### For uncompressed FASTQ files

```powershell
Get-ChildItem -Path "C:\path\to\directory" -Recurse -Include "*.fastq","*.fq" |
    Get-Content |
    Out-File -FilePath "combined_reads.fastq" -Encoding ascii

# Then compress with gzip (requires gzip to be installed, e.g., via WSL or Git Bash)
gzip combined_reads.fastq
```

#### Using PowerShell with .NET compression

```powershell
$files = Get-ChildItem -Path "C:\path\to\directory" -Recurse -Include "*.fastq","*.fq"
$outputPath = "C:\path\to\output\combined_reads.fastq.gz"

$outputStream = [System.IO.File]::Create($outputPath)
$gzipStream = New-Object System.IO.Compression.GzipStream($outputStream, [System.IO.Compression.CompressionMode]::Compress)
$writer = New-Object System.IO.StreamWriter($gzipStream)

foreach ($file in $files) {
    $content = Get-Content $file.FullName -Raw
    $writer.Write($content)
}

$writer.Close()
$gzipStream.Close()
$outputStream.Close()
```

### Using Windows Subsystem for Linux (WSL) - Recommended

```bash
# Open WSL terminal and use the Linux commands above
find /mnt/c/path/to/directory -name "*.fastq" -o -name "*.fq" | xargs cat | gzip > combined_reads.fastq.gz
```

### Using Git Bash

```bash
find /c/path/to/directory -name "*.fastq" -o -name "*.fq" | xargs cat | gzip > combined_reads.fastq.gz
```

## Quick Reference

| OS | Uncompressed FASTQ | Already Compressed (.gz) |
|----|-------------------|-------------------------|
| Linux | `find . \( -name "*.fastq" -o -name "*.fq" \) -exec cat {} + \| gzip > out.fastq.gz` | `find . \( -name "*.fastq.gz" -o -name "*.fq.gz" \) -exec cat {} + > out.fastq.gz` |
| macOS | Same as Linux | Same as Linux |
| Windows (WSL) | Same as Linux | Same as Linux |
| Windows (PowerShell) | See PowerShell examples above | Requires additional tooling |

## Tips

1. **Check file count first**: Before combining, verify you're capturing all files:
   ```bash
   find /path/to/directory \( -name "*.fastq" -o -name "*.fq" \) | wc -l
   ```

2. **Estimate output size**: FASTQ files compress to roughly 20-30% of original size:
   ```bash
   find /path/to/directory \( -name "*.fastq" -o -name "*.fq" \) -exec du -ch {} + | tail -1
   ```

3. **Use pigz for faster compression**: If you have many CPU cores:
   ```bash
   find . \( -name "*.fastq" -o -name "*.fq" \) -exec cat {} + | pigz -p 8 > combined_reads.fastq.gz
   ```

4. **Preserve file order**: If order matters, sort the file list:
   ```bash
   find . \( -name "*.fastq" -o -name "*.fq" \) | sort | xargs cat | gzip > combined_reads.fastq.gz
   ```
