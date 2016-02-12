---
layout: page
title: "Inference: t-tests, multiple comparisons"
---




# Introduction

In this section we will cover inference in the context of genomics experiments. We apply some of the concepts we have covered in previous sections including t-tests and multiple comparisons; later we will
compute standard deviation estimates from hierarchical models. 

We start by loading the pooling experiment data 



```r
library(Biobase)
library(maPooling)
data(maPooling)
pd=pData(maPooling)
individuals=which(rowSums(pd)==1)
```

And extracting the individual mice as well as their strain


```r
individuals=which(rowSums(pd)==1)
individuals=individuals[-grep("tr",names(individuals))]
y=exprs(maPooling)[,individuals]
g=factor(as.numeric(grepl("b",names(individuals))))
```


# T-test

We can now apply a t-test to each gene using the `rowttest` function in the `genefilter` package


```r
library(genefilter)
```

```
## 
## Attaching package: 'genefilter'
```

```
## The following object is masked from 'package:base':
## 
##     anyNA
```

```r
tt=rowttests(y,g)
```

Now which genes do we report as statistically significant? For somewhat arbitrary reasons, in science p-values of 0.01 and 0.05 are used as cutoff. In this particular example we get 


```r
sum(tt$p.value<0.01)
```

```
## [1] 1578
```

```r
sum(tt$p.value<0.05)
```

```
## [1] 2908
```


# Multiple testing
We described multiple testing in detail in course 3. Here we provide a quick summary.

Do we report all these genes? Let's explore what happens if we split the first group into two, forcing the null hypothesis to be true


```r
set.seed(0)
shuffledIndex <- factor(sample(c(0,1),sum(g==0),replace=TRUE ))
nulltt <- rowttests(y[,g==0],shuffledIndex)
sum(nulltt$p.value<0.01)
```

```
## [1] 79
```

```r
sum(nulltt$p.value<0.05)
```

```
## [1] 840
```

If we use the 0.05 cutoff we will be reporting 840 false positives. We have described several ways to adjust for this include the `qvalue` method available in the `qvalue` package. After this adjustment we include a smaller list of genes.


```r
library(qvalue)
qvals = qvalue(tt$p.value)$qvalue
sum(qvals<0.05)
```

```
## [1] 1179
```

```r
sum(qvals<0.01)
```

```
## [1] 538
```

And now the null case generates fewer false positives:


```r
library(qvalue)
nullqvals = qvalue(nulltt$p.value)$qvalue
sum(nullqvals<0.05)
```

```
## [1] 0
```

```r
sum(nullqvals<0.01)
```

```
## [1] 0
```
