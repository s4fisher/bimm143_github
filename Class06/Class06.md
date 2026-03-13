# Class06: R Functions
Sam Fisher (A18131929)

- [Background](#background)
- [A First Function](#a-first-function)
- [A Second Function](#a-second-function)
- [A new cool function](#a-new-cool-function)

## Background

Functions are the heart of using R. Everything we do involves calling
and using functions (from data input, analysis, to results output).

All functions have at least 3 things:

1.  A **name** the thing we used to call the function.
2.  One or more input **arguments** that are coma separated.
3.  The **body**, lines of code between curly brackets {} that does the
    work of the function.

## A First Function

Let’s write a silly wee function to add some numbers:

``` r
add <- function(x) {
  x + 1 
}
```

Let’s try it out

``` r
add(100)
```

    [1] 101

Will this work

``` r
add(c(100, 200, 300))
```

    [1] 101 201 301

Modify to be more useful

``` r
add <- function(x, y=1) {
  x + y
}
```

``` r
add(100, 10)
```

    [1] 110

Will this still work?

``` r
add(100)
```

    [1] 101

``` r
plot(1:10, col="blue", typ="b")
```

![](Class06_files/figure-commonmark/unnamed-chunk-7-1.png)

``` r
log(10, base=10)
```

    [1] 1

> **Note** Input arguments can either be **required** or **optional**.
> The later have a fall-back default that is specified in the function
> code with an equals sign.

## A Second Function

All functions in R look like this

    name <- function(arg) {body}

The `sample()` function in R…

``` r
sample(1:10, 4)
```

    [1]  8 10  7  2

> Q. Return 12 numbers picked randomly from the input 1:10

``` r
sample(1:10, 12, replace=T)
```

     [1]  5  1  3  3  1  9  3  4  5  7  7 10

> Q. Write the code to generate a 12 nucleotide long DNA sequence?

``` r
sample(c("A","T", "G", "C"), 12, replace=T)
```

     [1] "C" "G" "A" "C" "T" "T" "C" "G" "T" "A" "T" "C"

> Q. Write a first version function called `generate_dna()` that
> generates a user specified length `n` random DNA sequence?

``` r
generate_DNA <- function(n = 10) {
  sample(c("A","T","G","C"), size = n, replace = TRUE)
}
```

``` r
generate_DNA()
```

     [1] "T" "G" "G" "A" "T" "A" "C" "A" "G" "A"

> Q. Modify your function to return FASTA like sequence so rather than
> \[1\] “G” “A” “C” “A” “T” we want “GACAT”. We want an actual sequence
> returned.

``` r
generate_DNA <- function(n=10) {
  seq <- sample(c("A", "T", "C", "G"), size = n, replace = T)
  paste(seq, collapse = "")
}
```

``` r
generate_DNA()
```

    [1] "TGAACAGTGC"

> Q. Give the user an option to return FASTA format output sequence or
> standard multi-element vector format?

``` r
generate_DNA <- function(n=10, fasta=TRUE) {
  ans <- sample(c("A", "T", "C", "G"), size = n, replace = T)
  
  if(fasta) {
    ans <- paste(ans, collapse = "")
    cat("Hello...")
  } else {
    cat("... is it me you're looking for...")
  }
  
  return(ans)
}
```

``` r
generate_DNA(10)
```

    Hello...

    [1] "TCCGTACACA"

``` r
generate_DNA(10, fasta=FALSE)
```

    ... is it me you're looking for...

     [1] "A" "T" "T" "C" "G" "G" "T" "C" "C" "A"

## A new cool function

> Q. Write a function called `generate_protein()` that generates a user
> specified length protein sequence in FASTA like format?

``` r
generate_protein <- function(n = 10, fasta = TRUE) {
  amino <- c("A","R","N","D","C","E","Q","G","H","I","L","K","M","F","P","S","T","W","Y","V")
  seq <- sample(amino, size = n, replace = TRUE)

  if (fasta) {
    return(paste(seq, collapse = "")) 
  } else {
    return(seq)
  }
}
```

``` r
generate_protein(15)
```

    [1] "EARIAFDKQEKLCQT"

``` r
generate_protein(15, fasta=FALSE)
```

     [1] "C" "W" "A" "A" "W" "Y" "R" "H" "Y" "P" "W" "S" "F" "L" "T"

> Q. Use your new `generate_protein()` function to generate all
> sequences between length 6 and 12 amino-acids in length and check if
> any of these are unique in nature (i.e. found in the NR database at
> NCBI)?

``` r
for(i in 6:12) {
  cat(">",i, sep="", "\n")
  cat(generate_protein(i), "\n")
}
```

    >6
    VWRMAV 
    >7
    TPWRMTC 
    >8
    PVRDHCKI 
    >9
    VAHCFRCQT 
    >10
    YDSILCYHGW 
    >11
    FKDETVGGYGM 
    >12
    ICTMPSMVFANV 

Identical matches were only seen in the amino acids that were generated
with lengths 6 and 7.
