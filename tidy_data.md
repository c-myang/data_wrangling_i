Tidy Data
================
2022-09-27

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

## `pivot_longer`

Load the PULSE data

``` r
pulse_data = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

Wide format to long format…

``` r
pulse_data_tidy = pulse_data %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to = "visit", #Names of the bdi variable goes under "visit" variable
    names_prefix = "bdi_score_", #Remove prefix 
    values_to = "bdi" #Values of the bdi scores go under "bdi" variable
  )
```

Now let’s put all of the data cleaning steps into a single chunk.
Rewrite, combine, and extend (to add a mutate step).

``` r
pulse_data = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to = "visit", #Names of the bdi variable goes under "visit" variable
    names_prefix = "bdi_score_", #Remove prefix 
    values_to = "bdi" #Values of the bdi scores go under "bdi" variable
  ) %>% 
  relocate(id, visit) %>% # Move ID and visit columns to the front
  mutate(visit = recode(visit, "bl" = "00m")) # Update 'bl' to '00m' in the dataset
```

## `pivot_wider`

Make up some data!

``` r
analysis_result = 
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )

analysis_result %>% 
  pivot_wider(
    names_from = "time", 
    values_from = "mean"
  )
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4