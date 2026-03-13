# Class13
Samuel Fisher (A18131929)

## Background

Today we will perform an RNASeq analysis on the effects of dexamethasone
(hereafter “dex”), a common steroid, on airway smooth muscle (ASM) cell
lines.

## Data Import

We need two things for this analysis:

1)  **countData**: a table with genes as rows and samples/experiments as
    columns

2)  **colData**: metadata about the columns (i.e. samples) in the main
    countData object

``` r
#counts <- read.csv("airway_scaledcounts.csv", row.names=1)
#metadata <- read.csv("airway_metadata.csv")
```

Let’s have a peak at the objects:

``` r
#metadata
```

``` r
#counts
```

``` r
#head(counts)
```

\##Check on metadata counts correspondance

We need to check that the metadata matches the samples in our count
data.

``` r
#ncol(counts) == nrow(metadata)
```

``` r
#colnames(counts) == metadata$id
```

> Q1. How many genes are in this dataset?

``` r
#nrow(counts)
```

38694 genes in this dataset

> Q2. How many “control” samples are in this dataset?

``` r
#sum(metadata$dex == "control")
```

4 control samples in this dataset

## Analysis Plan

We have 4 replicates per condition (“control” and “treated”) (drug and
no drug respectively). We want to compare the control vs the treated to
see which gene’s expression levels change when we have the drug present.

We are going to row by row (gene by gene) and see if the average value
in control columns is different than the treated columns

Step 1: Find which columns in `counts` correspond to the “control”

Step 2: Filter / Select / Extract these columns

Step 3: Calculate an average value for each gene.

``` r
# The indices (i.e. positions) that are "control" 
#control.inds <- metadata$dex == "control"
```

``` r
# Extract/select these "control" columns from counts
#control.counts <- counts[, control.inds]
```

``` r
# Calculate the mean for each gene (i.e. row)
#control.mean <- rowMeans(control.counts)
```

> Q. Do the same for “treated” samples - find the mean count value per
> gene.

``` r
#treated.counts <- rowMeans(counts[, metadata$dex == "treated"])
```

Let’s put these two mean values into a new data.frame `meancounts` for
easy book-keeping and plotting.

``` r
#treated.mean <- rowMeans(counts[, metadata$dex == "treated"])
```

``` r
#meancounts <- data.frame(control.mean,
                       #  treated.mean)
#head(meancounts)
```

> Q. Make a ggplot of average counts of control vs treated

``` r
#library(ggplot2)

#ggplot(meancounts) +
 # aes(control.mean,
    # treated.mean) +
  #geom_point(alpha=0.4)
```

Adding logarithmic axises

``` r
#library(ggplot2)

#ggplot(meancounts) +
#  aes(control.mean,
    #  treated.mean) +
  #geom_point(alpha=0.4) +
 # scale_x_log10() +  scale_y_log10() 
```

## Log2 units and fold change

If we consider “treated”/“control” counts we will get a number that
tells us the change.

``` r
# No change
#log2(20/20)
```

``` r
# A doubling in the treated vs control 
#log2(40/20)
```

``` r
# A halving in the treated vs control
#log2(10/20)
```

> Q. Add a new column called `log2fc` for log2 fold change of treated/
> control to our `meancounts` object.

``` r
#meancounts$log2fc <- 
 # log2(meancounts$treated.mean/
   #      meancounts$control.mean)

#head(meancounts)
```

## Remove zero count genes

Typically we would not consider zero count genes - as we have no data
about them and they should be excluded from further consideration. These
lead to “funky” log2 fold change values (e.g. divide by zero errors
etc.)

## DESeq analysis

We are missing any measure of significance from the work we have so far.
Let’s do this properly with the **DESeq2 package.**

``` r
#library(DESeq2)
```

The DESeq2 package, like many bioconductor packages, wants it’s input in
a very specific way - a data structure setup with all the info it needs
for the calculations.

``` r
#dds <- DESeqDataSetFromMatrix(countData = counts,
                       #colData = metadata,
                       #design = ~dex)
```

The main function in this package is called `DESeq()` it will run the
full analysis for us on our `dds` input object:

``` r
#dds <- DESeq(dds)
```

Extract our results:

``` r
#res <- results(dds)
#head(res)
```

## Volcano Plot

A useful summary figure of our results is often called a volcano plot.
It’s basically a plot of log2 fold change values vs adjusted p-values.

> Q. Use ggplot to make a first version “volcano plot” of
> `log2FoldChange` vs `padj`

``` r
#ggplot(res, aes(log2FoldChange, padj)) + geom_point()
```

This is niot very useful because the y-axis (P-value) is not really
helpful - we want to focus on low P-values.

``` r
#ggplot(res) +
  #aes(x = log2FoldChange,
    #  y = log10(padj)) +
  #geom_point()
```

``` r
#ggplot(res) +
  #aes(x = log2FoldChange,
      #y = -log10(padj)) +
  #geom_point()
```

``` r
#ggplot(res) +
  #aes(x = log2FoldChange,
     # y = -log10(padj)) +
  #geom_point() +
  #geom_vline(xintercept = c(-2, 2), col="red") +
  #geom_hline(yintercept = -log(0.05), col = "red") 
```

## Add some plot annotation

> Q. Add color to the points (genes) we care about, nice axis labels, a
> useful title and a nice theme.

``` r
#ggplot(res) +
 # aes(x = log2FoldChange,
    #  y = -log10(padj)) +
 # geom_point() +
 # geom_vline(xintercept = c(-2, 2), col="red") +
 # geom_hline(yintercept = -log(0.05), col = "red") 
```

``` r
#mycols <- rep("gray", nrow(res))
#mycols[res$log2FoldChange > 2] <- "blue"
#mycols[res$log2FoldChange < -2 ] <- "darkgreen"
#mycols[res$padj >= 0.05] <- "gray"
```

``` r
#ggplot(res, aes(x = log2FoldChange,
   #             y = -log10(padj))) +
  #geom_point(color = mycols, alpha = 0.5) +
  #geom_vline(xintercept = c(-2, 2), col = "red") +
  #geom_hline(yintercept = -log10(0.05), col = "red") +
  #labs(title = "Volcano Plot",
    #   x = "Log2 Fold Change",
   #    y = "-Log10 Adjusted P-value") +
  #theme_minimal()
```

## Save our results to a CSV file

``` r
#write.csv(res, file="results.csv")
```
