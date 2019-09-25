Tidy Data
================
Jingyi
9/24/2019

First, update tidyverse. This only came out a week ago.

``` r
#install.packages("tidyr")

#########
# I am not building a R package, but do I still need to do so?
#########
```

## Wide to long

``` r
pulse_data = haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
pulse_data
```

    ## # A tibble: 1,087 x 7
    ##       id   age sex   bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##    <dbl> <dbl> <chr>        <dbl>         <dbl>         <dbl>         <dbl>
    ##  1 10003  48.0 male             7             1             2             0
    ##  2 10015  72.5 male             6            NA            NA            NA
    ##  3 10022  58.5 male            14             3             8            NA
    ##  4 10026  72.7 male            20             6            18            16
    ##  5 10035  60.4 male             4             0             1             2
    ##  6 10050  84.7 male             2            10            12             8
    ##  7 10078  31.3 male             4             0            NA            NA
    ##  8 10088  56.9 male             5            NA             0             2
    ##  9 10091  76.0 male             0             3             4             0
    ## 10 10092  74.2 fema~           10             2            11             6
    ## # ... with 1,077 more rows

With our new understanding of tidy data, we quickly recognize a problem:
the BDI score is spread across four columns, which correspond to four
observation times. We can fix this problem using pivot\_longer:

``` r
pulse_tidy_data = 
  pivot_longer(
    pulse_data,
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi"
  )

pulse_tidy_data
```

    ## # A tibble: 4,348 x 5
    ##       id   age sex   visit           bdi
    ##    <dbl> <dbl> <chr> <chr>         <dbl>
    ##  1 10003  48.0 male  bdi_score_bl      7
    ##  2 10003  48.0 male  bdi_score_01m     1
    ##  3 10003  48.0 male  bdi_score_06m     2
    ##  4 10003  48.0 male  bdi_score_12m     0
    ##  5 10015  72.5 male  bdi_score_bl      6
    ##  6 10015  72.5 male  bdi_score_01m    NA
    ##  7 10015  72.5 male  bdi_score_06m    NA
    ##  8 10015  72.5 male  bdi_score_12m    NA
    ##  9 10022  58.5 male  bdi_score_bl     14
    ## 10 10022  58.5 male  bdi_score_01m     3
    ## # ... with 4,338 more rows

This looks much better\! However, now visit is an issue. The original
column names were informative but we probably don’t need to keep the
bdi\_score\_ prefix in each case. I’ll use an additional option in
pivot\_longer to address this:

``` r
pulse_tidy_data = 
  pivot_longer(
    pulse_data,
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi")

pulse_tidy_data
```

    ## # A tibble: 4,348 x 5
    ##       id   age sex   visit   bdi
    ##    <dbl> <dbl> <chr> <chr> <dbl>
    ##  1 10003  48.0 male  bl        7
    ##  2 10003  48.0 male  01m       1
    ##  3 10003  48.0 male  06m       2
    ##  4 10003  48.0 male  12m       0
    ##  5 10015  72.5 male  bl        6
    ##  6 10015  72.5 male  01m      NA
    ##  7 10015  72.5 male  06m      NA
    ##  8 10015  72.5 male  12m      NA
    ##  9 10022  58.5 male  bl       14
    ## 10 10022  58.5 male  01m       3
    ## # ... with 4,338 more rows

In the preceding I’ve saved intermediate datasets to make each step
clear.

While this can be a helpful crutch as you’re trying out code, it is
generally bad practice.

There are also some additional transformations needed to wrap up the
data wrangling process, like changing bl to 00m for consistency across
visits and converting visit to a factor variable.

(It’s possible that you would want visit to be a numeric variable
instead, which could be done with a different call to mutate.)

Lastly, it’s nice to organize the data into a reasonable order.

Altogether, then, the code below will import, tidy, and transform the
PULSE dataset into a usable format:

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%   
  # read data
  janitor::clean_names() %>%   
  # clean names 
  pivot_longer(  
    # put multiple columns into a new column and put the values of them to another column
    bdi_score_bl:bdi_score_12m,  
    # put these columns
    names_to = "visit",  
    # the column name is visit for time of observations (original column names)
    names_prefix = "bdi_score_",  
    # get rid of the prefix of the "observations" of the original column names
    values_to = "bdi") %>%  
  # put the values of each orginal columns to a new column called bdi
  select(id, visit, everything()) %>%  
  # select all variables in the dataset
  mutate(
    visit = replace(visit, visit == "bl", "00m"), 
    # changing bl to 00m for consistency across visits
    visit = factor(visit, levels = str_c(c("00", "01", "06", "12"), "m"))) %>%   
  # converting visit to a factor variable
  arrange(id, visit)
  # organize the data into order as id, visit, age, sex and bdi (ascending)
  
print(pulse_data, n = 12)
```

    ## # A tibble: 4,348 x 5
    ##       id visit   age sex     bdi
    ##    <dbl> <fct> <dbl> <chr> <dbl>
    ##  1 10003 00m    48.0 male      7
    ##  2 10003 01m    48.0 male      1
    ##  3 10003 06m    48.0 male      2
    ##  4 10003 12m    48.0 male      0
    ##  5 10015 00m    72.5 male      6
    ##  6 10015 01m    72.5 male     NA
    ##  7 10015 06m    72.5 male     NA
    ##  8 10015 12m    72.5 male     NA
    ##  9 10022 00m    58.5 male     14
    ## 10 10022 01m    58.5 male      3
    ## 11 10022 06m    58.5 male      8
    ## 12 10022 12m    58.5 male     NA
    ## # ... with 4,336 more rows

``` r
# print the dataset to see the first 12 observations
```

Before moving on, let’s revisit the group variable in the litters
dataset:

``` r
read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>% 
  janitor::clean_names() %>% 
  count(group)
```

    ## # A tibble: 6 x 2
    ##   group     n
    ##   <chr> <int>
    ## 1 Con7      7
    ## 2 Con8      8
    ## 3 Low7      8
    ## 4 Low8      7
    ## 5 Mod7     12
    ## 6 Mod8      7

These data are also untidy: group encodes both dose and day of
treatment\! Time to fix that …

``` r
litters_data = 
  # load the data
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>% 
  # clean the names
  janitor::clean_names() %>% 
  # separate the group into dose and day of tx
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>% 
  # QUESTION: figure out this sep = 3
  
  mutate(
    # bring the strings in dose into their lower cases
    dose = str_to_lower(dose),
    # add a new column called wt_gain by this subtraction
    wt_gain = gd18_weight - gd0_weight) %>% 
  
  # arrange by litter number
  arrange(litter_number)
# Question: Why I can't proceed without this arrange()?
# Question solved. I forgot to take out the %>% 
  
#display the dataset
litters_data
```

    ## # A tibble: 49 x 10
    ##    dose  day_of_tx litter_number gd0_weight gd18_weight gd_of_birth
    ##    <chr> <chr>     <chr>              <dbl>       <dbl>       <int>
    ##  1 con   7         #1/2/95/2           27          42            19
    ##  2 con   7         #1/5/3/83/3-~       NA          NA            20
    ##  3 con   8         #1/6/2/2/95-2       NA          NA            20
    ##  4 mod   7         #1/82/3-2           NA          NA            19
    ##  5 low   8         #100                20          39.2          20
    ##  6 low   7         #101                23.8        42.7          20
    ##  7 low   7         #102                22.6        43.3          20
    ##  8 mod   7         #103                21.4        42.1          19
    ##  9 mod   7         #106                21.7        37.8          20
    ## 10 low   7         #107                22.6        42.4          20
    ## # ... with 39 more rows, and 4 more variables: pups_born_alive <int>,
    ## #   pups_dead_birth <int>, pups_survive <int>, wt_gain <dbl>

\#\#Leanring Assessment \#1:

In the litters data, the variables gd0\_weight and gd18\_weight give the
weight of the mother mouse on gestational days 0 and 18.

Write a data cleaning chain that retains only litter\_number and these
columns; produces new variables gd and weight; and makes gd a numeric
variable taking values 0 and 18 (for the last part, you might want to
use recode …).

Is this version “tidy”?

``` r
# in litters_data dataset
litters_data %>% 
  
  # retains only litter_number, gd0_weight and gd18_weight
  select(litter_number, gd0_weight, gd18_weight) %>%
  
  # put gd0 and gd18 into one column called gd, and their values to a column called weight
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd",
    values_to = "weight"
  ) %>% 
  
  # change the name of gd0_weight to 0 and so for 18, and put these 0 and 18's into gd column as observations
  mutate(gd = recode(gd, "gd0_weight" = 0, "gd18_weight" = 18))
```

    ## # A tibble: 98 x 3
    ##    litter_number      gd weight
    ##    <chr>           <dbl>  <dbl>
    ##  1 #1/2/95/2           0   27  
    ##  2 #1/2/95/2          18   42  
    ##  3 #1/5/3/83/3-3/2     0   NA  
    ##  4 #1/5/3/83/3-3/2    18   NA  
    ##  5 #1/6/2/2/95-2       0   NA  
    ##  6 #1/6/2/2/95-2      18   NA  
    ##  7 #1/82/3-2           0   NA  
    ##  8 #1/82/3-2          18   NA  
    ##  9 #100                0   20  
    ## 10 #100               18   39.2
    ## # ... with 88 more rows

In one sense, this is “tidy” because I have a variable for day and a
variable for weight rather that using values in my variable names.
However, it’s less useful if I’m interested in computing or analyzing
weight gain during pregnancy.

\#\#pivot\_wider

We’ve been exclusively interested in tidying data, but we’ve admitted
that sometimes untidy is better for human consumption. For that reason
we’re going to take a short digression into untidying your tidy data.

The code below creates a tidy dataset that could result from an
analysis. This is the correct format for additional analysis or
visualization, but doesn’t facilitate quick comparisons for human
readers.

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

analysis_result
```

    ## # A tibble: 4 x 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

An alternative presentation of the same data might have groups in rows,
times in columns, and mean values in table cells. This is decidedly
non-tidy; to get there from here we’ll need to use pivot\_wider, which
is the inverse of pivot\_longer:

``` r
pivot_wider(
  # dataset name
  analysis_result, 
  # put time into columns
  names_from = "time", 
  # put means into table cells
  values_from = "mean"
  # and leave groups in row
  )
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

We’re pretty much there now – in some cases you migth use select to
reorder columns, and (depending on your goal) use knitr::kable() to
produce a nicer table for reading.

## Binding rows

We’ve looked at single-table non-tidy data, but non-tidiness often stems
from relevant data spread across multiple tables.

In the simplest case, these tables are basically the same and can be
stacked to produce a tidy dataset.

That’s the setting in LotR\_words.xlsx, where the word counts for
different races and sexes in each movie in the trilogy are spread across
distinct data rectangles.

To produce the desired tidy dataset, we first need to read each table
and do some cleaning.

``` r
fellowship_ring = 
  # read data in excel and for certain range
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>% 
  # add a column named movie and put the value of that column for the data we read here as fellowship_ring
  mutate(movie = "fellowship_ring") 

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")
```

Here it was necessary to add a variable to each dataframe indicating the
movie; that information had stored elsewhere in the original
spreadsheet.

As an aside, the three code snippets above are all basically the same
except for the range and the movie name – later we’ll see a better way
to handle cases like this by writing our own functions, but this works
for now.

Once each table is ready to go, we can stack them up using bind\_rows
and tidy the result:

``` r
lotr_tidy = 
  # bind the rows of these 3 dataframes
  bind_rows(fellowship_ring, two_towers, return_king) %>%
  # clean the names
  janitor::clean_names() %>%
  # make the female and male columns as data value in a new column named sex, and put the values of original female and male columns into a new column called words
  pivot_longer(
    female:male,
    names_to = "sex", 
    values_to = "words") %>%
  #change the data in race into their lower cases
  mutate(race = str_to_lower(race)) %>% 
  # show everything in lotr_tidy
  select(movie, everything()) 
  # select(everything()) just grabs everything, but select(movie, everything()) put the movie first and then everything
  
lotr_tidy
```

    ## # A tibble: 18 x 4
    ##    movie           race   sex    words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring elf    male     971
    ##  3 fellowship_ring hobbit female    14
    ##  4 fellowship_ring hobbit male    3644
    ##  5 fellowship_ring man    female     0
    ##  6 fellowship_ring man    male    1995
    ##  7 two_towers      elf    female   331
    ##  8 two_towers      elf    male     513
    ##  9 two_towers      hobbit female     0
    ## 10 two_towers      hobbit male    2463
    ## 11 two_towers      man    female   401
    ## 12 two_towers      man    male    3589
    ## 13 return_king     elf    female   183
    ## 14 return_king     elf    male     510
    ## 15 return_king     hobbit female     2
    ## 16 return_king     hobbit male    2673
    ## 17 return_king     man    female   268
    ## 18 return_king     man    male    2459

Having the data in this form will make it easier to make comparisons
across movies, aggregate within races across the trilogy, and perform
other analyses.

\#\#Joining datasets

Data can be spread across multiple related tables, in which case it is
necessary to combine or **join** them prior to analysis. We’ll focus on
the problem of combining two tables only; combining three or more is
done step-by-step using the same ideas.

There are four major ways join dataframes x and y:

  - Inner: keeps data that appear in both x and y
  - Left: keeps data that appear in x
  - Right: keeps data that appear in y
  - Full: keeps data that appear in either x or y

Left joins are the most common, because they add data from a smaller
table y into a larger table x without removing anything from x.

As an example, consider the data tables in FAS\_pups.csv and
FAS\_litters.csv, which are related through the Litter Number variable.

The former contains data unique to each pup, and the latter contains
data unique to each litter.

We can combine these using a left join of litter data into pup data;
doing so retains data on each pup and adds data in new columns.

``` r
pup_data = 
  # read pups dataset
  read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>%
  # clean names
  janitor::clean_names() %>%
  # add column sex and rename male as 1 and female as 2
  mutate(sex = recode(sex, `1` = "male", `2` = "female")) 

litter_data = 
  # read litters dataset
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  # clean names
  janitor::clean_names() %>%
  # remove pups_survive column
  select(-pups_survive) %>%
  # add wt_gain and subtraction as its values
  # let everything in groups in lower cases
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))

# left join pups and litter, organize by "litter_number"
# this will keep everything in pups and discard NAs in pups
fas_data = 
  left_join(pup_data, litter_data, by = "litter_number")
fas_data
```

    ## # A tibble: 313 x 13
    ##    litter_number sex   pd_ears pd_eyes pd_pivot pd_walk group gd0_weight
    ##    <chr>         <chr>   <int>   <int>    <int>   <int> <chr>      <dbl>
    ##  1 #85           male        4      13        7      11 con7        19.7
    ##  2 #85           male        4      13        7      12 con7        19.7
    ##  3 #1/2/95/2     male        5      13        7       9 con7        27  
    ##  4 #1/2/95/2     male        5      13        8      10 con7        27  
    ##  5 #5/5/3/83/3-3 male        5      13        8      10 con7        26  
    ##  6 #5/5/3/83/3-3 male        5      14        6       9 con7        26  
    ##  7 #5/4/2/95/2   male       NA      14        5       9 con7        28.5
    ##  8 #4/2/95/3-3   male        4      13        6       8 con7        NA  
    ##  9 #4/2/95/3-3   male        4      13        7       9 con7        NA  
    ## 10 #2/2/95/3-2   male        4      NA        8      10 con7        NA  
    ## # ... with 303 more rows, and 5 more variables: gd18_weight <dbl>,
    ## #   gd_of_birth <int>, pups_born_alive <int>, pups_dead_birth <int>,
    ## #   wt_gain <dbl>

We made the key explicit in the join. By default, the \*\_join functions
in dplyr will try to determine the key(s) based on variable names in the
datasets you want to join.

This is often but not always sufficient, and an extra step to make the
key clear will help you and others reading your code.

Note that joining is not particularly amenable to the %\>% operator
because it is fundamentally non-linear: two separate datasets are coming
together, rather than a single dataset being processed in a step-by-step
fashion.

As a final point, the \*\_join functions are very much related to SQL
syntax, but emphasize operations common to data analysis.

\#\#Learning Assessment \#2:

The datasets in this zip file contain de-identified responses to surveys
included in past years of this course. Both contain a unique student
identifier; the first has reponses to a question about operating
systems, and the second has responses to questions about degree program
and git experience. Write a code chunk that imports and cleans both
datasets, and then joins them.

``` r
# read data
surv_os = read_csv("./data/surv_os.csv") %>% 
  # clean names
  janitor::clean_names() %>% 
  # rename each variables
  rename(id = what_is_your_uni, os = what_operating_system_do_you_use)
```

    ## Parsed with column specification:
    ## cols(
    ##   `What is your UNI?` = col_character(),
    ##   `What operating system do you use?` = col_character()
    ## )

``` r
# read data
surv_pr_git = read_csv("./data/surv_program_git.csv") %>% 
  # clean names
  janitor::clean_names() %>% 
  # rename
  rename(
    id = what_is_your_uni,
    prog = what_is_your_degree_program,
    git_exp = which_most_accurately_describes_your_experience_with_git
  )
```

    ## Parsed with column specification:
    ## cols(
    ##   `What is your UNI?` = col_character(),
    ##   `What is your degree program?` = col_character(),
    ##   `Which most accurately describes your experience with Git?` = col_character()
    ## )

``` r
left_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 175 x 4
    ##    id        os       prog  git_exp                                        
    ##    <chr>     <chr>    <chr> <chr>                                          
    ##  1 student_~ <NA>     MS    Pretty smooth: needed some work to connect Git~
    ##  2 student_~ Windows~ Other Pretty smooth: needed some work to connect Git~
    ##  3 student_~ Mac OS X MPH   Smooth: installation and connection with GitHu~
    ##  4 student_~ Windows~ MS    Smooth: installation and connection with GitHu~
    ##  5 student_~ Mac OS X MS    Smooth: installation and connection with GitHu~
    ##  6 student_~ Mac OS X MS    Smooth: installation and connection with GitHu~
    ##  7 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ##  8 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ##  9 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ## 10 student_~ Mac OS X <NA>  <NA>                                           
    ## # ... with 165 more rows

``` r
inner_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 129 x 4
    ##    id        os       prog  git_exp                                        
    ##    <chr>     <chr>    <chr> <chr>                                          
    ##  1 student_~ <NA>     MS    Pretty smooth: needed some work to connect Git~
    ##  2 student_~ Windows~ Other Pretty smooth: needed some work to connect Git~
    ##  3 student_~ Mac OS X MPH   Smooth: installation and connection with GitHu~
    ##  4 student_~ Windows~ MS    Smooth: installation and connection with GitHu~
    ##  5 student_~ Mac OS X MS    Smooth: installation and connection with GitHu~
    ##  6 student_~ Mac OS X MS    Smooth: installation and connection with GitHu~
    ##  7 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ##  8 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ##  9 student_~ Windows~ MPH   Pretty smooth: needed some work to connect Git~
    ## 10 student_~ Windows~ MPH   Smooth: installation and connection with GitHu~
    ## # ... with 119 more rows

``` r
anti_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 46 x 2
    ##    id          os                                     
    ##    <chr>       <chr>                                  
    ##  1 student_86  Mac OS X                               
    ##  2 student_91  Windows 10                             
    ##  3 student_24  Mac OS X                               
    ##  4 student_103 Mac OS X                               
    ##  5 student_163 Mac OS X                               
    ##  6 student_68  Other (Linux, Windows, 95, TI-89+, etc)
    ##  7 student_158 Mac OS X                               
    ##  8 student_19  Windows 10                             
    ##  9 student_43  Mac OS X                               
    ## 10 student_78  Mac OS X                               
    ## # ... with 36 more rows

``` r
anti_join(surv_pr_git, surv_os)
```

    ## Joining, by = "id"

    ## # A tibble: 15 x 3
    ##    id        prog  git_exp                                                 
    ##    <chr>     <chr> <chr>                                                   
    ##  1 <NA>      MPH   Pretty smooth: needed some work to connect Git, GitHub,~
    ##  2 student_~ PhD   Pretty smooth: needed some work to connect Git, GitHub,~
    ##  3 <NA>      MPH   Pretty smooth: needed some work to connect Git, GitHub,~
    ##  4 <NA>      MPH   Pretty smooth: needed some work to connect Git, GitHub,~
    ##  5 <NA>      MS    Pretty smooth: needed some work to connect Git, GitHub,~
    ##  6 student_~ MS    Pretty smooth: needed some work to connect Git, GitHub,~
    ##  7 <NA>      MS    Smooth: installation and connection with GitHub was easy
    ##  8 student_~ PhD   Pretty smooth: needed some work to connect Git, GitHub,~
    ##  9 student_~ MPH   Smooth: installation and connection with GitHub was easy
    ## 10 student_~ MS    Smooth: installation and connection with GitHub was easy
    ## 11 <NA>      MS    Pretty smooth: needed some work to connect Git, GitHub,~
    ## 12 <NA>      MS    "What's \"Git\" ...?"                                   
    ## 13 <NA>      MS    Smooth: installation and connection with GitHub was easy
    ## 14 <NA>      MPH   Pretty smooth: needed some work to connect Git, GitHub,~
    ## 15 <NA>      MS    Pretty smooth: needed some work to connect Git, GitHub,~

\#\#A quick note on names:

Up until very recently, folks were using gather and spread instead of
pivot\_longer and pivot\_wider. The new functions were updated for good
reasons; gather and spread will still exist, but they’re going to be
less common over time.
