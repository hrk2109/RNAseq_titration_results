# Cross-platform normalization enables machine learning model training on microarray and RNA-seq data simultaneously

## Summary

We performed a series of supervised and unsupervised machine learning 
evaluations, as well as differential expression analyses, to assess which 
normalization methods are best suited for combining data from microarray and 
RNA-seq platforms. We evaluated five normalization approaches for all methods: 
log-transformation (LOG), non-paranormal transformation (NPN), 
quantile normalization (QN), Training Distribution Matching (TDM), and 
standardizing scores (z-scoring; Z).

A version of this project (`DATE`) is detailed in our pre-print 
`TODO: LINK TO PREPRINT` 
_We are actively making improvements to this codebase; see [#12](https://github.com/greenelab/RNAseq_titration_results/issues/12), [#19](https://github.com/greenelab/RNAseq_titration_results/issues/19), and [#20](https://github.com/greenelab/RNAseq_titration_results/issues/20)._
 
## Data

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.58862.svg)](https://doi.org/10.5281/zenodo.58862)

The Cancer Genome Atlas BRCA data used for these analyses
is [available at zenodo](https://zenodo.org/record/58862).

```
# To download data, run in top directory:
sh brca_data_download.sh
```

## Analysis

### Machine Learning Pipeline

Here's a schematic overview of our machine learning experiments:

![](https://github.com/greenelab/RNAseq_titration_results/blob/master/diagrams/RNA-seq_titration_ML_overview.png)

**Overview of supervised and unsupervised machine learning experiments.** (A) 
520 TCGA Breast Cancer samples run on both microarray and RNA-seq were split 
into a training (2/3) and hold-out set (1/3). (B) RNA-seq’d samples were 
"titrated" into the training set, 10% at a time (0-100%) resulting in eleven 
training sets for each normalization method. (C) _Machine learning 
applications._ Three supervised multi-class (BRCA PAM50 subtype) classifiers 
were trained on each training set and tested on the microarray and RNA-seq 
hold-out sets. The hold-out sets were projected into and back out of the 
training set space using two unsupervised techniques, Independent and Principal 
Components Analysis, to obtain reconstructed hold-out sets. The classifiers used 
in 4A were used to predict on the reconstructed hold-out sets. 

```
# To run the machine learning pipeline, run in top directory:
sh run_machine_learning_experiments.sh

# To run one repeat of the subtype classifier pipeline, use:
Rscript run_experiments.R
```

### Differential Expression Pipeline

Here's a schematic overview of our main differential expression experiment:

![](https://github.com/greenelab/RNAseq_titration_results/blob/master/diagrams/RNA-seq_titration_diff_expression_overview.png?raw=true)

**Overview of differential expression experiment.** All matched TCGA breast 
cancer samples (n = 520) were considered when building the platform-specific 
“silver standards.” These standards are the genes that were differentially 
expressed at a specified False Discovery Rate (FDR) using data sets comprised 
entirely of one platform and processed in a standard way: log2-transformed 
microarray data and “untransformed” RSEM count data (preprocessed using the 
`limma::voom` function). RNA-seq’d samples were ‘titrated’ into the data set, 
10% at a time (0-100%) resulting in eleven experimental sets for each n
ormalization method. Differentially expressed genes (DEGs) were identified using 
the `limma` package. We compared the Her2 and LumA subtypes as well as Basal
v. all other samples. Lists of experimental DEGs were compared to standard gene 
sets using Jaccard similarity. 

```
# Note: This requires the data to be processed to include matched samples only, 
# and split into training and test sets (0-expression_data_overlap_and_split.R)

# To run the differential expression pipeline, run in top directory:
sh run_differential_expression_experiments.sh
```

## Requirements

This analysis was performed in 	R. It requires R & Bioconductor packages 
detailed in `check_installs.R` to be installed.

One github package (`TDM`) is required. To install, run:

    library(devtools)
    devtools::install_github("greenelab/TDM")

**This analysis is [in the process](https://github.com/greenelab/RNAseq_titration_results/issues/18) of being moved to a Docker image.**
