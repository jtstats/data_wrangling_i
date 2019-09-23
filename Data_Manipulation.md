Data Manipulation with dplyr
================
Jingyi
9/23/2019

\#\#Selecting

For a given analysis, you may only need a subset of the columns in a
data table; extracting only what you need can helpfully de-clutter,
especially when you have large datasets.

Select columns using *select*.

\*\*select(dataname, variable\*)\*\*

``` r
# You can specify the columns you want to keep by naming all of them:

select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 x 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ... with 46 more rows

``` r
# return with the order above

# If you want to select everything, here is a helper function:
select(litters_data, litter_number, group, everything())
```

    ## # A tibble: 49 x 8
    ##   litter_number group gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr>         <chr>      <dbl>       <dbl>       <int>           <int>
    ## 1 #85           Con7        19.7        34.7          20               3
    ## 2 #1/2/95/2     Con7        27          42            19               8
    ## 3 #5/5/3/83/3-3 Con7        26          41.4          19               6
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   pups_survive <int>

``` r
# If you want to select column names that start with “pups”, here is a helper function:
select(litters_data, litter_number, gd0_weight, starts_with("pups"))
```

    ## # A tibble: 49 x 5
    ##   litter_number gd0_weight pups_born_alive pups_dead_birth pups_survive
    ##   <chr>              <dbl>           <int>           <int>        <int>
    ## 1 #85                 19.7               3               4            3
    ## 2 #1/2/95/2           27                 8               0            7
    ## 3 #5/5/3/83/3-3       26                 6               0            5
    ## # ... with 46 more rows

``` r
# You can specify the specify a range of columns to keep:
select(litters_data, group:gd_of_birth)
```

    ## # A tibble: 49 x 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ... with 46 more rows

``` r
# You can also specify columns you’d like to remove:

select(litters_data, -pups_survive)
```

    ## # A tibble: 49 x 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 1 more variable: pups_dead_birth <int>

``` r
# You can rename variables as part of this process:

select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 x 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # ... with 46 more rows

``` r
# If all you want to do is rename something, you can use rename instead of select. This will rename the variables you care about, and keep everything else:

rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 x 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   pups_survive <int>

There are some handy helper functions for select; read about all of them
using ?select\_helpers. The one I use most frequently is everything(),
which is handy for reorganizing columns without discarding anything:

``` r
select(litters_data, litter_number, group, everything())
```

    ## # A tibble: 49 x 8
    ##   litter_number group gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr>         <chr>      <dbl>       <dbl>       <int>           <int>
    ## 1 #85           Con7        19.7        34.7          20               3
    ## 2 #1/2/95/2     Con7        27          42            19               8
    ## 3 #5/5/3/83/3-3 Con7        26          41.4          19               6
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   pups_survive <int>

In larger datasets, I also use starts\_with(), ends\_with(), and
contains() often.

Lastly, like other functions in dplyr, select will export a dataframe
even if you only select one column. Mostly this is fine, but sometimes
you want the vector stored in the column.

To pull a single variable, use pull.

\#\#\#Leanring Assessment\#1: In the pups data, select the columns
containing litter number, sex, and PD ears.

``` r
select(pups_data, litter_number:pd_ears)
```

    ## # A tibble: 313 x 3
    ##   litter_number   sex pd_ears
    ##   <chr>         <int>   <int>
    ## 1 #85               1       4
    ## 2 #85               1       4
    ## 3 #1/2/95/2         1       5
    ## # ... with 310 more rows

\#\#Fliter

Some data tables will include rows you don’t need for your current
analysis.

Although you could remove specific row numbers using base R, you
shouldn’t – this might break if the raw data are updated, and the
thought process isn’t transparent.

Instead, you should filter rows based on logical expressions using the
filter function.

Like select, the first argument to filter is the dataframe you’re
filtering; all subsequent arguments are logical expressions.

You will often filter using comparison operators (\>, \>=, \<, \<=, ==,
and \!=).

You may also use %in% to detect if values appear in a set, and is.na()
to find missing values.

The results of comparisons are logical – the statement is TRUE or FALSE
depending on the values you compare – and can be combined with other
comparisons using the logical operators & and |, or negated using \!.

Some ways you might filter the litters data are:

  - gd\_of\_birth == 20
  - pups\_born\_alive \>= 2
  - pups\_survive \!= 4
  - \!(pups\_survive == 4)
  - group %in% c(“Con7”, “Con8”)
  - group == “Con7” & gd\_of\_birth == 20

A very common filtering step requires you to omit missing observations.
You can do this with filter, but I recommend using drop\_na from the
tidyr package:

  - drop\_na(litters\_data) will remove any row with a missing value.
  - drop\_na(litters\_data, wt\_increase) will remove rows for which
    wt\_increase is missing.

Filtering can be helpful for limiting a dataset to only those
observations needed for an analysis.

However, I recommend against the creation of many data subsets (e.g. one
for each group). This can clutter up your workspace, and we’ll see good
tools for the analysis of subsets before long.

\#Learning Assessment \#2:

In the pups data: \* Filter to include only pups with sex 1 \* Filter to
include only pups with PD walk less than 11 and sex 2

``` r
filter(pups_data, sex == 1)
```

    ## # A tibble: 155 x 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # ... with 152 more rows

``` r
filter(pups_data, sex == 2 & pd_walk < 11)
```

    ## # A tibble: 127 x 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # ... with 124 more rows

## Mutate

Sometimes you need to select columns; sometimes you need to change them
or create new ones. You can do this using mutate.

The example below creates a new variable measuring the difference
between gd18\_weight and gd0\_weight and modifies the existing group
variable.

``` r
mutate(litters_data, wt_gain = gd18_weight - gd0_weight, group = str_to_lower(group))
```

    ## # A tibble: 49 x 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 3 more variables: pups_dead_birth <int>,
    ## #   pups_survive <int>, wt_gain <dbl>

``` r
# Question: What does this str_to_lower(group) do?
# make all string variables to their lower cases
# I can modify and overwrite the existing variables.
```

A few things in this example are worth noting:

  - Your new variables can be functions of old variables
  - New variables appear at the end of the dataset in the order that
    they are created
  - You can overwrite old variables
  - You can create a new variable and immediately refer to (or change)
    it

Creating a new variable that does exactly what you need can be a
challenge; the more functions you know about, the easier this gets.

\#\#Learning Assessment \#3

In the pups data:

  - Create a variable that subtracts 7 from PD pivot

  - Create a variable that is the sum of all the PD variable

<!-- end list -->

``` r
mutate(pups_data, pd_pivotsub7 = pd_pivot - 7, pd_sum = pd_ears + pd_eyes + pd_pivot + pd_walk)
```

    ## # A tibble: 313 x 8
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pd_pivotsub7 pd_sum
    ##   <chr>         <int>   <int>   <int>    <int>   <int>        <dbl>  <int>
    ## 1 #85               1       4      13        7      11            0     35
    ## 2 #85               1       4      13        7      12            0     36
    ## 3 #1/2/95/2         1       5      13        7       9            0     34
    ## # ... with 310 more rows

## arrange

In comparison to the preceding, arranging is pretty straightforward.

You can arrange the rows in your data according to the values in one or
more columns:

``` r
head(arrange(litters_data, group, pups_born_alive), 10)
```

    ## # A tibble: 10 x 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ##  1 Con7  #85                 19.7        34.7          20               3
    ##  2 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  4 Con7  #4/2/95/3-3         NA          NA            20               6
    ##  5 Con7  #2/2/95/3-2         NA          NA            20               6
    ##  6 Con7  #1/2/95/2           27          42            19               8
    ##  7 Con7  #1/5/3/83/3-~       NA          NA            20               9
    ##  8 Con8  #2/2/95/2           NA          NA            19               5
    ##  9 Con8  #1/6/2/2/95-2       NA          NA            20               7
    ## 10 Con8  #3/6/2/2/95-3       NA          NA            20               7
    ## # ... with 2 more variables: pups_dead_birth <int>, pups_survive <int>

## %\>%

We’ve seen several commands you will use regularly for data manipulation
and cleaning.

You will rarely use them in isolation.

For example, suppose you want to load the data, clean the column names,
remove pups\_survive, and create wt\_gain.

There are a couple of options for this kind of multi-step data
manipulation:

  - define intermediate datasets (or overwrite data at each stage)
  - nest function calls

The following is an example of the first option:

``` r
# load the data
litters_data_raw = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")

# clean data names
litters_data_clean_names = janitor::clean_names(litters_data_raw)

# remove pups_survive
litters_data_selected_cols = select(litters_data_clean_names, -pups_survive)

# create wt_gain
litters_data_with_vars = 
  mutate(
    litters_data_selected_cols, 
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))

# get rid of all rows with missing value 
litters_data_with_vars_without_missing = 
  drop_na(litters_data_with_vars, wt_gain)

# show the data set
litters_data_with_vars_without_missing
```

    ## # A tibble: 31 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 28 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   wt_gain <dbl>

Below, we try the second option:

``` r
litters_data_clean = 
  drop_na(
    mutate(
      select(
        janitor::clean_names(
          read_csv("./data/FAS_litters.csv", col_types = "ccddiiii")          # load data
          ),        # clean the names
      -pups_survive
      ),      # remove pups_survive
    wt_gain = gd18_weight - gd0_weight,   # create wt_gain value
    group = str_to_lower(group) # make everything string variables their lower cases
    ),
  wt_gain     # create wt_gain variable and add this into the dataframe
  )

litters_data_clean # view the dataset
```

    ## # A tibble: 31 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 28 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   wt_gain <dbl>

These are both confusing and bad: the first gets confusing and clutters
our workspace, and the second has to be read inside out.

Piping solves this problem.

It allows you to turn the nested approach into a sequential chain by
passing the result of one function call as an argument to the next
function call:

``` r
# same thing with pipe

litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%                 # load the dataset and then
  janitor::clean_names() %>%    # clean the names and then
  select(-pups_survive) %>%     # remove pups_survive and then
  mutate(
    wt_gain = gd18_weight - gd0_weight,  # create wt_gain variable and
    group = str_to_lower(group)) %>%    # make group variables into their lower cases and then
  drop_na(wt_gain)              # remove rows with missing wt_gain values

litters_data   # veiw dataset
```

    ## # A tibble: 31 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 28 more rows, and 2 more variables: pups_dead_birth <int>,
    ## #   wt_gain <dbl>

All three approaches result in the same dataset, but the piped commands
are by far the most straightforward.

The easiest way to read %\>% is “then”; the keyboard shortcuts are Ctrl
+ Shift + M (Windows).

The functions in dplyr (and much of the tidyverse) are designed to work
smoothly with the pipe operator.

By default, the pipe will take the result of one function call and use
that as the first argument of the next function call; by design,
functions in dplyr will take a tibble as an input and return a tibble as
a result.

As a consequence, functions in dplyr are easy to connect in a data
cleaning chain.

You can make this more explicit by using . as a placeholder for the
result of the preceding call:

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>% # load dataset
  janitor::clean_names(dat = .) %>% # clean names
  select(.data = ., -pups_survive) %>% # remove pups_survive
  mutate(.data = ., # preceding dataset
    wt_gain = gd18_weight - gd0_weight, # add wt_gain variable
    group = str_to_lower(group)) %>%  # make them lower cases
  drop_na(data = ., wt_gain) # drop missing wt_gain value rows
```

In this example, the dataset argument is called dat in
janitor::clean\_names, .data in the dplyr functions, and data in
drop\_na – which is definitely confusing.

In the majority of cases (and everywhere in the tidyverse) you’ll elide
the first argument and be happy with life, but there are some cases
where the placeholder is necessary.

For example, to regress wt\_gain on pups\_born\_alive, you might use:

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%
  broom::tidy()
```

    ## # A tibble: 2 x 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3

There are limitations to the pipe.

You shouldn’t have sequences that are too long; there isn’t a great way
to deal with multiple inputs and outputs; and (since it’s not base R)
not everyone will know what %\>% means or does.

That said, compared to days when R users only had the first two options,
life is much better\!

## Learning Assessment \#4

Write a chain of commands that:

  - loads the pups data
  - cleans the variable names
  - filters the data to include only pups with sex 1
  - removes the PD ears variable
  - creates a variable that indicates whether PD pivot is 7 or more days

<!-- end list -->

``` r
pups_data = 
  read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii") %>%         # load data
  janitor::clean_names(dat = .) %>% # clean names
  filter(.data = ., sex == 1) %>%   # filter data to include only pups with sex 1
  select(.data = ., -pd_ears) %>%   # remove PD ears variable
  mutate(.data = .,
         pd_pivot_gt7 = pd_pivot >= 7)             # create a variable that indicates whether PD pivot is 7 or more days

pups_data 
```

    ## # A tibble: 155 x 6
    ##   litter_number   sex pd_eyes pd_pivot pd_walk pd_pivot_gt7
    ##   <chr>         <int>   <int>    <int>   <int> <lgl>       
    ## 1 #85               1      13        7      11 TRUE        
    ## 2 #85               1      13        7      12 TRUE        
    ## 3 #1/2/95/2         1      13        7       9 TRUE        
    ## # ... with 152 more rows
