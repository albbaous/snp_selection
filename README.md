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

## Then use the following code to get the most significant SNPs for the European ancestry 
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
