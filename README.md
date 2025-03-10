# snp_selection
just a repo to track how on earth i am going to even get to the stage of doing MR 

## Selection from DIAMANTE and what we know 
Decided to go for this https://diagram-consortium.org/downloads/DIAMANTE_summary_statistics_download.pdf - i downloaded the ``DIAMANTE-EUR.sumstat.txt`` file
- chose the European data as we had discussed to focus on European first
- the data came in a ``.txt`` file - the data contains summary statistics from a GWAS so not individual level data to use PLINK on

To find the right SNPs for MR, they should follow:
- Genome-wide significance threshold (p-value < 5 × 10⁻⁸).
- independent SNPs (minimal linkage disequilibrium).
- Clearly defined effect sizes and allele frequencies from the source dataset.

## 1. Use the following code to get the most significant SNPs for the European ancestry 
- ``awk '$9 < 5e-8' DIAMANTE-EUR.sumstat.txt > significant_snps.txt``
- The paper mentions that they used 5e-9 as the threshold for genome-wide significance to account for linkage disequilibrium (LD), but the summary statistics provided in the data contain results with fixed_effectsp-values greater than 5e-8

### BUT !!
The Fixed-effects p-value is used when combining results from different studies to get a single p-value that summarizes the overall evidence for a SNP’s association with the disease across all studies (in this case, the European studies). Imagine you have three studies:

- Study 1 says a SNP is associated with Type 2 Diabetes with a p-value of 0.03.
- Study 2 says the same SNP is associated with p-value 0.01.
- Study 3 says the same SNP has a p-value of 0.005.


A Fixed-effects meta-analysis will combine all these p-values to calculate a single p-value for the SNP based on all studies together. This combined p-value will give a better picture of the SNP’s true effect on Type 2 Diabetes across all studies.

In short:
- **Normal p-value**: Tells you if a SNP is significant in one study.
- **Fixed-effects p-value**: Combines p-values from multiple studies and assumes the SNP has the same effect across all studies. It gives a more overall, combined measure of whether a SNP is associated with Type 2 Diabetes.

## 2. Filter based on MAF > 0.01:
``awk '$7 > 0.01' eur_significant_snps.txt > eur_filtered_snps.txt``

## 3.PLINK for LD pruning 
If you don’t have access to the genotypic data (e.g., just summary statistics), you'll need the actual genotype data (which would typically come from your GWAS) or from a public reference panel like 1000 Genomes.

#### 1. **Obtain the 1000 Genomes Genotype Data**
   - If you don't already have access to the **1000 Genomes genotype data**, you can download it from the [1000 Genomes Project website](https://www.internationalgenome.org/data/).
   - The data should be in **PLINK format** (e.g., `.ped`, `.map`, `.bed`, `.bim`, `.fam` files).

#### 2. **Prepare the Summary Statistics**
   - The summary statistics file you have (`DIAMANTE-EUR.sumstat.txt`) already contains information such as:
     - `rsID`, `effect allele`, `other allele`, `effect size`, and **p-values**.
   - You can filter these for significant SNPs (p-value < 5 × 10⁻⁸) using the following command:

     ```bash
     awk '$10 < 5e-8' DIAMANTE-EUR.sumstat.txt > eur_significant_snps.txt
     ```

   This will extract all SNPs that meet the genome-wide significance threshold.

#### 3. **Perform LD Pruning**

   To perform **LD pruning** using PLINK, you will need the **1000 Genomes genotype data**. Use the following PLINK command to prune the SNPs based on **linkage disequilibrium** (LD):

   ```bash
   plink --bfile 1000genomes_data --indep-pairwise 50 5 0.2 --out pruned_data


PS the code so far means ive gotten rid of the headings which isnt great tbh 
