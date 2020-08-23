## Motivation

Below are the notes I have taken on David Robinson's screencasts, with tips and tricks I use for my own `R` peregrinations. Hopefully, these notes will be useful to others.

It took me some time to fully transition from base `R` to the `Tidyverse`. I really clicked when I started watching screencasts by [David Robinson](http://varianceexplained.org/){:target="_blank" rel="noopener"} on his [Youtube channel](https://www.youtube.com/user/safe4democracy/featured){:target="_blank" rel="noopener"}. 

Each week, he goes live for an hour or so, and does exploratory analyses in `R` with data he has never seen before (!). The data come from the [#TidyTuesday project](https://github.com/rfordatascience/tidytuesday){:target="_blank" rel="noopener"} brought to us by the [R for Data Science Online Learning Community](https://www.rfordatasci.com/){:target="_blank" rel="noopener"}. This is an awesome initiative, check out the [beautiful data visualisations](https://twitter.com/hashtag/tidytuesday){:target="_blank" rel="noopener"} on Twitter. 

David Robinson shares [the #RStats code](https://github.com/dgrtwo/data-screencasts){:target="_blank" rel="noopener"} he writes during his screencasts, and provides [annotations](https://github.com/dgrtwo/data-screencasts/tree/master/screencast-annotations){:target="_blank" rel="noopener"} for us to grasp at once what the screencast is about, and what the main steps of the analyses are (including the `R` functions he used).     

Check out David Robinson's talk [Ten Tremendous Tricks for Tidyverse](https://www.youtube.com/watch?v=NDHSBUN_rVU){:target="_blank" rel="noopener"}. You might also be interested in Emily Robinson's talk [The Lesser Known Stars of the Tidyverse](https://www.youtube.com/watch?v=ax4LXQ5t38k){:target="_blank" rel="noopener"}.

Let's get to it. 

## Setting the scene

Load the tidyverse suite of packages:

```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
## ✓ tibble  3.0.3     ✓ dplyr   1.0.1
## ✓ tidyr   1.1.1     ✓ stringr 1.4.0
## ✓ readr   1.3.1     ✓ forcats 0.5.0
```

```
## ── Conflicts ────────────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

Set [the theme](https://ggplot2.tidyverse.org/reference/ggtheme.html){:target="_blank" rel="noopener"} you like for data visualisation with `ggplot2`:

```r
theme_set(theme_light())
```

For illustration, I will use the `starwars` dataset that comes with `tidyverse`:

```r
data("starwars")
starwars_raw <- starwars
```

If you need to explore the functions of a package, a trick is to use the autocompletion in `RStudio`. Just type in `?package_name::` and you should see the functions of the `package_name` package in a pull-down menu. 

## Inspect the data

Use the viewer to inspect your dataset:

```r
starwars_raw %>%
  View()
```

The tibble format is great to double check the format of your columns:

```r
starwars_raw
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

`chr`, `int`, `dbl`, `fct` and `list` are for characters, integers, doubles, factors and lists respectively. 

We are gonna use only a few columns of the original dataset:

```r
starwars <- starwars_raw %>%
  select(name, gender, species, mass)
```


## Count, count, aaaand count

To get a sense of the data, count stuff and use `sort = TRUE` to arrange things by decreasing counts:

```r
starwars %>% 
  count(name, sort = TRUE) # does a character appear only once
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
  count(gender, sort = TRUE) # 
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
  filter(!is.na(gender)) %>% # filter out missing values
  count(gender, sort = TRUE)
```

```
## # A tibble: 2 x 2
##   gender        n
##   <chr>     <int>
## 1 masculine    66
## 2 feminine     17
```

```r
starwars %>% 
  count(species, gender, sort = TRUE) # 
```

```
## # A tibble: 42 x 3
##    species  gender        n
##    <chr>    <chr>     <int>
##  1 Human    masculine    26
##  2 Human    feminine      9
##  3 Droid    masculine     5
##  4 <NA>     <NA>          4
##  5 Gungan   masculine     3
##  6 Mirialan feminine      2
##  7 Wookiee  masculine     2
##  8 Zabrak   masculine     2
##  9 Aleena   masculine     1
## 10 Besalisk masculine     1
## # … with 32 more rows
```

You may want to use the `wt` argument to get a count weighted by another variable. Compare the call to `count()` with and without the `wt` argument in the example below. When `wt = mass` is used, we compute `sum(mass)` for each species, otherwise we compute the number of rows in each species:

```r
starwars %>%
  count(species, wt = mass)
```

```
## # A tibble: 38 x 2
##    species       n
##    <chr>     <dbl>
##  1 Aleena       15
##  2 Besalisk    102
##  3 Cerean       82
##  4 Chagrian      0
##  5 Clawdite     55
##  6 Droid       279
##  7 Dug          40
##  8 Ewok         20
##  9 Geonosian    80
## 10 Gungan      148
## # … with 28 more rows
```

```r
starwars %>%
  count(species)
```

```
## # A tibble: 38 x 2
##    species       n
##    <chr>     <int>
##  1 Aleena        1
##  2 Besalisk      1
##  3 Cerean        1
##  4 Chagrian      1
##  5 Clawdite      1
##  6 Droid         6
##  7 Dug           1
##  8 Ewok          1
##  9 Geonosian     1
## 10 Gungan        3
## # … with 28 more rows
```

You can add the counts to your tibble by using `add_count()`, check out the `n` column:

```r
starwars %>%
  add_count(species, wt = mass)
```

```
## # A tibble: 87 x 5
##    name               gender    species  mass     n
##    <chr>              <chr>     <chr>   <dbl> <dbl>
##  1 Luke Skywalker     masculine Human      77 1821.
##  2 C-3PO              masculine Droid      75  279 
##  3 R2-D2              masculine Droid      32  279 
##  4 Darth Vader        masculine Human     136 1821.
##  5 Leia Organa        feminine  Human      49 1821.
##  6 Owen Lars          masculine Human     120 1821.
##  7 Beru Whitesun lars feminine  Human      75 1821.
##  8 R5-D4              masculine Droid      32  279 
##  9 Biggs Darklighter  masculine Human      84 1821.
## 10 Obi-Wan Kenobi     masculine Human      77 1821.
## # … with 77 more rows
```

When you count by two or more variables, it may happen that you don't have all combinations. No worries, you're covered with `complete()`. Compare the call to `complete()` without and with in the example below. The Aleena species has no feminine representative, but this info is implicit. When `complete()` is used, a row is added with a `NA` for feminine gender: 

```r
starwars %>% 
  filter(species %in% c('Aleena','Droid')) %>%
  count(species, gender)
```

```
## # A tibble: 3 x 3
##   species gender        n
##   <chr>   <chr>     <int>
## 1 Aleena  masculine     1
## 2 Droid   feminine      1
## 3 Droid   masculine     5
```

```r
starwars %>% 
  filter(species %in% c('Aleena','Droid')) %>%
  count(species, gender) %>%
  complete(species, gender)
```

```
## # A tibble: 4 x 3
##   species gender        n
##   <chr>   <chr>     <int>
## 1 Aleena  feminine     NA
## 2 Aleena  masculine     1
## 3 Droid   feminine      1
## 4 Droid   masculine     5
```




<!--   filter(originDate >= "1960-01-01") %>% -->
<!--   count(acquisition, -->
<!--         decade = 5 * (year(originDate) %/% 5)) %>% -->
<!--   complete(acquisition, decade, fill = list(n = 0)) -->


<!-- animal_outcomes %>% -->
<!--   complete(animal_type, outcome, year, fill = list(total = 0)) %>% -->
<!--   mutate(outcome = fct_reorder(outcome, total, sum), -->
<!--          animal_type = fct_reorder(animal_type, -total, sum)) %>% -->
<!--   ggplot(aes(year, total, color = outcome)) + -->
<!--   geom_line() + -->
<!--   facet_wrap(~ animal_type, scales = "free_y") + -->
<!--   scale_y_continuous(labels = comma) + -->
<!--   labs(x = "Year", -->
<!--        y = "# of animals with this outcome", -->
<!--        color = "Outcome") -->



<!-- mutate(position = fct_lump(position, 15)) %>% -->

<!-- milk_products_tidied %>% -->
<!--   group_by(product = fct_lump(product, 6, w = lbs_per_person), -->
<!--            year) %>% -->
<!--   summarize(lbs_per_person = sum(lbs_per_person)) %>% -->
<!--   ggplot(aes(year, lbs_per_person, color = product)) + -->
<!--   geom_line() -->


<!-- filter != other -->

<!-- fct_reorder_within dans tidytext -->

<!-- by_continent_threat %>% -->
<!--   mutate(threat_type = reorder_within(threat_type, n, continent)) %>% -->
<!--   ggplot(aes(n, threat_type)) + -->
<!--   geom_col() + -->
<!--   scale_y_reordered() + -->
<!--   facet_wrap(~ continent, scales = "free") + -->
<!--   labs(x = "# of plants facing this threat", -->
<!--        y = "", -->
<!--        fill = "Continent", -->
<!--        title = "What are the most common threats to plants by continent?") -->


<!-- rvest avec sa petite app pour recuperer html depuis wikipedia entre autres -->

<!-- The decade trick is one of his bests. -->

<!-- fct_reorder and coord_flip -->
<!-- by_minor_category_2016 %>% -->
<!--   mutate(minor_category = fct_reorder(minor_category, wage_percent_of_male)) %>% -->
<!--   ggplot(aes(minor_category, wage_percent_of_male, fill = major_category)) + -->
<!--   geom_col() + -->
<!--   coord_flip() -->

<!-- milk_products_tidied %>% -->
<!--   filter(year == max(year)) %>% -->
<!--   mutate(product = fct_reorder(product, lbs_per_person, sum)) %>% -->
<!--   ggplot(aes(product, lbs_per_person, fill = category)) + -->
<!--   geom_col() + -->
<!--   coord_flip() + -->
<!--   labs(x = "", -->
<!--        y = "Pounds consumed per US person in 2017") -->

<!-- coffee_lumped %>% -->
<!--   mutate(variety = fct_reorder(variety, total_cup_points)) %>% -->
<!--   ggplot(aes(total_cup_points, variety)) + -->
<!--   geom_boxplot() -->

<!-- mutate(word = reorder(word, n)) %>% -->



<!-- scale_x_log10 -->

<!-- wine_ratings %>% -->
<!--   ggplot(aes(price)) + -->
<!--   geom_histogram() + -->
<!--   scale_x_log10() -->


<!-- learn regex to be used w/ str_remove etc -->

<!-- mutate(type = str_remove(type, "\\d+")) -->

<!-- ggmap -->
<!-- them_map -->

<!-- big fan of gganimate, rvest, gggraph (library(ggraph), library(igraph)), broom as well. Aime bien aussi packages countrycode et WDI. Et glmnet aussi.  -->


<!-- utilisation de accross -->

<!-- xmen <- tidytuesdayR::tt_load('2020-06-30') -->
<!-- character_visualization <- xmen$character_visualization %>% -->
<!--   separate(character, c("superhero", "secret_identity"), sep = " = ", fill = "right") -->
<!-- by_character <- character_visualization %>% -->
<!--   group_by(superhero) %>% -->
<!--   summarize(across(speech:depicted, -->
<!--                    list(total = sum, -->
<!--                         issues = ~ sum(. > 0), -->
<!--                         avg = ~ mean(.[depicted > 0])))) -->

<!-- coffee_ratings %>% -->
<!--   summarize(across(everything(), ~ mean(!is.na(.)))) %>% -->
<!--   gather() %>% -->
<!--   View() -->


<!-- replace gather and spread by pivot_longer et pivot_wider -->



<!-- loliplot -->

<!-- costume_ratios %>% -->
<!--   mutate(superhero = fct_reorder(superhero, costume_ratio)) %>% -->
<!--   ggplot(aes(costume_ratio, y = superhero)) + -->
<!--   geom_errorbarh(aes(xmin = 1, xmax = costume_ratio), height = 0) + -->
<!--   geom_point(aes(size = speech_total, color = costume_ratio > 1)) + -->
<!--   scale_x_log10() + -->
<!--   scale_color_discrete(guide = FALSE) + -->
<!--   labs(size ="# of lines", -->
<!--        x = "Lines in costume / lines out of costume", -->
<!--        y = "", -->
<!--        title = "Which X-Men tend to speak in costume/out of costume?") -->



<!-- group_by fct_lump -->
<!-- xmen$comic_bechdel %>% -->
<!--   bind_rows(xmen$xmen_bechdel %>% mutate(writer = "Chris Claremont")) %>% -->
<!--   filter(!is.na(pass_bechdel), -->
<!--          !is.na(writer)) %>% -->
<!--   group_by(writer = fct_lump(writer, 5)) %>% -->



<!-- paquets de trucs sur X-axis, scales -->
<!-- xmen$xmen_bechdel %>% -->
<!--   filter(!is.na(pass_bechdel)) %>% -->
<!--   group_by(issue_group = 20 * (issue %/% 20)) %>% -->
<!--   summarize(pct_bechdel = mean(pass_bechdel == "yes"), -->
<!--             n = n()) %>% -->
<!--   ggplot(aes(issue_group, pct_bechdel)) + -->
<!--   geom_line() + -->
<!--   scale_y_continuous(labels = scales::percent) -->

<!-- by_decade <- tdf_winners %>% -->
<!--   group_by(decade = 10 * (year %/% 10)) %>% -->
<!--   summarize(winner_age = mean(age), -->
<!--             winner_height = mean(height, na.rm = TRUE), -->
<!--             winner_weight = mean(weight, na.rm = TRUE), -->
<!--             winner_margin = mean(time_margin, na.rm = TRUE), -->
<!--             winner_speed = mean(speed, na.rm = TRUE)) -->



<!-- filter les others -->
<!-- by_issue_character %>% -->
<!--   filter(fct_lump(superhero, 9, w = speech) != "Other") %>% -->
<!--   mutate(superhero = fct_reorder(superhero, -speech, sum)) %>% -->
<!--   ggplot(aes(issue_group, speech)) + -->
<!--   geom_col() + -->
<!--   facet_wrap(~ superhero) -->


<!-- when first read, use data_raw, then cleaning in data -->

<!-- always forget about the midpoint -->

<!-- ggplot(joined_trees, aes(fill = percent_maple)) + -->
<!--   geom_sf() + -->
<!--   scale_fill_gradient2(low = "brown", -->
<!--                        high = "darkgreen", -->
<!--                        midpoint = .1, -->
<!--                        labels = scales::percent) + -->
<!--   theme_void() + -->
<!--   coord_sf(datum = NA) + -->
<!--   labs(fill = "% trees that are maple", -->
<!--        title = "Where are the maple trees in NYC?", -->
<!--        subtitle = "Based on a 2015 survey of 600,000 trees") -->

<!-- janitor::clean_names() -->
<!-- lubridate::year() -->

<!-- scale_y_continuous(labels = scales::percent_format()) + -->
<!-- scale_x_continuous(labels = scales::comma()) + -->



<!-- parse_number -->

<!-- thanksgiving_survey_raw <- tidytuesdayR::tt_load('2018-11-20')  -->

<!-- thanksgiving_survey <- thanksgiving_survey_raw$thanksgiving_meals %>% -->
<!--   mutate(family_income = fct_reorder(family_income, parse_number(family_income))) -->

<!-- thanksgiving_survey_raw$family_income -->
<!-- parse_number(thanksgiving_survey_raw$family_income) -->




<!-- simpsons %>% -->


<!--   separate_rows(role, sep = ";\\s+") %>% -->
<!--   add_count(role) %>% -->
<!--   filter(n >= 8) %>% -->
<!--   count(season, role) %>% -->
<!--   mutate(role = fct_reorder(role, -n, sum)) %>% -->
<!--   ggplot(aes(season, n)) + -->
<!--   geom_col() + -->
<!--   facet_wrap(~ role) -->





<!-- crossing function, cf riddlers -->



<!-- ## pipe a (g)lm  -->

<!-- by_hectare %>% -->
<!--   mutate(n_gray = round(pct_gray * n)) %>% -->
<!--   glm(cbind(n_gray, n - n_gray) ~ lat, data = ., family = "binomial") %>% -->
<!--   summary() -->

<!-- horror_movies %>% -->
<!--   filter(!is.na(movie_rating)) %>% -->
<!--   mutate(movie_rating = fct_lump(movie_rating, 5)) %>% -->
<!--   lm(review_rating ~ movie_rating, data = .) %>% -->
<!--   anova() -->

<!-- ## barre d'erreur -->

<!-- geom_errorbarh() -->
<!-- geom_errorbar -->

<!-- n.distinct() -->
<!-- distinct -->

<!-- nobel_winners %>% -->
<!--   group_by(category, decade) %>% -->
<!--   summarize(winners = n(), -->
<!--             winners_per_year = winners / n_distinct(prize_year)) %>% -->
<!--   ggplot(aes(decade, winners_per_year, color = category)) + -->
<!--   geom_line() + -->
<!--   expand_limits(y = 0) -->

<!-- nobel_winners %>% -->
<!--   distinct(full_name, prize_year, category) %>% -->
<!--   group_by(full_name) %>% -->
<!--   mutate(prizes = n(), -->
<!--          distinct_prizes = n_distinct(category)) %>% -->
<!--   arrange(desc(prizes), full_name) -->


<!-- ## heatmap -->

<!-- geom_tile -->

<!-- november_2018 %>% -->
<!-- #  mutate(arrival_station = fct_infreq(fct_lump(arrival_station, prop = .01))) %>% -->
<!-- #  mutate(departure_station = fct_infreq(fct_lump(departure_station, prop = .01))) %>% -->
<!--   mutate(arrival_station = fct_reorder(fct_lump(arrival_station, prop = .01), pct_late_at_departure)) %>% -->
<!--   mutate(departure_station = fct_reorder(fct_lump(departure_station, prop = .01), pct_late_at_departure)) %>% -->
<!--   group_by(arrival_station, departure_station) %>% -->
<!--   summarize(pct_late_at_departure = sum(num_late_at_departure) / sum(total_num_trips)) %>% -->
<!--   ggplot(aes(arrival_station, departure_station, fill = pct_late_at_departure)) + -->
<!--   geom_tile() + -->
<!--   scale_fill_gradient2(low = "blue", high = "red", midpoint = .25, labels = percent_format()) + -->
<!--   theme(axis.text.x = element_text(angle = 90, hjust = 1)) + -->
<!--   labs(x = "Arrival station", -->
<!--        y = "Departure station", -->
<!--        fill = "% late at departure", -->
<!--        title = "Which routes have the most delayed trains in November 2018?", -->
<!--        subtitle = "Stations with only one arriving/departing route were lumped into 'Other'") -->

<!-- ## serparate rows -->

<!-- ```{r eval = FALSE} -->
<!-- horror_movies %>% -->
<!--   separate_rows(genres, sep = "\\| ") %>% -->
<!--   mutate(genre = fct_lump(genres, 5)) %>% -->
<!--   ggplot(aes(genre, review_rating)) + -->
<!--   geom_boxplot() -->
<!-- ``` -->

<!-- ## create var in count -->

<!-- gdpr_violations %>% -->
<!--   count(country = fct_lump(country, 8, w = price), -->
<!--         sort = TRUE, wt = price, name = "total_price") %>% -->
<!--   mutate(country = fct_reorder(country, total_price)) %>% -->
<!--   ggplot(aes(total_price, country)) + -->
<!--   geom_col() + -->
<!--   scale_x_continuous(labels = dollar_format()) -->

<!-- ## PCA too  -->

<!-- perso i prefer going for FactoMineR and factoextra -->


<!-- ## tidymetrics -->


<!-- # devtools::install_github("ramnathv/tidymetrics") -->
<!-- library(tidymetrics) -->

<!-- brewing_summarized <- brewing_materials %>% -->
<!--   rename(material = type) %>% -->
<!--   filter(!str_detect(material_type, "Total")) %>% -->
<!--   cross_by_dimensions(material, material_type) %>% -->
<!--   cross_by_periods(c("month", "quarter", "year")) %>% -->
<!--   summarize(nb_pounds = sum(month_current)) %>% -->
<!--   ungroup() -->



<!-- ## different ways of representing a distribution -->

<!-- penguins_pivoted %>% -->
<!--   ggplot(aes(value, fill = species)) + -->
<!--   geom_histogram(bins = 20) + -->
<!--   facet_wrap(~ metric, scales = "free_x") -->

<!-- penguins_pivoted %>% -->
<!--   ggplot(aes(value, fill = species)) + -->
<!--   geom_density(alpha = .5) + -->
<!--   facet_wrap(~ metric, scales = "free") -->

<!-- penguins_pivoted %>% -->
<!--   ggplot(aes(species, value)) + -->
<!--   geom_boxplot() + -->
<!--   facet_wrap(~ metric, scales = "free_y") -->

<!-- ## mention tidymodels, refer to screencasts by Julia Silge and website on tidymodels -->

<!-- ## mention tidytext, refer to book by Robinson and Silge -->

<!-- ## reduire texte dans strip apres un facet_wrap g -->

<!-- + theme(strip.text = element_text(size = 6)) -->

<!-- voir aussi un  -->

<!-- mutate(port_origin = str_trunc(port_origin, 25), -->

<!-- ## working with factors -->

<!-- plants <- tt$plants %>% -->
<!--   mutate(year_last_seen = fct_relevel(year_last_seen, "Before 1900")) %>% -->
<!--   separate(binomial_name, c("genus", "species"), sep = " ", remove = FALSE) -->
