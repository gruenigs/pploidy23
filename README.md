# Autopolyploid populations

## Provided material

- Download the data and scripts:
https://filesender.switch.ch/filesender2/download.php?token=03f49d77-61a0-4503-96e4-554dda9d8469&files_ids=453221

- For simplicity when running script, keep the same organisation of directories. For this, transfer the zip file into your `data/Day4` directory within your renku session and unzip it as below

```bash
## these commands assume you are in the data/Day4 directory
unzip Day4_material.zip 
mv Day4_material/* . # move directories within Day4_material to your current location
rm -r Day4_material # remove that directory (now empty)
cd ../..
```


## Part 1 - inheritance

**Input files** = `inheritance.tetraL.SNPs.vcf.gz` and `inheritance.tetraH.SNPs.vcf.gz`
These inputs correspond to one vcf file per population and they only include variant sites (i.e. biallelic SNPs).

1. From the renku laucher tab, lauch Rstudio.
2. In Rstudio open the script  `plot_genotypes_freq.R`. (Go to `File` > `Open File...` and browse to the script).
If your datasets are in the `/data/Day4/01.inheritance` directory, then you should not need to change anything to the script. Otherwise, adapt paths to your datasets at the beginning of the script. Select the full script and run it at once. It will take ca. 25 minutes to complete, you can go to the next part in the meantime and check the output later.

The R script reads the vcf, extracts genotypes and allele frequencies from the vcf and then report the corresponding genotype frequencies and allele frequencies into a table. Finally, genotype frequencies are plotted against the alternative allele frequency.

## Part 2 - Linkage disequilibrium

In this part, the linkage disequilibrium in both diploids and tetraploids will be estimated using the R package `ldsep` (https://cran.r-project.org/package=ldsep).

The analysis consists in two parts

1. **LD estimation**: the first script `ldsep.R` is used to estimate r<sup>2</sup> between pairs of SNPs and report these value in a table. This output is comparable to those obtained with PLINK2, which is widely used to estimate LD in diploids. This first step is quite long to run. Thus, we will only run it on one diploid and one tetraploid population on a 1Mbp subset of the dataset. `ldsep` also implements multiple ways of estimating LD and here we will go for the quickest due to time constraints. The input datasets are splitted into populations specific VCF (no possibility to allocate samples to populations within the package). Further, the dataset was reduced by removal of the singletons, which are uninformative in such analyses.

**Input files** = `LD_diL_1M.vcf.gz` and `LD_tetraL_1M.vcf.gz`

- Run the `ldsep_ibu.R` script in Rstudio (open R studio with thesame instructions as points 1. and 2. of the Part 1) once for the diL and once for the tetraL population. Edit accordingly the input file name, output file name, and ploidy. This will run the r<sup>2</sup> estimations and write them into tables.

2. **Visualisation of the LD decay** by plotting the average r<sup>2</sup> in categories of distances (in bp). Scaffold wide LD estimations are provided in the `output_ldsep` directory. Use these as input for the `plot_r2_outputs.R` to visualise LD decay. Again, check input names and paths at the beginning of the R script.

## Part 3 - Genome scans

In this part we will use ScanTools (https://github.com/pmonnahan/ScanTools) to estimate F<sub>ST</sub>, D<sub>XY</sub>, Tajima's D, and pi diversity along genomic windows. These estimations along a whole chromosome would again take hours. Thus, a first step will consist in running ScanTools on a 1Mbp subset of the dataset, while chromosome-wide estimations output are provided in `gs_output` and can be used as input for the second part.

1. Run ScanTools

**Input files** = `03.genomescans/recode_dir/POP.table.recode.txt` these tables were obtained with the `.splitVCF` function in Scantools, which takes 1 vcf file per scaffold / chromosome and produce these populations specific tables.

- Go within the `03.genomescans/ScanTools` directory and edit the script `01.genomicscans.py`. Set paths according to instructions given in the script.

**NB**: you must provide full paths e.g. `/home/rstudio/work/polyploid-summer-school-2023/data/Day4/03.genomescans` and not only `data/Day4/03.genomescans`

- Once properly edited, run the script `01.genomicscans.py`

```bash
cd path/to/ScanTools  # change directory to the Scantools directory

# run the script
python3 01.genomicscans.py > commands.sh
```

- Scantools is designed to work on a cluster and scripts are usually directly submitted with sbatch. However, for this practical the commands were written into the script `commands.sh` which will be run as below:

```bash
chmod +x commands.sh # this makes the script executable
./commands.sh # this runs the script
```

- The results will be written in the directory with the input tables (i.e. the "recode_dir" from the script `01.genomicscans.py`)

Within population diversity statistics are reported in outputs `*.WPM.txt` files and pairwise differentiation statistics are reported in `*.BPM.txt`

2. Plot genome scans

**Input files** = `*WPM/BPM.txt` files found in the `04.genomescans/gs_output` directory. These were obtained by running Scantools as above but on a whole Scaffold.

- Edit the `Plot_genomescans.R` script found in the `04.genomescans` directory to set path to input.

- Run the R script
