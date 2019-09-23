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
```

Good Try\!

\#\#Looking at data
