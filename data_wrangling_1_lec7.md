data_wrangling_1_lec7
================
2024-09-29

``` r
library(tidyverse)
options(tibble.print_min=5)
```

## pivot_longer

``` r
#read data
pulse_df = haven::read_sas("./data/public_pulse_data.sas7bdat") |>
  janitor::clean_names()

pulse_df
```

    ## # A tibble: 1,087 × 7
    ##      id   age sex   bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##   <dbl> <dbl> <chr>        <dbl>         <dbl>         <dbl>         <dbl>
    ## 1 10003  48.0 male             7             1             2             0
    ## 2 10015  72.5 male             6            NA            NA            NA
    ## 3 10022  58.5 male            14             3             8            NA
    ## 4 10026  72.7 male            20             6            18            16
    ## 5 10035  60.4 male             4             0             1             2
    ## # ℹ 1,082 more rows

create multiple records for observation times

``` r
pulse_tidy_df = pivot_longer(
  pulse_df, 
bdi_score_bl:bdi_score_12m, 
names_to="visit",
values_to="bdi")

pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##      id   age sex   visit           bdi
    ##   <dbl> <dbl> <chr> <chr>         <dbl>
    ## 1 10003  48.0 male  bdi_score_bl      7
    ## 2 10003  48.0 male  bdi_score_01m     1
    ## 3 10003  48.0 male  bdi_score_06m     2
    ## 4 10003  48.0 male  bdi_score_12m     0
    ## 5 10015  72.5 male  bdi_score_bl      6
    ## # ℹ 4,343 more rows

remove bdi_score_prefix in ‘visit’ column

``` r
pulse_tidy_df = pivot_longer(
  pulse_df,
  bdi_score_bl:bdi_score_12m, 
  names_to = "visit",
  names_prefix="bdi_score_",
  values_to="bdi")

pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##      id   age sex   visit   bdi
    ##   <dbl> <dbl> <chr> <chr> <dbl>
    ## 1 10003  48.0 male  bl        7
    ## 2 10003  48.0 male  01m       1
    ## 3 10003  48.0 male  06m       2
    ## 4 10003  48.0 male  12m       0
    ## 5 10015  72.5 male  bl        6
    ## # ℹ 4,343 more rows

don’t create intermediate datasets change bl to 00m convert ‘visit’
variable to a factor variable

``` r
pulse_df = haven::read_sas("./data/public_pulse_data.sas7bdat") |>
  janitor::clean_names() |> 
  pivot_longer(
  bdi_score_bl:bdi_score_12m, 
  names_to = "visit",
  names_prefix="bdi_score_",
  values_to="bdi") |>
  mutate(
    visit = replace(visit, visit=="bl", "00m"),
    visit = factor(visit)
  )

pulse_df
```

    ## # A tibble: 4,348 × 5
    ##      id   age sex   visit   bdi
    ##   <dbl> <dbl> <chr> <fct> <dbl>
    ## 1 10003  48.0 male  00m       7
    ## 2 10003  48.0 male  01m       1
    ## 3 10003  48.0 male  06m       2
    ## 4 10003  48.0 male  12m       0
    ## 5 10015  72.5 male  00m       6
    ## # ℹ 4,343 more rows

## pivot_wider

tidy dataset - good for further analysis

``` r
analysis_results = 
  tibble(
    group = c("treatment",
              "treatment",
              "placebo",
              "placebo"
              ),
    time = c("pre",
             "post",
             "pre",
             "post"),
    mean = c(4, 8, 3.5, 4)
      
    )

analysis_results
```

    ## # A tibble: 4 × 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

untidy data using pivot_wider() - good for human readability - opposite
of pivot_longer()

``` r
pivot_wider(analysis_results, 
            names_from="time",
            values_from="mean")
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

## binding rows
