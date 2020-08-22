
## Motivation

Below are the notes I have gathered on David Robinson's screencasts, with tips and tricks I use for my own `R` peregrinations. Hopefully, these notes will be useful to others.

It took me some time to fully transition from base `R` to the `Tidyverse`. I really clicked when I started watching screencasts by [David Robinson](http://varianceexplained.org/) on his [Youtube channel](https://www.youtube.com/user/safe4democracy/featured). 

Each week, he goes live for an hour or so, and does exploratory analyses in `R` with data he has never seen before (!). The data come from the [#TidyTuesday project](https://github.com/rfordatascience/tidytuesday) brought to us by the [R for Data Science Online Learning Community](https://www.rfordatasci.com/). This is an awesome initiative, check out the [beautiful data visualisations on Twitter](https://twitter.com/hashtag/tidytuesday). 

David Robinson shares [the #RStats code he writes during his screencasts](https://github.com/dgrtwo/data-screencasts), and provides [annotations](https://github.com/dgrtwo/data-screencasts/tree/master/screencast-annotations) for us to grasp at once what the screencast is about, and what the main steps of the analyses are (including the `R` functions he used).     

Check out David Robinson's talk [Ten Tremendous Tricks for Tidyverse](https://www.youtube.com/watch?v=NDHSBUN_rVU). You might also be interested in Emily Robinson's talk [The Lesser Known Stars of the Tidyverse](https://www.youtube.com/watch?v=ax4LXQ5t38k)

Let's get to it. 

## Setting the scene

Load the tidyverse packages

```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
## ✓ tibble  3.0.3     ✓ dplyr   1.0.1
## ✓ tidyr   1.1.1     ✓ stringr 1.4.0
## ✓ readr   1.3.1     ✓ forcats 0.5.0
```

```
## ── Conflicts ────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

Set [the theme](https://ggplot2.tidyverse.org/reference/ggtheme.html) you like for data visualisation with `ggplot2`

```r
theme_set(theme_light())
```

For illustration, we use the `starwars` dataset that comes with `tidyverse`

```r
data("starwars")
starwars
```

```
## # A tibble: 87 x 14
##    name  height  mass hair_color skin_color eye_color birth_year sex   gender
##    <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
##  1 Luke…    172    77 blond      fair       blue            19   male  mascu…
##  2 C-3PO    167    75 <NA>       gold       yellow         112   none  mascu…
##  3 R2-D2     96    32 <NA>       white, bl… red             33   none  mascu…
##  4 Dart…    202   136 none       white      yellow          41.9 male  mascu…
##  5 Leia…    150    49 brown      light      brown           19   fema… femin…
##  6 Owen…    178   120 brown, gr… light      blue            52   male  mascu…
##  7 Beru…    165    75 brown      light      blue            47   fema… femin…
##  8 R5-D4     97    32 <NA>       white, red red             NA   none  mascu…
##  9 Bigg…    183    84 black      light      brown           24   male  mascu…
## 10 Obi-…    182    77 auburn, w… fair       blue-gray       57   male  mascu…
## # … with 77 more rows, and 5 more variables: homeworld <chr>, species <chr>,
## #   films <list>, vehicles <list>, starships <list>
```

## Count, count, aaaand count


```r
starwars %>% 
  count(name, sort = TRUE)
```

```
## # A tibble: 87 x 2
##    name                    n
##    <chr>               <int>
##  1 Ackbar                  1
##  2 Adi Gallia              1
##  3 Anakin Skywalker        1
##  4 Arvel Crynyd            1
##  5 Ayla Secura             1
##  6 Bail Prestor Organa     1
##  7 Barriss Offee           1
##  8 BB8                     1
##  9 Ben Quadinaros          1
## 10 Beru Whitesun lars      1
## # … with 77 more rows
```

```r
starwars %>% 
  count(hair_color, sort = TRUE)
```

```
## # A tibble: 13 x 2
##    hair_color        n
##    <chr>         <int>
##  1 none             37
##  2 brown            18
##  3 black            13
##  4 <NA>              5
##  5 white             4
##  6 blond             3
##  7 auburn            1
##  8 auburn, grey      1
##  9 auburn, white     1
## 10 blonde            1
## 11 brown, grey       1
## 12 grey              1
## 13 unknown           1
```

```r
starwars %>% 
  filter(!is.na(hair_color)) %>%
  count(hair_color, sort = TRUE)
```

```
## # A tibble: 12 x 2
##    hair_color        n
##    <chr>         <int>
##  1 none             37
##  2 brown            18
##  3 black            13
##  4 white             4
##  5 blond             3
##  6 auburn            1
##  7 auburn, grey      1
##  8 auburn, white     1
##  9 blonde            1
## 10 brown, grey       1
## 11 grey              1
## 12 unknown           1
```

```r
starwars %>% 
  count(gender, sort = TRUE)
```

```
## # A tibble: 3 x 2
##   gender        n
##   <chr>     <int>
## 1 masculine    66
## 2 feminine     17
## 3 <NA>          4
```

```r
starwars %>% 
  count(species, sort = TRUE)
```

```
## # A tibble: 38 x 2
##    species      n
##    <chr>    <int>
##  1 Human       35
##  2 Droid        6
##  3 <NA>         4
##  4 Gungan       3
##  5 Kaminoan     2
##  6 Mirialan     2
##  7 Twi'lek      2
##  8 Wookiee      2
##  9 Zabrak       2
## 10 Aleena       1
## # … with 28 more rows
```

```r
starwars %>% 
  count(homeworld, sort = TRUE)
```

```
## # A tibble: 49 x 2
##    homeworld     n
##    <chr>     <int>
##  1 Naboo        11
##  2 Tatooine     10
##  3 <NA>         10
##  4 Alderaan      3
##  5 Coruscant     3
##  6 Kamino        3
##  7 Corellia      2
##  8 Kashyyyk      2
##  9 Mirial        2
## 10 Ryloth        2
## # … with 39 more rows
```






filter != other

fct_reorder_within dans tidytext

?package_name:: then use autocompletion to explore the functions of the package_name package

rvest avec sa petite app pour recuperer html depuis wikipedia entre autres

The decade trick is one of his bests.

Use of View() a lot

Count a lot

themeset(blabla)


