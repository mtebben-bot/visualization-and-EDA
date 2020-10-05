Visualization
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ─────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

## Load weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: /Users/megantebbenhoff/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2020-10-05 15:37:03 (7.522)

    ## file min/max dates: 1869-01-01 / 2020-10-31

    ## using cached file: /Users/megantebbenhoff/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2020-10-05 15:37:09 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: /Users/megantebbenhoff/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2020-10-05 15:37:11 (0.88)

    ## file min/max dates: 1999-09-01 / 2020-10-31

## Scatterplots

#### First scatterplot\!

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/scatterplot1-1.png)<!-- -->

#### New approach, same plot

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/scatterplot2-1.png)<!-- --> Allows for
some data manipulation before graphing in the same step, creates
consistency between this and how we code other things in R.

#### Save and edit a plot object

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/plot%20object-1.png)<!-- --> Allows you
to make different types of graphs on the same plot.

## Advanced scatterplot

Let’s make it fancy.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/fancy%20scatterplot-1.png)<!-- --> Can
also place aes in the other geometries, but if you place it in the
ggplot function, then you will apply that aesthetic to everything that
comes later.

#### Faceting some things.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.2, size = 0.8) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/faceting-1.png)<!-- --> The facet grid
function says (rows \~ columns), so in this case, we are saying no rows,
just columns, defined by the variable name. Alpha indicates the level of
transparency, size sets the size of the points. Alpha can also be used
in ggplot and can be set by variables (example: alpha = tmin).

#### Time to combine some elements and create a new plot.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = 0.5) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/another%20plot-1.png)<!-- -->

## A little note

You can have whatever geoms you want.

There are some fun ones `geom_density2d()`, `geom_hex`.

## Univariate plots.

Start off with histograms.

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](ggplot2_pt1_files/figure-gfm/histogram-1.png)<!-- -->

Some things to do with histograms.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](ggplot2_pt1_files/figure-gfm/histogram2-1.png)<!-- --> We use fill=
rather than color= to fill in the bars of the histogram. Using position
= “dodge” means that the bars will not stack on top of each other. Could
also use facet\_grid() to see the three plots separately.

#### New geometry - geom\_density()

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](ggplot2_pt1_files/figure-gfm/geom_density-1.png)<!-- -->

#### Box plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin)) +
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

![](ggplot2_pt1_files/figure-gfm/boxplot%20baby-1.png)<!-- -->

#### Trendy plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = 0.5) +
  stat_summary(fun = "median")
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 15 rows containing non-finite values (stat_summary).

    ## Warning: Removed 3 rows containing missing values (geom_segment).

![](ggplot2_pt1_files/figure-gfm/violin-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.67

    ## Warning: Removed 15 rows containing non-finite values (stat_density_ridges).

![](ggplot2_pt1_files/figure-gfm/ridge%20plot-1.png)<!-- --> Need to
load the ggridges packages in order to use this plot.

## Save and embed

#### Let’s save a scatterplot.

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point()

ggsave("weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

#### Let’s embed

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot2_pt1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
