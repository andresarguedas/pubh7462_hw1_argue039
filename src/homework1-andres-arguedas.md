PubH7462 - Homework 1
================
Andrés Arguedas
2/2/2022

-   [Independent Bivariate Normal Random
    Sample](#independent-bivariate-normal-random-sample)
-   [Penguin EDA](#penguin-eda)
    -   [Data Description](#data-description)
    -   [Visualization](#visualization)

## Independent Bivariate Normal Random Sample

In this case, we want to create a sample of size *n* = 1000 from an
independent bivariate normal distribution, say
(*X*<sub>1</sub>,*Y*<sub>1</sub>), …, (*X*<sub>*n*</sub>), (*Y*<sub>*n*</sub>)
such that *X*<sub>*i*</sub> ∼ 𝒩(0,1) and *Y*<sub>*i*</sub> ∼ 𝒩(1,2). We
can simulate each of *X*<sub>*i*</sub> and *Y*<sub>*i*</sub>
independently by using the `rnorm()` function, and specifying argument
`n` for the sample size, `mean` for the mean, and `sd` for the standard
deviation. Additionally, we will create an indicator variable, called
`sum_indicator`, which will be `TRUE` if *X* + *Y* \> 0.5 and `FALSE`
otherwise, and then convert this to a categorical variable called
`indicator_TRUE`. Finally, we can put this all together in a tibble
using the `tibble()` function, as shown below.

``` r
# Set a seed to be able to reproduce the results
set.seed(123)

# Create a tibble with the desired variables and call it `sim_data`
sim_data <- tibble(
  # Simulate data from a standard normal distribution with sample size 1000
  x = rnorm(n = 1000, mean = 0, sd = 1),
  # Simulate data from a normal distribution with mean 1 and standard deviation
  # 2 with sample size 1000
  y = rnorm(n = 1000, mean = 1, sd = 2),
  # Use the `ifelse()` function to create a logical variable if x+y>0.5
  sum_indicator = ifelse(x + y > 0.5, TRUE, FALSE)
) %>%
  mutate(
    # Create a new categorical variable based on the `sum_indicator` variable
    indicator_TRUE = factor(sum_indicator, labels = c("No", "Yes")),
    # Reorder the factor levels so that `Yes` comes before `No`
    indicator_TRUE = fct_relevel(indicator_TRUE, "Yes", "No")
  )
```

Having created the simulated data, as well as the other required
variables, and saved them in the `sim_data` object, we can proceed to
create a scatterplot of *X* against *Y*, using the `indicator_TRUE`
variable to color points according to if *X* + *Y* \> 0.5 or not. The
scatterplot is presented as the following figure:

``` r
# Create a scatter plot of `x` against `y`, with coloring depending on if
# $X+Y>0.5$ or not, based on the simulated data from `sim_data`
sim_data %>%
  # Specify the required aesthetic options in the `ggplot()` function
  ggplot(aes(x = x, y = y, col = indicator_TRUE)) +
  # Add points to the plot
  geom_point() +
  # Add titles to the plot, axes, and legends
  labs(
    title = "A random sample of size 1000, from an independent bivariate normal 
    distribution, one with mean 0 and standard deviation 1, and the 
    other with mean 1 and standard deviation 2, colored according 
    to if the sum of a point is greater than 0.5",
    # The `expression()` function allows us to introduce simple math terms into
    # text to be rendered directly by R into the plots
    x = expression(X %~% N(0, 1)),
    y = expression(Y %~% N(1, 2)),
    col = expression(X + Y > 0.5)
  )
```

![](homework1-andres-arguedas_files/figure-gfm/plot-of-simulated-data-1.png)<!-- -->

As we can see, the data appear to follow an elliptical cloud in its
distribution, with the data being divided diagonally according to their
sum. Notice that the range of the x-axis is smaller than that of the
y-axis, and thus care has to be taken when giving other descriptions or
observations of the distribution of the data, since the scales are not
the same across the axes.

## Penguin EDA

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
according to their species, without taking into account the 2 penguins
for which the measurements were missing. This plot is presented in the
following figure:

``` r
# Create a scatterplot of penguin's bill and flipper length, by species
penguin.df %>%
  # Define the variables in the x and y axes, as well as the variable with which
  # to color the different points
  ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  # Add points to the plot
  geom_point() +
  # Add titles to the plot, axes, and legend
  labs(
    title = "Penguin's bill length by flipper length, separated by species, 
    Palmer Archipelago, Antartica, from 2007-2009",
    x = "Bill length (mm)",
    y = "Flipper length (mm)",
    col = "Species"
  )
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-1.png)<!-- -->

Overall, there seems to be a relatively clear separation between the
three species of penguins according to both their flipper and bill
lengths. Specifically, Gentoo penguins seem to have the longest
flippers, overall, while chinstrap penguin’s have longer bills than
adelies. There are some penguins which seem to be relatively smaller
than others in their species, and thus this separation is not exactly
perfect, but it seems to be good for only using these two variables.
Nevertheless, there might be further differences if we additionally
separate the penguins according to their sex, which is presented in the
following figure:

``` r
# Create a scatterplot of penguin's bill and flipper length, by species and sex
penguin.df %>%
  # Drop any observation for which the penguin's sex was not recorded
  filter(!is.na(sex)) %>%
  # Rename the `sex` variable to `Sex` for ease of use when labelling the plot
  rename(Sex = sex) %>%
  # Change the names of the labels for the categories of the `Sex` variable so
  # it's easier to use in the plot
  mutate(Sex = recode(Sex, female = "Female", male = "Male")) %>%
  # Add the corresponding variables in the x and y-axis, as well as the variable
  # to be used for coloring the different observations
  ggplot(aes(y = flipper_length_mm, x = bill_length_mm, col = species)) +
  # Separate the plot further according to the `Sex` variable, and use both the
  # variable and category names to label the corresponding facets
  facet_grid(~Sex, labeller = label_both) +
  # Add points to the plot
  geom_point() +
  # Add titles to the plot, axes, and legend
  labs(
    title = "Penguin's bill length by flipper length, separated by species and sex,
    Palmer Archipelago, Antartica, from 2007-2009",
    x = "Bill length (mm)",
    y = "Flipper length (mm)",
    col = "Species"
  )
```

![](homework1-andres-arguedas_files/figure-gfm/flipper-and-bill-length-by-species-and-sex-1.png)<!-- -->

First of all, it’s important to note that the 9 penguins for which the
sex was not measured were not including when constructing the previous
plot. Nevertheless, from the above plot it seems as though the general
trend according to species are the same across both sexes, with the main
difference that females tend to be smaller than males overall.
Therefore, the penguins seem to be a bit better separated when also
taking into account sex, but it’s also important to remember that there
are still other variables that we haven’t used, like the island, or the
penguin’s body mass and bill depth, which could further help distinguish
between species.
