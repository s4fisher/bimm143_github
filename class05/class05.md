# class05
Sam Fisher A18131929

- [Background](#background)
- [Going further with gapminder](#going-further-with-gapminder)
- [First look at the dplyr package](#first-look-at-the-dplyr-package)

## Background

There are lots of ways to make plots in R. These include so-called “Base
R” (like the ‘plot()’) and add on packages like **ggplot2**

Let’s make the same plot with these two graphics systems. We can use the
inbuilt ‘cars’ data set:

``` r
head(cars)
```

      speed dist
    1     4    2
    2     4   10
    3     7    4
    4     7   22
    5     8   16
    6     9   10

with “base R” we simply:

``` r
plot(cars)
```

![](class05_files/figure-commonmark/unnamed-chunk-2-1.png)

Now let’s try ggplot. First I need to install the package using
`install.packages("ggplot2")`

**N.I.K.** We never run on \`install.packages() in a code chunk
otherwise we will re-install needlessly every time we open the document

Every time we want to use an add we must load it up using `library()`

``` r
library(ggplot2)
ggplot(cars)
```

![](class05_files/figure-commonmark/unnamed-chunk-3-1.png)

Every ggplot needs at least three things:

1.  The **Data** i.e. stuff to plot as data frame
2.  the **aes** are aesthetics that map the data to the plot
3.  the **geom** or geometry i.e. the plot type such as scatter, linear,
    etc

``` r
ggplot(cars) +
  aes(x=speed, y=dist) + geom_point() + geom_line()
```

![](class05_files/figure-commonmark/unnamed-chunk-4-1.png)

``` r
ggplot(cars) + 
  aes(x=speed, y=dist) + geom_point() + geom_smooth(method="lm", se=FALSE) + labs(x="speed(mph)", y="Distance(ft)", title="Stopping Distance of Old Cars") + theme_bw()
```

    `geom_smooth()` using formula = 'y ~ x'

![](class05_files/figure-commonmark/unnamed-chunk-5-1.png)

\##Gene Expression Plot

Read some data on the effects of GLP-1 inhibitor (drug) on gene
expression values:

``` r
url <- "https://bioboot.github.io/bimm143_S20/class-material/up_down_expression.txt"
genes <- read.delim(url)
head(genes)
```

            Gene Condition1 Condition2      State
    1      A4GNT -3.6808610 -3.4401355 unchanging
    2       AAAS  4.5479580  4.3864126 unchanging
    3      AASDH  3.7190695  3.4787276 unchanging
    4       AATF  5.0784720  5.0151916 unchanging
    5       AATK  0.4711421  0.5598642 unchanging
    6 AB015752.4 -3.6808610 -3.5921390 unchanging

Version 1 plot - start simple by getting some ink on the page

``` r
ggplot(genes) + 
  aes(x=Condition1, y=Condition2) + 
  geom_point(col="blue", alpha=0.2)
```

![](class05_files/figure-commonmark/unnamed-chunk-7-1.png)

Let’s color by `State` up, down or no change

``` r
table(genes$State)
```


          down unchanging         up 
            72       4997        127 

``` r
ggplot(genes) + 
  aes(x=Condition1, y=Condition2, col=State) + 
  geom_point() +
  scale_color_manual(values=c("red",
                              "gray",
                              "blue") ) + 
  labs(x="Control (no drug)",
     y= "Drug",
     title= "Expression changes with GLP-1 Drug") 
```

![](class05_files/figure-commonmark/unnamed-chunk-9-1.png)

## Going further with gapminder

Here we explore the famous `gapminder` dataset with some custom plots.

``` r
# File location online
url <- "https://raw.githubusercontent.com/jennybc/gapminder/master/inst/extdata/gapminder.tsv"

gapminder <- read.delim(url)
head(gapminder)
```

          country continent year lifeExp      pop gdpPercap
    1 Afghanistan      Asia 1952  28.801  8425333  779.4453
    2 Afghanistan      Asia 1957  30.332  9240934  820.8530
    3 Afghanistan      Asia 1962  31.997 10267083  853.1007
    4 Afghanistan      Asia 1967  34.020 11537966  836.1971
    5 Afghanistan      Asia 1972  36.088 13079460  739.9811
    6 Afghanistan      Asia 1977  38.438 14880372  786.1134

> Q. How many rows does this dataset have?

``` r
nrow(gapminder) 
```

    [1] 1704

> Q. How many different continents are in this dataset?

``` r
table(gapminder$continent)
```


      Africa Americas     Asia   Europe  Oceania 
         624      300      396      360       24 

Version 1 plot gdpPercap vs LifeExp for all rows

``` r
ggplot(gapminder) +
  aes(x=gdpPercap, y=lifeExp, col=continent) + 
  geom_point() 
```

![](class05_files/figure-commonmark/unnamed-chunk-13-1.png)

I want to see a plot for each continent - in ggplot lingo this is called
“faceting”

``` r
ggplot(gapminder) +
  aes(x=gdpPercap, y=lifeExp, col=continent) + 
  geom_point() +
  facet_wrap(~continent)
```

![](class05_files/figure-commonmark/unnamed-chunk-14-1.png)

## First look at the dplyr package

Another add-on package with a function called `filter()` that we want to
use

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
filter(gapminder, year == 2007, country == "Ireland")
```

      country continent year lifeExp     pop gdpPercap
    1 Ireland    Europe 2007  78.885 4109086     40676

``` r
input <- filter(gapminder, year == 2007 | year == 1997)
```

``` r
ggplot(gapminder) +
  aes(gdpPercap, lifeExp, color = continent) +
  geom_point() +
  filter(gapminder, year ==2007 | year == 1997) + 
  facet_wrap(~year)
```

![](class05_files/figure-commonmark/unnamed-chunk-18-1.png)
