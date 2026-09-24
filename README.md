# FSuite

FSuite is an integrative solution to exploit inbreeding in dense SNP chip and exome data. Its goals are for

1 Population genetic studies:
- estimating and detecting inbreeding on individuals without known genealogy,
- estimating the individual probability to be into offspring of different mating types,
- estimating the proportion of mating types in the population,
  
2 Rare disease studies: performing homozygosity mapping with heterogeneity,

3 Multifactorial disease studies: performing HBD-GWAS strategy.

## Downloading FSuite
FSuite version 1.0.4 is freely available under GNU GPL license.

FSuite needs following programs to be installed:
- [PLINK2](https://www.cog-genomics.org/plink2/) version from BGI,
- [Merlin](https://csg.sph.umich.edu/abecasis/Merlin/) (optional),
- R with [quantsmooth](https://www.bioconductor.org/packages/2.11/bioc/html/quantsmooth.html) and [zoo](https://cran.r-project.org/package=zoo) packages and [Circos](https://circos.ca/) (optional).

## Running FSuite
FSuite is a user friendly pipeline consisting in a main perl function, fsuite.pl, that implements the creation of several random sparse submaps on genome-wide data (to remove linkage disequilibrium), in order to run FEstim program.
It needs input files in PLINK format.

<img width="729" height="843" alt="fsuite_flowchart" src="https://github.com/user-attachments/assets/b3785ad7-2fff-47a6-ada5-2a0c8fefd27e" />

As described in this flowchart, FSuite is based on 6 functions corresponding to the following steps:
- Step 1: estimation of allele frequencies (optional if allele frequencies are already available). This step creates a PLINK “frq” file.
- Step 2: creation of several random submaps. This step creates a « submaps » folder containing one file per submap, and some summary files.
- Step 3: estimating inbreeding coefficient. This step creates different output files summarizing inbreeding information.
- Step 4: calculation of FLOD scores for each inbred individual. This step creates a FLOD folder containing files with HBD posterior probabilities, HBD segments, and FLOD scores.
- Step 5: calculation of HFLOD scores for the sample of inbred individuals. This step creates an HFLOD folder containing a file with HFLOD scores, and some graphical outputs.
- Step 6: generation of plots with HBD segments. This step creates different types of graphical outputs from HBD segments.

## Citing Fsuite

If you use FSuite in a published analysis, please cite FSuite paper and report the FSuite version used.

Gazal S, Sahbatou M, Genin E, Leutenegger AL. 2014. FSuite: exploiting inbreeding in dense SNP chip and exome data. Bioinformatics 30:1940-1.

In addition, please cite the appropriate publication or publications listed below.
- When using multiple submaps: Leutenegger AL, Sahbatou M, Gazal S, Cann H, Genin E. 2011. Consanguinity around the world: what do the genomic data of the HGDP-CEPH diversity panel tell us? Eur J Hum Genet 19: 583-587.
- When estimating inbreeding coefficient: Leutenegger AL, Prum B, Genin E, Verny C, Lemainque A, Clerget-Darpoux F, Thompson EA. 2003. Estimation of the inbreeding coefficient through use of genomic data. Am J Hum Genet 73: 516-523.
- When calculating FLOD scores: Leutenegger AL, Labalme A, Genin E, Toutain A, Steichen E, Clerget-Darpoux F, Edery P. 2006. Using genomic inbreeding coefficient estimates for homozygosity mapping of rare recessive traits: application to Taybi-Linder syndrome. Am J Hum Genet 79: 62-66.
- When performing HBD-GWAS strategy: Genin E, Sahbatou M, Gazal S, Babron MC, Perdry H, Leutenegger AL. 2012. Could Inbred Cases Identified in GWAS Data Succeed in Detecting Rare Recessive Variants Where Affected Sib-Pairs Have Failed? Hum Hered 74: 142-152.

## FAQ
1. I have the following error message when using step 3 of FSuite pipeline:

        Error in Conditional_proba: qstar[…][1]=0, division by zero Problem in Conditional_proba in main.

What should I do?
This error message mainly appears when the map/bim file does not have genetic positions (3rd column always equals to 0).
The best way to add the genetic position in your PLINK files is to use –cm-map option of plink2. For human, we recommend using SHAPEIT genetic map.
When genetic map is added to your map/bim file, you need to re-run –create-submaps to generate accurate submaps.
Note that an error message should appears from version 1.0.4 when creating submaps with no genetic positions in the map/bim file.

2. Does FSuite read vcf file?
FSuite does not read vcf file, as this format does not integrate information on the family structure and the genetic positions. Some formatting is thus necessary to convert your vcf file into PLINK files. All these steps can be handle with plink2.

- Step 1: Converting your vcf file into PLINK files

        plink2 --vcf myfile.vcf --make-bed --out myfile_temp

        Note that we recommend using FSuite only on common polymorphisms (MAF >=5%) and that an additional filter can be used at this step with PLINK –maf option (if you have a large dataset to estimate allele frequencies) or with PLINK –extract option (if you have a list of common SNPs with corresponding allele frequencies estimated in a reference sample).

- Step 2 (if necessary): Update your fam file if you have families and you need to run FSuite step 4 with –familywise option

- Step 3: Add genetic distances to the bim file (see FAQ 1 for more details)

        plink2 --bfile myfile_temp --cm-map /path/genetic_map_chr@_combined_b37.txt --make-bed --out myfile_for_FSuite

- Step 4: Clean temporary files

        rm myfile_temp.*

3. How can I analyze my whole exome sequencing (WES) data with FSuite?
Analyzing WES data with FSuite requires the same formatting steps as described in FAQ 2, to convert the initial vcf containing all your samples to the final PLINK files.
If you have a small sample size we recommend using our set of common polymorphisms present in WES data (download here), with corresponding PLINK .frq files estimating frequencies in African, European, East-Asian and South-Asian 1000 Genomes phase 3 populations).
   
        plink2 --vcf myfile.vcf --extract /path/WES_common.txt --make-bed --out myfile_temp

