---
title: "Introduction to Advanced Statistics for the Life Sciences"
author: "Rafa"
date: "January 31, 2015"
output: html_document
layout: page
---




# Installing Bioconductor

Many of the datasets we will use in this chapter require packages made availalbe via the Bioconductor project. Bioconductor is similar to CRAN but uses a different set of functions for downloads. It also includes many more data packages as well as _annotation_ packages that store information about either highthroughout products or information about molecular endpoints such as genes. We will need to some of these packages in this chapter. Here we show how to install the Biobase package. 


```r
source("http://bioconductor.org/biocLite.R")
```

```
## Bioconductor version 3.0 (BiocInstaller 1.16.1), ?biocLite for help
```

```r
biocLite("Biobase")
```

```
## BioC_mirror: http://bioconductor.org
## Using Bioconductor version 3.0 (BiocInstaller 1.16.1), R version 3.1.2.
## Installing package(s) 'Biobase'
```

```
## 
## The downloaded binary packages are in
## 	/var/folders/79/m125hnm95cs7j8dm_1xj_qk40000gp/T//Rtmpqo2mJN/downloaded_packages
```

```
## Old packages: 'boot', 'class', 'cluster', 'codetools', 'colorspace',
##   'foreign', 'KernSmooth', 'lattice', 'manipulate', 'MASS', 'Matrix',
##   'mgcv', 'nlme', 'nnet', 'qvalue', 'rpart', 'spatial', 'survival'
```

You can install a suite of recommended packages by simply typing `biocLite()`

# Data organized in three tables

One of the great advantages of using Bioconductor for high throughput data is that it provides object classes specifically designed to keep high throughput data organized. Below we show an example of how the three tables that are needed to conduct data analysis are avaialabe from bioconductor data objects. For example for gene expression we can use the ExpressionSet object.


```r
library(Biobase)
##can be installed like this: devtools::install_github("genomicsclass/GSE5859")
library(GSE5859)
data(GSE5859)
class(e)
```

```
## [1] "ExpressionSet"
## attr(,"package")
## [1] "Biobase"
```


These objects were originally designed for gene expression data so the methods to extract the high throughput measurements have related names:

```r
dat <- exprs(e)
dim(dat)
```

```
## [1] 8793  208
```

The information about samples is also stored in this object and the functions to create it try to guarantee that the columns of `exprs(e)` match the rows of the sample information table. `pData` is use as shorthand for _phenotype_ data. 
:


```r
sampleInfo <- pData(e)
dim(sampleInfo)
```

```
## [1] 208   3
```

```r
head(sampleInfo)
```

```
##   ethnicity       date        filename
## 1       CEU 2003-02-04 GSM25349.CEL.gz
## 2       CEU 2003-02-04 GSM25350.CEL.gz
## 3       CEU 2002-12-17 GSM25356.CEL.gz
## 4       CEU 2003-01-30 GSM25357.CEL.gz
## 5       CEU 2003-01-03 GSM25358.CEL.gz
## 6       CEU 2003-01-16 GSM25359.CEL.gz
```

A final table, which we will cover in much more detail in the Bioconducto chapter, is a table that describes the rows, in this case genes. Because each product will have a different table, these have already been created in Bioconductor. Becuase there are certain products that are widely used, Bioconductor makes databases available from which you can extract this information. This every object does not have to carry around this information:
 

```r
library(hgfocus.db)
```

```
## Loading required package: AnnotationDbi
## Loading required package: stats4
## Loading required package: GenomeInfoDb
## Loading required package: S4Vectors
## Loading required package: IRanges
## 
## Attaching package: 'AnnotationDbi'
## 
## The following object is masked from 'package:GenomeInfoDb':
## 
##     species
## 
## Loading required package: org.Hs.eg.db
## Loading required package: DBI
```

```r
annot <- elect(hgfocus.db, keys=featureNames(e), keytype="PROBEID", columns=c("CHR", "CHRLOC", "SYMBOL"))
```

```
## Error in eval(expr, envir, enclos): could not find function "elect"
```

```r
##pick one
annot <-annot[match(featureNames(e),annot$PROBEID),]
```

```
## Error in eval(expr, envir, enclos): object 'annot' not found
```

```r
head(annot)
```

```
## Error in head(annot): error in evaluating the argument 'x' in selecting a method for function 'head': Error: object 'annot' not found
```

```r
dim(annot)
```

```
## Error in eval(expr, envir, enclos): object 'annot' not found
```