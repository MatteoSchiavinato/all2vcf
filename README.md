# all2vcf

Convert common variant formats to VCF format

### Install

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

##### If you need a specific python3 distribution to be used...

Once you've cloned it, you can navigate to the main directory of it and open the `all2vcf` executable with a text editor. Substitute the shebang sequence (currently `#!/usr/bin/env python3.8`) with your own python3 version (e.g. `#!/usr/bin/python3`).

Then, substitute the interpreter at line 7 (currently `interpreter="python3.8"`) with your preferred python3 interpreter (e.g. `interpreter=python3.6.2`). This is what the program will try to call from your `$PATH`. If this interpreter is not in the `$PATH`, use the full path of the interpreter instead (e.g. `interpreter=/usr/bin/python3.6.2`).

### Conversion tools

##### all2vcf isec

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


##### all2vcf mummer

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


### Statistic tools

##### all2vcf density

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


##### all2vcf stats

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
