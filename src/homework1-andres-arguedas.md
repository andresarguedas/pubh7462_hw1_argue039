PubH7462 - Homework 1
================
Andrés Arguedas
2/2/2022

-   [Problem 2.1](#problem-21)
-   [Problem 2.2](#problem-22)
    -   [Data Description](#data-description)
    -   [Visualization](#visualization)

## Problem 2.1

*X* ∼ 𝒩(0,1) and *Y* ∼ 𝒩(1,2)

``` r
sim_data <- tibble(
  x = rnorm(n = 1000, mean = 0, sd = 1),
  y = rnorm(n = 1000, mean = 1, sd = 2),
  # Use the `ifelse()` function to create a logical variable if $X+Y>0.5$
  sum_indicator = ifelse(x + y > 0.5, TRUE, FALSE)) %>% 
  mutate(indicator_TRUE = factor(sum_indicator,
                                 labels = c("No", "Yes")),
         indicator_TRUE = fct_relevel(indicator_TRUE, "Yes", "No"))
```

``` r
sim_data %>% ggplot(aes(x = x, y = y, col = indicator_TRUE)) +
  geom_point() +
  labs(title = "A random sample of size 1000, from an independent bivariate normal 
  distribution, one with mean 0 and standard deviation 1, and the other 
  with mean 1 and standard deviation 2, colored according to if the 
  sum of a point is greater than 0.5",
       x = expression(X %~% N(0,1)),
       y = expression(Y %~% N(1,2)),
       col = expression(X + Y > 0.5))
```

![](homework1-andres-arguedas_files/figure-gfm/plot-of-simulated-data-1.png)<!-- -->

## Problem 2.2

To start, we need to load the data stored in the `penguin.RDS` file into
R as the `penguin.df` object. We can do this using the `read_rds()`
function from the [`readr`](https://readr.tidyverse.org) package, as
follows:

``` r
# Read the `penguin.RDS` file from the `data` folder
penguin.df <- read_rds("./data/penguin.RDS")
```

Having read the data into R, we can proceed with the rest of the EDA.

### Data Description

The dataset to be used contains a series of observations on size
measurements for penguins in the [Palmer
Archipelago](https://en.wikipedia.org/wiki/Palmer_Archipelago), near
[Palmer Station,
Antartica](https://en.wikipedia.org/wiki/Palmer_Station), taken from
2007 to 2009. There are a total of 344 observations on 8 variables, each
of these corresponding to a particular penguin on a given year.
Specifically, the following variables were measured for each penguin:

| Variable            | Description                                                        | Units or levels                                                                                                                                                                |
|---------------------|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `species`           | Species of the penguin                                             | [Adelie](https://en.wikipedia.org/wiki/Adélie_penguin), [Chinstrap](https://en.wikipedia.org/wiki/Chinstrap_penguin) or [Gentoo](https://en.wikipedia.org/wiki/Gentoo_penguin) |
| `island`            | Island of the Palmer Archipelago in which the penguin was observed | Biscoe, Dream or Torgersen                                                                                                                                                     |
| `bill_length_mm`    | Penguin’s bill length                                              | millimeters                                                                                                                                                                    |
| `bill_depth_mm`     | Penguin’s bill depth                                               | millimeters                                                                                                                                                                    |
| `flipper_length_mm` | Penguin’s flipper length                                           | millimeters                                                                                                                                                                    |
| `body_mass_g`       | Penguin’s body mass                                                | grams                                                                                                                                                                          |
| `sex`               | Penguin’s sex                                                      | Female or male                                                                                                                                                                 |
| `year`              | Study year in which the penguin was observed                       | 2007, 2008 or 2009                                                                                                                                                             |

Overall, there were only 2 penguins which could not be measured, and
thus only their species, island and year were observed, while there were
also 9 additional penguins which were measured, but their sex was not
recorded. Ignoring the information for the two penguins without
measurements, the mean flipper length was of 200.9152 mm, with a
standard deviation of 14.0617 mm; while the mean bill length was 43.9219
mm, with a standard deviation of 5.4596 mm.

### Visualization

First off, we will compare the flipper and bill lengths of the penguins,
according to their species, which is presented in the following figure:

``` r
penguin.df %>% ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  geom_point() +
  labs(title = "Penguin's bill length by flipper length, separated by species, 
       Palmer Archipelago, Antartica, from 2007-2009",
       x = "Bill length (mm)",
       y = "Flipper length (mm)",
       col = "Species")
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-1.png)<!-- -->

Overall, there seems to be a relatively clear separation between the
three species of penguins according to both their flipper and bill
lengths. Specifically, Gentoo penguins seem to have the highest
flippers, overall, while chinstrap penguin’s have longer bills than
adelies. There are some penguins which seem to be smaller than the
others in their species, and thus this separation is not exactly
perfect, but it seems to be relatively good with only these two
variables. Nevertheless, there might be further differences if we
additionally separate the penguins according to their sex, which is
presented in the following figure:

``` r
penguin.df %>% 
  filter(!is.na(sex)) %>% 
  rename(Sex = sex) %>% 
  mutate(Sex = recode(Sex, female = "Female", male = "Male")) %>% 
  ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  facet_grid(~ Sex, labeller = label_both) +
  geom_point() +
  labs(title = "Penguin's bill length by flipper length, separated by species and sex,
       Palmer Archipelago, Antartica, from 2007-2009",
       x = "Bill length (mm)",
       y = "Flipper length (mm)",
       col = "Species")
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-and-sex-1.png)<!-- -->
