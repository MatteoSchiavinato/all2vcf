# all2vcf

Convert common variant formats to VCF format

### isec

```
--sites             Input "sites.txt" file from bcftools isec               [mandatory]
--vcf               Input VCF files used in isec, IN THE SAME ORDER         [mandatory]
--vcf-names         Names of the input VCF files, IN THE SAME ORDER         [mandatory]
--reference         Reference FASTA file                                    [mandatory]
--basename          Prefix for output file(s)                               ["all2vcf_isec"]
--output-dir        Output directory (if not existent, will be created)     [current]

```

<description to be added>

### mummer

```
--snps              Mummer "snps" file from 'show-snps -T'                  [mandatory]
--reference         Reference genome FASTA file                             [mandatory]
--type              Restrict the output to SNP|INDEL|ALL                    [ALL]
--head-in           Use this flag if the file has a header                  [off]
--head-out          Add a newly-generated VCF header to the output          [off]
--no-Ns             Exclude variants featuring Ns                           [off]

```

<description to be added>
