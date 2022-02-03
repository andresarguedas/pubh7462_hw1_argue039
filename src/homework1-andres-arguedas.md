PubH7462 - Homework 1
================
Andrés Arguedas
2/2/2022

-   [Problem 2.1](#problem-21)
-   [Problem 2.2](#problem-22)
    -   [Data Description](#data-description)
    -   [Visualization](#visualization)

# Problem 2.1

``` r
sim_data <- tibble(
  x = rnorm(n = 1000, mean = 0, sd = 1),
  y = rnorm(n = 1000, mean = 1, sd = 2),
  sum_indicator = ifelse(x + y > 0.5, TRUE, FALSE)) %>% 
  mutate(indicator_TRUE = factor(sum_indicator,
                                 labels = c("No", "Yes")),
         indicator_TRUE = fct_relevel(indicator_TRUE, "Yes", "No"))
```

``` r
sim_data %>% ggplot(aes(x = x, y = y, col = indicator_TRUE)) +
  geom_point() +
  labs(title = "Figure 1:
  Two random normal samples of size 1000, one with mean 0 and standard 
  deviation 1, and the other with mean 1 and standard deviation 2, 
  colored according to if their sum is greater than 0.5",
       x = expression(X %~% N(0,1)),
       y = expression(Y %~% N(1,2)),
       col = expression(X + Y > 0.5))
```

![](homework1-andres-arguedas_files/figure-gfm/plot-of-simulated-data-1.png)<!-- -->

# Problem 2.2

To start, we need to load the data into R

``` r
# Read the `penguin.RDS` file from the `data` folder
penguin.df <- read_rds("./data/penguin.RDS")
```

## Data Description

-   Observations: 344
-   Variables: 8
-   Mean flipper length: 200.9152 mm
-   Mean bill length: 43.9219 mm

| Variable            | Description                                                        | Units or levels             |
|---------------------|--------------------------------------------------------------------|-----------------------------|
| `species`           | Species of the penguin                                             | Adelie, Chinstrap or Gentoo |
| `island`            | Island of the Palmer Archipelago in which the penguin was observed | Biscoe, Dream or Torgersen  |
| `bill_length_mm`    | Penguin’s bill length                                              | millimeters                 |
| `bill_depth_mm`     | Penguin’s bill depth                                               | millimeters                 |
| `flipper_length_mm` | Penguin’s flipper length                                           | millimeters                 |
| `body_mass_g`       | Penguin’s body mass                                                | grams                       |
| `sex`               | Penguin’s sex                                                      | Female or male              |
| `year`              | Study year in which the penguin was observed                       | 2007, 2008 or 2009          |

## Visualization

``` r
penguin.df %>% ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  geom_point() +
  labs(title = "",
       x = "Bill length (mm)",
       y = "Flipper length (mm)",
       col = "Species")
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-1.png)<!-- -->

``` r
penguin.df %>% ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  facet_wrap(~ sex) +
  geom_point() +
  labs(title = "",
       x = "Bill length (mm)",
       y = "Flipper length (mm)",
       col = "Species")
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-and-sex-1.png)<!-- -->
