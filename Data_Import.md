Data Import
================
Jingyi
9/22/2019

I downloaded the data and created a GitHub repo + local R project.

**How to move the datasets to my repo/directory?** *Is
<https://help.github.com/en/articles/moving-a-file-to-a-new-location>
correct?* **Follow the lecture, and just add the data files into the
local R folder.**

## Load in a dataset

*I can use library(here) and here::here(“file\_I\_want.csv”) to find
where my file is.*

**Never ever use setwd()**

``` r
## reads in a dataset

# Load from a relative path.
# Question: What's the difference between read_csv and read.csv?
litters_data = read_csv(file = "./data/FAS_litters.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

``` r
## use janitor::clean_names() to clean up variable names after importing
## This will take the column names are and convert them to lower snake case.

names(litters_data)
```

    ## [1] "Group"             "Litter Number"     "GD0 weight"       
    ## [4] "GD18 weight"       "GD of Birth"       "Pups born alive"  
    ## [7] "Pups dead @ birth" "Pups survive"

``` r
litters_data = janitor::clean_names(litters_data)

names(litters_data)
```

    ## [1] "group"           "litter_number"   "gd0_weight"      "gd18_weight"    
    ## [5] "gd_of_birth"     "pups_born_alive" "pups_dead_birth" "pups_survive"

``` r
# Question: Why my output are not italized?
```

**The package::function syntax lets you use a function from a package
without loading the whole library.** *Figure out what is conflicted
package.*

***Leanring Assessment***

Make sure you are able to load the FAS\_pups.csv dataset. Use both
absolute and relative paths. Quit R Studio and move the directory
containing your project, data, and R Markdown document. Repeat the
previous data import process; do both absolute and relative paths still
work?

``` r
# load from absolute path
FAS_pups_absolute = read_csv(file = "C://Users//Jingyi//Desktop//Columbia_University//Fall2019//P8105_Data_Science_I//Lecture In-Class Codes//Data_Wrangling_I//data_wrangling_i//Data//FAS_pups.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

``` r
# load from relative path
FAS_pups_relative = read.csv(file = "./data/FAS_pups.csv")

FAS_pups = janitor::clean_names(FAS_pups_relative)
```

Good Try\!

\#\#Looking at data

The first thing to do after importing the data (unless read\_csv gives
warnings) is to look at it. If there are unexpected results during data
import, you’ll catch a lot of them here. In addition to printing the
data, often time you can use View / view, str, head, and tail

``` r
#This will show the data
litters_data
```

    ## # A tibble: 49 x 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                 19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2           27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3         NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2         NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-~       NA          NA            20               9
    ##  8 Con8  #3/83/3-3           NA          NA            20               9
    ##  9 Con8  #2/95/3             NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95         28.5        NA            20               8
    ## # ... with 39 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

``` r
#This will show the last 5 rows of data
tail(litters_data, 5)
```

    ## # A tibble: 5 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Low8  #100                20          39.2          20               8
    ## 2 Low8  #4/84               21.8        35.2          20               4
    ## 3 Low8  #108                25.6        47.5          20               8
    ## 4 Low8  #99                 23.5        39            20               6
    ## 5 Low8  #110                25.5        42.7          20               7
    ## # ... with 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

Another tool to use can be skimr::skim.

``` r
skimr::skim(litters_data)
```

    ## Skim summary statistics
    ##  n obs: 49 
    ##  n variables: 8 
    ## 
    ## -- Variable type:character ----------------------------
    ##       variable missing complete  n min max empty n_unique
    ##          group       0       49 49   4   4     0        6
    ##  litter_number       0       49 49   3  15     0       49
    ## 
    ## -- Variable type:numeric ------------------------------
    ##         variable missing complete  n  mean   sd   p0   p25   p50   p75
    ##      gd_of_birth       0       49 49 19.65 0.48 19   19    20    20   
    ##       gd0_weight      15       34 49 24.38 3.28 17   22.3  24.1  26.67
    ##      gd18_weight      17       32 49 41.52 4.05 33.4 38.88 42.25 43.8 
    ##  pups_born_alive       0       49 49  7.35 1.76  3    6     8     8   
    ##  pups_dead_birth       0       49 49  0.33 0.75  0    0     0     0   
    ##     pups_survive       0       49 49  6.41 2.05  1    5     7     8   
    ##  p100     hist
    ##  20   <U+2585><U+2581><U+2581><U+2581><U+2581><U+2581><U+2581><U+2587>
    ##  33.4 <U+2581><U+2583><U+2587><U+2587><U+2587><U+2586><U+2581><U+2581>
    ##  52.7 <U+2582><U+2583><U+2583><U+2587><U+2586><U+2582><U+2581><U+2581>
    ##  11   <U+2582><U+2582><U+2583><U+2583><U+2587><U+2585><U+2581><U+2581>
    ##   4   <U+2587><U+2582><U+2581><U+2581><U+2581><U+2581><U+2581><U+2581>
    ##   9   <U+2582><U+2582><U+2583><U+2583><U+2585><U+2587><U+2587><U+2585>

*Call View/view in R console, because this function don’t work well with
RMD.* This function will open a window (looks like excel) for you to
view the dataset.

\#\#Arguments to read\_\*

In the best case, the data are stored in the csv without any weirdness –
there are no blank lines or columns, the first row is the variable name,
missing values are stored in sensible ways.

When this isn’t the case, arguments to read\_csv are helpful. The ones I
use most frequently are:

*col\_names: usually TRUE. If FALSE, column names are X1, X1, … . You
can also supply column names. *na: string vector containing character
expressions for missing values. \*skip: number of rows to skip before
reading data.

For example, the call below will skip the first 50 lines of data and not
assume the first row are variable names:

``` r
litters_data = read_csv(file = "./data/FAS_litters.csv", skip = 10, col_names = FALSE)
```

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_character(),
    ##   X2 = col_character(),
    ##   X3 = col_double(),
    ##   X4 = col_double(),
    ##   X5 = col_double(),
    ##   X6 = col_double(),
    ##   X7 = col_double(),
    ##   X8 = col_double()
    ## )

``` r
head(litters_data)
```

    ## # A tibble: 6 x 8
    ##   X1    X2                 X3    X4    X5    X6    X7    X8
    ##   <chr> <chr>           <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 Con8  #3/5/2/2/95      28.5    NA    20     8     0     8
    ## 2 Con8  #5/4/3/83/3      28      NA    19     9     0     8
    ## 3 Con8  #1/6/2/2/95-2    NA      NA    20     7     0     6
    ## 4 Con8  #3/5/3/83/3-3-2  NA      NA    20     8     0     8
    ## 5 Con8  #2/2/95/2        NA      NA    19     5     0     4
    ## 6 Con8  #3/6/2/2/95-3    NA      NA    20     7     0     7

These arguments generally work for other members of the read\_\* family
of functions.

\#\#Parsing Columns:

Skipped col\_types

The read\_\* functions will attempt to guess the data type stored in
each column; by default, these guesses are based on the first 1000 rows.
The guesses are also usually pretty good.

In some cases, though, you’ll want to give explicit column
specifications.

``` r
litters_data = read_csv(file = "./data/FAS_litters.csv", col_types = cols(
  Group = col_character(),
  `Little Number` = col_character(),
  `GD18 weight` = col_double(),
  `GD of Birth` = col_integer(),
  `Pups born alive` = col_integer(),
  `Pups dead @ birth` = col_integer(),
  `Pups survive` = col_integer()
))
```

    ## Warning: The following named parsers don't match the column names: Little
    ## Number

``` r
tail(litters_data)
```

    ## # A tibble: 6 x 8
    ##   Group `Litter Number` `GD0 weight` `GD18 weight` `GD of Birth`
    ##   <chr> <chr>                  <dbl>         <dbl>         <int>
    ## 1 Low8  #79                     25.4          43.8            19
    ## 2 Low8  #100                    20            39.2            20
    ## 3 Low8  #4/84                   21.8          35.2            20
    ## 4 Low8  #108                    25.6          47.5            20
    ## 5 Low8  #99                     23.5          39              20
    ## 6 Low8  #110                    25.5          42.7            20
    ## # ... with 3 more variables: `Pups born alive` <int>, `Pups dead @
    ## #   birth` <int>, `Pups survive` <int>

``` r
# short-hand

litters_data = read_csv(file = "./data/FAS_litters.csv",
  col_types = "ccddiiii"
)

litters_data
```

    ## # A tibble: 49 x 8
    ##    Group `Litter Number` `GD0 weight` `GD18 weight` `GD of Birth`
    ##    <chr> <chr>                  <dbl>         <dbl>         <int>
    ##  1 Con7  #85                     19.7          34.7            20
    ##  2 Con7  #1/2/95/2               27            42              19
    ##  3 Con7  #5/5/3/83/3-3           26            41.4            19
    ##  4 Con7  #5/4/2/95/2             28.5          44.1            19
    ##  5 Con7  #4/2/95/3-3             NA            NA              20
    ##  6 Con7  #2/2/95/3-2             NA            NA              20
    ##  7 Con7  #1/5/3/83/3-3/2         NA            NA              20
    ##  8 Con8  #3/83/3-3               NA            NA              20
    ##  9 Con8  #2/95/3                 NA            NA              20
    ## 10 Con8  #3/5/2/2/95             28.5          NA              20
    ## # ... with 39 more rows, and 3 more variables: `Pups born alive` <int>,
    ## #   `Pups dead @ birth` <int>, `Pups survive` <int>

***Leanring Assessment***

Repeat the data import process above for the file FAS\_pups.csv. Make
sure the column names are reasonable, and take some quick looks at the
dataset. What happens if your specifications for column parsing aren’t
reasonable (e.g. character instead of double, or vice versa)?

``` r
#load the data:

FAS_pups_relative = read.csv(file = "./data/FAS_pups.csv")

FAS_pups = janitor::clean_names(FAS_pups_relative)

# look at data

head(FAS_pups)
```

    ##   litter_number sex pd_ears pd_eyes pd_pivot pd_walk
    ## 1           #85   1       4      13        7      11
    ## 2           #85   1       4      13        7      12
    ## 3     #1/2/95/2   1       5      13        7       9
    ## 4     #1/2/95/2   1       5      13        8      10
    ## 5 #5/5/3/83/3-3   1       5      13        8      10
    ## 6 #5/5/3/83/3-3   1       5      14        6       9

``` r
# parse

pups_data = read_csv(file = "./data/FAS_pups.csv", col_types = "ciiiii")
```
