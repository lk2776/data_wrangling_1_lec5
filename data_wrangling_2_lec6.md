data_wrangling_2_lec6
================
2024-09-29

### load packages

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

### read data

``` r
options(tibble.print_min = 3)
litters_df = read_csv("./data/FAS_litters.csv",
                      na=c("NA","."))
```

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df = janitor::clean_names(litters_df)
head(litters_df)
```

    ## # A tibble: 6 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 4 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ## 5 Con7  #4/2/95/3-3         NA          NA            20               6
    ## 6 Con7  #2/2/95/3-2         NA          NA            20               6
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

### select, select helper functions, rename, relocate

``` r
#specify names of columns to keep 
select(litters_df, 
       group, 
       litter_number,
       gd0_weight,
       pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <dbl>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ℹ 46 more rows

``` r
#specify a range of columns to keep 
select(litters_df, 
       group:gd_of_birth)
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ℹ 46 more rows

``` r
#specify names of columns to remove
select(litters_df, 
       -pups_survive,
       -gd0_weight)
```

    ## # A tibble: 49 × 6
    ##   group litter_number gd18_weight gd_of_birth pups_born_alive pups_dead_birth
    ##   <chr> <chr>               <dbl>       <dbl>           <dbl>           <dbl>
    ## 1 Con7  #85                  34.7          20               3               4
    ## 2 Con7  #1/2/95/2            42            19               8               0
    ## 3 Con7  #5/5/3/83/3-3        41.4          19               6               0
    ## # ℹ 46 more rows

``` r
#rename 'selected' names of columns 
select(litters_df, 
       GROUP=group, 
       LiTter_NuMbEr=litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTter_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # ℹ 46 more rows

``` r
#rename only - not 'selecting' 
rename(litters_df, 
       GROUP=group, 
       LiTtER_NuMbEr=litter_number)
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtER_NuMbEr gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
#select helper functions : starts_with()
select(litters_df, 
       starts_with("gd"))
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <dbl>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # ℹ 46 more rows

``` r
select(litters_df, 
       ends_with("weight")
       )
```

    ## # A tibble: 49 × 2
    ##   gd0_weight gd18_weight
    ##        <dbl>       <dbl>
    ## 1       19.7        34.7
    ## 2       27          42  
    ## 3       26          41.4
    ## # ℹ 46 more rows

``` r
select(litters_df, 
       contains("pups"))
```

    ## # A tibble: 49 × 3
    ##   pups_born_alive pups_dead_birth pups_survive
    ##             <dbl>           <dbl>        <dbl>
    ## 1               3               4            3
    ## 2               8               0            7
    ## 3               6               0            5
    ## # ℹ 46 more rows

``` r
#everything(): reorganize columns without discarding any columns
select(litters_df, 
       litter_number, 
       pups_survive, 
       everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_born_alive <dbl>, pups_dead_birth <dbl>

``` r
#relocate - similar everything() select helper function 
relocate(litters_df, 
         litter_number,
         pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_born_alive <dbl>, pups_dead_birth <dbl>

``` r
#pull a single variable 
#'vector' format
pull(litters_df, 
     group)
```

    ##  [1] "Con7" "Con7" "Con7" "Con7" "Con7" "Con7" "Con7" "Con8" "Con8" "Con8"
    ## [11] "Con8" "Con8" "Con8" "Con8" "Con8" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7"
    ## [21] "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Low7" "Low7" "Low7"
    ## [31] "Low7" "Low7" "Low7" "Low7" "Low7" "Mod8" "Mod8" "Mod8" "Mod8" "Mod8"
    ## [41] "Mod8" "Mod8" "Low8" "Low8" "Low8" "Low8" "Low8" "Low8" "Low8"

``` r
#outputs one column - in 'dataframe' format
select(litters_df, 
       group)
```

    ## # A tibble: 49 × 1
    ##   group
    ##   <chr>
    ## 1 Con7 
    ## 2 Con7 
    ## 3 Con7 
    ## # ℹ 46 more rows

la:

``` r
options(tibble.print_min = 3)
pups_df = read_csv("./data/FAS_pups.csv",
                      na=c("NA","."))
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df = janitor::clean_names(pups_df)
colnames(pups_df)
```

    ## [1] "litter_number" "sex"           "pd_ears"       "pd_eyes"      
    ## [5] "pd_pivot"      "pd_walk"

``` r
select(pups_df, 
       litter_number, 
       sex, 
       pd_ears)
```

    ## # A tibble: 313 × 3
    ##   litter_number   sex pd_ears
    ##   <chr>         <dbl>   <dbl>
    ## 1 #85               1       4
    ## 2 #85               1       4
    ## 3 #1/2/95/2         1       5
    ## # ℹ 310 more rows

## filter rows using logical expressions using filter()

comparison operators: \>, \>=, \<, \<=, ==, != presence or absence of
values: %in% find missing values: is.na() logical operators: &, \|, !

``` r
#drop all rows with missing values
drop_na(litters_df)
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 28 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
#drop rows for which wt_increase is missing

drop_na(litters_df, 
        gd0_weight)
```

    ## # A tibble: 34 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 31 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

la:

``` r
pups_df = read_csv("./data/FAS_pups.csv",
                      na=c("NA","."))
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df = janitor::clean_names(pups_df)
colnames(pups_df)
```

    ## [1] "litter_number" "sex"           "pd_ears"       "pd_eyes"      
    ## [5] "pd_pivot"      "pd_walk"

``` r
filter(pups_df, 
       sex==1)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # ℹ 152 more rows

``` r
filter(pups_df, 
       pd_walk<11, 
       sex==2)
```

    ## # A tibble: 127 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # ℹ 124 more rows

## mutate

change variables create new variales

``` r
mutate(litters_df, 
       wt_gain = gd18_weight - gd0_weight, #create new variable 
       group = str_to_lower(group)) #update variable
```

    ## # A tibble: 49 × 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 3 more variables: pups_dead_birth <dbl>, pups_survive <dbl>, wt_gain <dbl>

la:

``` r
mutate(pups_df, 
       pd_pivot_new = pd_pivot-7,
       pd_sum = pd_ears+pd_eyes+pd_pivot+pd_walk)
```

    ## # A tibble: 313 × 8
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pd_pivot_new pd_sum
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>        <dbl>  <dbl>
    ## 1 #85               1       4      13        7      11            0     35
    ## 2 #85               1       4      13        7      12            0     36
    ## 3 #1/2/95/2         1       5      13        7       9            0     34
    ## # ℹ 310 more rows
