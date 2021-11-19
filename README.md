# all2vcf

Convert common variant formats to VCF format

```

[manipulation]
filter_vcf       Filter variants in a VCF file using common metrics
frequency        Add allele frequency (AF) to INFO and FORMAT fields

[conversion]
isec            Convert the "sites.txt" output file of bcftools isec to VCF
mummer          Convert the result of "show-snps -T" to a psuedo-VCF file

[statistics]
density         Count variants and variants per kbp from a VCF file
stats           Count occurrences of variants inside the provided VCF files

```

## Cite

Unfortunately, we haven't published it yet. But one day, maybe, we will. For now, it would be a grand gesture if you could cite this github repository. Support open science!

## Install

Here's how to setup **all2vcf**. First make sure that the following **python3** packages are installed:

| Program     | Version | Type          | Link                                |
|-------------|---------|---------------|-------------------------------------|
| Python      | 3.*     | Interpreter   | https://www.python.org/downloads/   |
| Pandas      | 1.*     | Module        | https://pandas.pydata.org/          |
| Biopython   | 1.78    | Module        | https://biopython.org/wiki/Download |

Other modules used are `operator`, `sys`, `os`, `time`, `argparse`, `random`, `string`. These should be all built-in within your python distribution.

Then, clone this repository with:

```
git clone https://github.com/MatteoSchiavinato/all2vcf.git
```

### If you need a specific python3 distribution to be used...

Once you've cloned it, you can navigate to the main directory of it and open the `all2vcf` executable with a text editor. Substitute the shebang sequence (currently `#!/usr/bin/env python3.8`) with your own python3 version (e.g. `#!/usr/bin/python3`).

Then, substitute the interpreter at line 7 (currently `interpreter="python3.8"`) with your preferred python3 interpreter (e.g. `interpreter=python3.6.2`). This is what the program will try to call from your `$PATH`. If this interpreter is not in the `$PATH`, use the full path of the interpreter instead (e.g. `interpreter=/usr/bin/python3.6.2`).

### Usage

#### all2vcf filter_vcf

Filter variants in VCF format according to common metrics.

```
--------------------------------------------------------------------------------
all2vcf filter_vcf
--------------------------------------------------------------------------------

USAGE:  all2vcf filter_vcf [ options ]

Filter variants contained in a VCF file based on common metrics

OPTIONS:
--input-file                VCF Format (not BCF)                                [stdin]
--GATK                      Input file is from GATK                             [off]
--output-file               Output filtered VCF file                            [stdout]
--quality                   Minimum call quality (VCF field 6)                  [off]
--qual-by-depth             Use the GATK QD field                               [off]
                            (QUAL normalized by depth of coverage)
--avg-map-qual              Minimum average mapping quality                     [off]
--alt-frac                  Min fraction of reads confirming alternative        [off]
                            allele (range: 0.00-1.00)
--min-depth                 Minimum coverage of the SNP (based on DP4)          [off]
--max-depth                 ... and maximum                                     [off]
--both-strands              Both strands have to fulfill --min-depth,           [off]
                            --max-depth, and --alt-frac criteria
--min-read-per-strand       Both strands need to have at least <N>              [off]
                            reads mapped
--fisher-score              Maximum accepted phred-score from                   [off]
                            Fisher's exact test
--strand-bias-p-value       Maximum accepted strand bias p-value                [off]
                            (phred-scaled!)
--var-dist-bias             Minimum accepted VDB score                          [off]
--read-pos-bias             Minimum accepted RPB score                          [off]
--map-qual-zero-frac        Max % of reads with mapping quality 0               [off]
                            (0%-100%)
--map-qual-vs-strand-bias   Min value for Mann-Whitney U test of                [off]
                            MQ and SB
--report                    Report file to be generated, containing the         [stderr]
                            number of variants that were excluded, subdivided
                            by cause for exclusion
--threads                   Number of threads                                   [1]
```

VCF format documentation: [see here](https://samtools.github.io/hts-specs/VCFv4.2.pdf)
GATK field documentation: [see here](https://gatk.broadinstitute.org/hc/en-us/articles/360035531692-VCF-Variant-Call-Format)

Each of these parameters works only if the corresponding field is found in the `INFO` field of the vcf file.

#### all2vcf frequency

Add the allele frequency specification inside the INFO and the FORMAT fields of the VCF file. Requires the presence of the AD field, which provides read counts per allele.

```
--------------------------------------------------------------------------------
all2vcf frequency
--------------------------------------------------------------------------------

USAGE:  all2vcf frequency [ options ]

Calculate allele frequencies for each variant using the AD field
Requires "AD" annotated in the "INFO" field

OPTIONS:
--input-file                VCF Format (not BCF)                                [stdin]
--output-file               Output VCF file with AF annotation in INFO          [stdout]
```

#### all2vcf isec

Convert the output of [bcftools isec](http://samtools.github.io/bcftools/bcftools.html) to VCF.

```
all2vcf isec [OPTIONS]

--sites             Input "sites.txt" file from bcftools isec               [mandatory]
--vcf               Input VCF files used in isec, IN THE SAME ORDER         [mandatory]
--vcf-names         Names of the input VCF files, IN THE SAME ORDER         [mandatory]
--reference         Reference FASTA file                                    [mandatory]
--basename          Prefix for output file(s)                               ["all2vcf_isec"]
--output-dir        Output directory (if not existent, will be created)     [current]

```

The output of this command is composed of multiple files:

- `*.sites.vcf`: the conversion to VCF of the `sites.txt` file produced by `bcftools isec`
- `*.from_<input_vcf_file>.vcf`: multiple files, each carrying the name of their corresponding input VCF file. These files contain the same variants found in `sites.txt` but taken from the original VCF file. You can check the sample-specific values in there, e.g. those contained in the `INFO` field.

#### all2vcf mummer

Convert the output of the **show-snps** tool from the [MUMmer](http://mummer.sourceforge.net/) toolkit to VCF. Note: **show-snps** must be used as `show-snps -T`.

```
all2vcf mummer [OPTIONS]

--snps              Mummer "snps" file from 'show-snps -T'                  [mandatory]
--reference         Reference genome FASTA file                             [mandatory]
--type              Restrict the output to SNP|INDEL|ALL                    [ALL]
--head-in           Use this flag if the file has a header                  [off]
--head-out          Add a newly-generated VCF header to the output          [off]
--no-Ns             Exclude variants featuring Ns                           [off]

```

The output of this command is a VCF-formatted file containing the same information as in the input snps file.

#### all2vcf density

Count variants and variants per kbp from a VCF file.

```
--vcf               Input VCF file to compute density on                            [mandatory]
--window-size       Size in bp for the window used for density calculation          [10000]
--step-size         Step in bp between calculations                                 [5000]
--regions           BED file with regions to include in the analysis                [off]
--output-file       Path and name of the output file containing variant densities   [mandatory]

```

The output of this command is a single file containing 5 columns: `CHROM, W_START, W_END, NUM_VARS, VARS_KBP`. The `CHROM` indicates the chromosome to which the row corresponds. The `W_START` and `W_END` columns indicate the start and the end position of the window that each row represents. The positions are indicated in 1-based, closed intervals. The `NUM_VARS` column indicates how many variants have been found in each interval, while the `VARS_KBP` column indicates the density / kbp of these variants.

Warning: depending on the chromosome naming, the output may not be sorted as you want it.

#### all2vcf stats

Obtain statistics on the input VCF files. Statistics include (for the moment) only a counting of the occurrences of each type of variant (e.g. SNP, INDEL, BND, INV, ...).

```
all2vcf stats [OPTIONS]

--vcf               Input VCF files to compute statistics on                [mandatory]
--exclude-chrom     SPACE-separated list of seq to be excluded              [off]
--basename          Prefix for output file(s)                               [all2vcf.stats]
--output-dir        Output directory (if not existent, will be created)     [current]


```

The output of this command is composed of multiple files:

- `*.summary.tsv`: table summarizing the different types of variants found in the file and their number of occurrences. This is the main output of the utility.
- `*.<input_vcf_file>.info.tsv`: quick information about the variants found in each of the input VCF file, represented in terms of sequence, position, variant type, reference allele, and alternative allele. Useful for downstream calculations.
- `*.excluded.tsv`: pseudo-VCF file containing the variants that were found in the sequences passed with `--exclude-chrom`.
