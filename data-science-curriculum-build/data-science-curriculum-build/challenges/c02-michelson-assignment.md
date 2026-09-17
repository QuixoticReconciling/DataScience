Michelson Speed-of-light Measurements
================
(Your name here)
2020-

- [Grading Rubric](#grading-rubric)
  - [Individual](#individual)
  - [Submission](#submission)
    - [**q1** Re-create the following table (from Michelson (1880),
      pg. 139) using `df_michelson` and `dplyr`. Note that your values
      *will not* match those of Michelson *exactly*; why might this
      be?](#q1-re-create-the-following-table-from-michelson-1880-pg-139-using-df_michelson-and-dplyr-note-that-your-values-will-not-match-those-of-michelson-exactly-why-might-this-be)
    - [**q2** Create a new variable `VelocityVacuum` with the $+92$ km/s
      adjustment to `Velocity`. Assign this new dataframe to
      `df_q2`.](#q2-create-a-new-variable-velocityvacuum-with-the-92-kms-adjustment-to-velocity-assign-this-new-dataframe-to-df_q2)
    - [**q3** Compare Michelson’s speed of light estimate against the
      modern speed of light value. Is Michelson’s estimate of the error
      (his uncertainty) greater or less than the true
      error?](#q3-compare-michelsons-speed-of-light-estimate-against-the-modern-speed-of-light-value-is-michelsons-estimate-of-the-error-his-uncertainty-greater-or-less-than-the-true-error)
    - [**q4** Inspect the following plot with the `Real` Michelson data
      and `Simulated` data from a probability model. Document the
      similarities and differences between the data under *observe*
      below.](#q4-inspect-the-following-plot-with-the-real-michelson-data-and-simulated-data-from-a-probability-model-document-the-similarities-and-differences-between-the-data-under-observe-below)
    - [**q5** You have access to a few other variables. Construct a **at
      least three** visualizations of `VelocityVacuum` against these
      other factors. Are there other patterns in the data that might
      help explain the difference between Michelson’s estimate and
      `LIGHTSPEED_VACUUM`?](#q5-you-have-access-to-a-few-other-variables-construct-a-at-least-three-visualizations-of-velocityvacuum-against-these-other-factors-are-there-other-patterns-in-the-data-that-might-help-explain-the-difference-between-michelsons-estimate-and-lightspeed_vacuum)
  - [Bibliography](#bibliography)

*Purpose*: When studying physical problems, there is an important
distinction between *error* and *uncertainty*. The primary purpose of
this challenge is to dip our toes into these factors by analyzing a real
dataset.

*Reading*: [Experimental Determination of the Velocity of
Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
(Optional)

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category | Needs Improvement | Satisfactory |
|----|----|----|
| Effort | Some task **q**’s left unattempted | All task **q**’s attempted |
| Observed | Did not document observations, or observations incorrect | Documented correct observations based on analysis |
| Supported | Some observations not clearly supported by analysis | All observations clearly supported by analysis (table, graph, etc.) |
| Assessed | Observations include claims not supported by the data, or reflect a level of certainty not warranted by the data | Observations are appropriately qualified by the quality & relevance of the data and (in)conclusiveness of the support |
| Specified | Uses the phrase “more data are necessary” without clarification | Any statement that “more data are necessary” specifies which *specific* data are needed to answer what *specific* question |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Submission

<!-- ------------------------- -->

Make sure to commit both the challenge report (`report.md` file) and
supporting files (`report_files/` folder) when you are done! Then submit
a link to Canvas. **Your Challenge submission is not complete without
all files uploaded to GitHub.**

``` r
# Libraries
library(tidyverse)
library(googlesheets4)

url <- "https://docs.google.com/spreadsheets/d/1av_SXn4j0-4Rk0mQFik3LLr-uf0YdA06i3ugE6n-Zdo/edit?usp=sharing"

# Parameters
LIGHTSPEED_VACUUM    <- 299792.458 # Exact speed of light in a vacuum (km / s)
LIGHTSPEED_MICHELSON <- 299944.00  # Michelson's speed estimate (km / s)
LIGHTSPEED_PM        <- 51         # Michelson error estimate (km / s)
```

*Background*: In 1879 Albert Michelson led an experimental campaign to
measure the speed of light. His approach was a development upon the
method of Foucault\[3\], and resulted in a new estimate of
$v_0 = 299944 \pm 51$ kilometers per second (in a vacuum). This is very
close to the modern *exact* value of 2.9979246^{5}. In this challenge,
you will analyze Michelson’s original data, and explore some of the
factors associated with his experiment.

I’ve already copied Michelson’s data from his 1880 publication; the code
chunk below will load these data from a public googlesheet.

*Aside*: The speed of light is *exact* (there is **zero error** in the
value `LIGHTSPEED_VACUUM`) because the meter is actually
[*defined*](https://en.wikipedia.org/wiki/Metre#Speed_of_light_definition)
in terms of the speed of light!

``` r
## Note: No need to edit this chunk!
gs4_deauth()
ss <- gs4_get(url)
df_michelson <-
  read_sheet(ss) %>%
  select(Date, Distinctness, Temp, Velocity) %>%
  mutate(Distinctness = as_factor(Distinctness))
```

    ## ✔ Reading from "michelson1879".

    ## ✔ Range 'Sheet1'.

``` r
df_michelson %>% glimpse()
```

    ## Rows: 100
    ## Columns: 4
    ## $ Date         <dttm> 1879-06-05, 1879-06-07, 1879-06-07, 1879-06-07, 1879-06-…
    ## $ Distinctness <fct> 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 2, 2, 2, 2, 2, 1, 3, 3, 2, …
    ## $ Temp         <dbl> 76, 72, 72, 72, 72, 72, 83, 83, 83, 83, 83, 90, 90, 71, 7…
    ## $ Velocity     <dbl> 299850, 299740, 299900, 300070, 299930, 299850, 299950, 2…

*Data dictionary*:

- `Date`: Date of measurement
- `Distinctness`: Distinctness of measured images: 3 = good, 2 = fair, 1
  = poor
- `Temp`: Ambient temperature (Fahrenheit)
- `Velocity`: Measured speed of light (km / s)

### **q1** Re-create the following table (from Michelson (1880), pg. 139) using `df_michelson` and `dplyr`. Note that your values *will not* match those of Michelson *exactly*; why might this be?

| Distinctness | n   | MeanVelocity |
|--------------|-----|--------------|
| 3            | 46  | 299860       |
| 2            | 39  | 299860       |
| 1            | 15  | 299810       |

``` r
## TODO: Compute summaries
df_q1 <- df_michelson
df_q1 %>%
  arrange(desc(Distinctness)) %>%

  knitr::kable()
```

| Date       | Distinctness | Temp | Velocity |
|:-----------|:-------------|-----:|---------:|
| 1879-06-05 | 3            |   76 |   299850 |
| 1879-06-09 | 3            |   83 |   299950 |
| 1879-06-09 | 3            |   83 |   299980 |
| 1879-06-09 | 3            |   83 |   299980 |
| 1879-06-09 | 3            |   83 |   299880 |
| 1879-06-13 | 3            |   72 |   300000 |
| 1879-06-13 | 3            |   72 |   300000 |
| 1879-06-13 | 3            |   79 |   299960 |
| 1879-06-13 | 3            |   79 |   299960 |
| 1879-06-13 | 3            |   79 |   299940 |
| 1879-06-13 | 3            |   79 |   299960 |
| 1879-06-13 | 3            |   79 |   299940 |
| 1879-06-13 | 3            |   79 |   299880 |
| 1879-06-13 | 3            |   79 |   299800 |
| 1879-06-17 | 3            |   77 |   299800 |
| 1879-06-17 | 3            |   77 |   299880 |
| 1879-06-17 | 3            |   77 |   299880 |
| 1879-06-17 | 3            |   77 |   299880 |
| 1879-06-17 | 3            |   77 |   299860 |
| 1879-06-20 | 3            |   60 |   299880 |
| 1879-06-20 | 3            |   61 |   299910 |
| 1879-06-21 | 3            |   80 |   299770 |
| 1879-06-21 | 3            |   81 |   299760 |
| 1879-06-21 | 3            |   82 |   299740 |
| 1879-06-21 | 3            |   82 |   299750 |
| 1879-06-21 | 3            |   81 |   299760 |
| 1879-06-23 | 3            |   89 |   299910 |
| 1879-06-23 | 3            |   89 |   299920 |
| 1879-06-23 | 3            |   90 |   299890 |
| 1879-06-23 | 3            |   90 |   299860 |
| 1879-06-23 | 3            |   90 |   299880 |
| 1879-06-24 | 3            |   72 |   299720 |
| 1879-06-24 | 3            |   73 |   299840 |
| 1879-06-24 | 3            |   74 |   299850 |
| 1879-06-24 | 3            |   75 |   299850 |
| 1879-06-24 | 3            |   76 |   299780 |
| 1879-06-27 | 3            |   73 |   299780 |
| 1879-06-27 | 3            |   74 |   299810 |
| 1879-06-27 | 3            |   75 |   299760 |
| 1879-06-27 | 3            |   75 |   299810 |
| 1879-06-27 | 3            |   76 |   299790 |
| 1879-06-27 | 3            |   76 |   299810 |
| 1879-07-02 | 3            |   86 |   299950 |
| 1879-07-02 | 3            |   86 |   299800 |
| 1879-07-02 | 3            |   86 |   299810 |
| 1879-07-02 | 3            |   85 |   299870 |
| 1879-06-07 | 2            |   72 |   299740 |
| 1879-06-07 | 2            |   72 |   299900 |
| 1879-06-07 | 2            |   72 |   300070 |
| 1879-06-07 | 2            |   72 |   299930 |
| 1879-06-07 | 2            |   72 |   299850 |
| 1879-06-09 | 2            |   83 |   300000 |
| 1879-06-10 | 2            |   90 |   299980 |
| 1879-06-10 | 2            |   90 |   299930 |
| 1879-06-12 | 2            |   71 |   299650 |
| 1879-06-12 | 2            |   71 |   299760 |
| 1879-06-13 | 2            |   72 |   299960 |
| 1879-06-17 | 2            |   62 |   299800 |
| 1879-06-17 | 2            |   63 |   299790 |
| 1879-06-18 | 2            |   75 |   299860 |
| 1879-06-18 | 2            |   75 |   299970 |
| 1879-06-18 | 2            |   75 |   299950 |
| 1879-06-20 | 2            |   62 |   299850 |
| 1879-06-20 | 2            |   63 |   299870 |
| 1879-06-20 | 2            |   78 |   299840 |
| 1879-06-20 | 2            |   79 |   299840 |
| 1879-06-20 | 2            |   80 |   299850 |
| 1879-06-20 | 2            |   79 |   299840 |
| 1879-06-20 | 2            |   79 |   299840 |
| 1879-06-20 | 2            |   79 |   299840 |
| 1879-06-21 | 2            |   61 |   299890 |
| 1879-06-21 | 2            |   62 |   299810 |
| 1879-06-21 | 2            |   63 |   299810 |
| 1879-06-21 | 2            |   64 |   299820 |
| 1879-06-21 | 2            |   65 |   299800 |
| 1879-06-26 | 2            |   86 |   299890 |
| 1879-06-26 | 2            |   86 |   299840 |
| 1879-06-30 | 2            |   85 |   299820 |
| 1879-06-30 | 2            |   86 |   299850 |
| 1879-06-30 | 2            |   86 |   299870 |
| 1879-06-30 | 2            |   86 |   299870 |
| 1879-07-01 | 2            |   83 |   299810 |
| 1879-07-01 | 2            |   84 |   299740 |
| 1879-07-01 | 2            |   86 |   299810 |
| 1879-07-01 | 2            |   86 |   299940 |
| 1879-06-12 | 1            |   71 |   299810 |
| 1879-06-14 | 1            |   64 |   299850 |
| 1879-06-14 | 1            |   64 |   299880 |
| 1879-06-14 | 1            |   65 |   299900 |
| 1879-06-14 | 1            |   66 |   299840 |
| 1879-06-14 | 1            |   67 |   299830 |
| 1879-06-14 | 1            |   84 |   299790 |
| 1879-06-14 | 1            |   85 |   299810 |
| 1879-06-14 | 1            |   84 |   299880 |
| 1879-06-14 | 1            |   84 |   299880 |
| 1879-06-14 | 1            |   84 |   299830 |
| 1879-06-17 | 1            |   64 |   299760 |
| 1879-06-18 | 1            |   58 |   299720 |
| 1879-06-18 | 1            |   58 |   299720 |
| 1879-06-18 | 1            |   59 |   299620 |

**Observations**: - Write your observations here! - The distinctness
doesn’t seem to have any coorelation with the velocity - Why might your
table differ from Michelson’s? - The table might differ from Michelson’s
as my table is grouped by distinctness whereas Michelson’s table uses a
different ordering structure. - The data isn’t inherently different, I
was still able to find the same data points in both tables.

The `Velocity` values in the dataset are the speed of light *in air*;
Michelson introduced a couple of adjustments to estimate the speed of
light in a vacuum. In total, he added $+92$ km/s to his mean estimate
for `VelocityVacuum` (from Michelson (1880), pg. 141). While the
following isn’t fully rigorous ($+92$ km/s is based on the mean
temperature), we’ll simply apply this correction to all the observations
in the dataset.

### **q2** Create a new variable `VelocityVacuum` with the $+92$ km/s adjustment to `Velocity`. Assign this new dataframe to `df_q2`.

``` r
## TODO: Adjust the data, assign to df_q2
df_q2 <- df_michelson %>%
  mutate(`VelocityVacuum` = Velocity + 92) %>%
  select(`VelocityVacuum`, everything())
df_q2
```

    ## # A tibble: 100 × 5
    ##    VelocityVacuum Date                Distinctness  Temp Velocity
    ##             <dbl> <dttm>              <fct>        <dbl>    <dbl>
    ##  1         299942 1879-06-05 00:00:00 3               76   299850
    ##  2         299832 1879-06-07 00:00:00 2               72   299740
    ##  3         299992 1879-06-07 00:00:00 2               72   299900
    ##  4         300162 1879-06-07 00:00:00 2               72   300070
    ##  5         300022 1879-06-07 00:00:00 2               72   299930
    ##  6         299942 1879-06-07 00:00:00 2               72   299850
    ##  7         300042 1879-06-09 00:00:00 3               83   299950
    ##  8         300072 1879-06-09 00:00:00 3               83   299980
    ##  9         300072 1879-06-09 00:00:00 3               83   299980
    ## 10         299972 1879-06-09 00:00:00 3               83   299880
    ## # ℹ 90 more rows

As part of his study, Michelson assessed the various potential sources
of error, and provided his best-guess for the error in his
speed-of-light estimate. These values are provided in
`LIGHTSPEED_MICHELSON`—his nominal estimate—and
`LIGHTSPEED_PM`—plus/minus bounds on his estimate. Put differently,
Michelson believed the true value of the speed-of-light probably lay
between `LIGHTSPEED_MICHELSON - LIGHTSPEED_PM` and
`LIGHTSPEED_MICHELSON + LIGHTSPEED_PM`.

Let’s introduce some terminology:\[2\]

- **Error** is the difference between a true value and an estimate of
  that value; for instance `LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON`.
- **Uncertainty** is an analyst’s *assessment* of the error.

Since a “true” value is often not known in practice, one generally does
not know the error. The best they can do is quantify their degree of
uncertainty. We will learn some means of quantifying uncertainty in this
class, but for many real problems uncertainty includes some amount of
human judgment.\[2\]

### **q3** Compare Michelson’s speed of light estimate against the modern speed of light value. Is Michelson’s estimate of the error (his uncertainty) greater or less than the true error?

``` r
## TODO: Compare Michelson's estimate and error against the true value
## Your code here!
LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON
```

    ## [1] -151.542

**Observations**: - Is Michelson’s estimate of the error (his
uncertainty) greater or less than the true error? - Michelson’s estimate
of the error is greater than the true error. - Make a quantitative
comparison between Michelson’s uncertainty and his error. - Michelson’s
uncertainty was 92, and his error was 151.542. His error was more than
his uncertainty

The following plot shows all of Michelson’s data as a [control
chart](https://en.wikipedia.org/wiki/Control_chart); this sort of plot
is common in manufacturing, where it is used to help determine if a
manufacturing process is under [statistical
control](https://en.wikipedia.org/wiki/Statistical_process_control).
Each dot is one of Michelson’s measurements, and the grey line connects
the mean taken for each day. The same plot also shows simulated data
using a probability model. We’ll get into statistics later in the
course; for now, let’s focus on understanding what real and simulated
data tend to look like.

### **q4** Inspect the following plot with the `Real` Michelson data and `Simulated` data from a probability model. Document the similarities and differences between the data under *observe* below.

``` r
## Note: No need to edit this chunk!
## Calibrate simulated data
v_mean <-
  df_q2 %>%
  summarize(m = mean(VelocityVacuum)) %>%
  pull(m)
v_sd <-
  df_q2 %>%
  summarize(s = sd(VelocityVacuum)) %>%
  pull(s)

## Visualize
set.seed(101)
df_q2 %>%
  mutate(Simulated = rnorm(n(), mean = v_mean, sd = v_sd)) %>%
  rename(Real = VelocityVacuum) %>%
  pivot_longer(
    cols = c(Simulated, Real),
    names_to = "source",
    values_to = "velocity"
  ) %>%

  ggplot(aes(Date, velocity)) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON,
    linetype = "dotted"
  ) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON - LIGHTSPEED_PM,
    linetype = "dashed"
  ) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON + LIGHTSPEED_PM,
    linetype = "dashed"
  ) +

  geom_line(
    data = . %>%
      group_by(Date, source) %>%
      summarize(velocity_mean = mean(velocity)),
    mapping = aes(y = velocity_mean),
    color = "grey50"
  ) +
  geom_point(
    mapping = aes(y = velocity),
    size = 0.8
  ) +

  facet_grid(source~.) +
  theme_minimal() +
  labs(
    x = "Date of Measurement (1879)",
    y = "Velocity (in Vacuum)"
  )
```

    ## `summarise()` has regrouped the output.
    ## ℹ Summaries were computed grouped by Date and source.
    ## ℹ Output is grouped by Date.
    ## ℹ Use `summarise(.groups = "drop_last")` to silence this message.
    ## ℹ Use `summarise(.by = c(Date, source))` for per-operation grouping
    ##   (`?dplyr::dplyr_by`) instead.

![](c02-michelson-assignment_files/figure-gfm/q4-cf-real-simulated-1.png)<!-- -->

**Observations**: Similarities - Both have means that hover around the
the speed of light in a vacuum - The outliers for both plots are similar
distances away Differences - There are more days where the mean velocity
falls outside of the uncertainty in the real data compared to the
simulation data

### **q5** You have access to a few other variables. Construct a **at least three** visualizations of `VelocityVacuum` against these other factors. Are there other patterns in the data that might help explain the difference between Michelson’s estimate and `LIGHTSPEED_VACUUM`?

``` r
df_q2 %>%
  ggplot(aes(x = VelocityVacuum)) +
  geom_density() +
  labs(
    title = "Density of Velocity",
    x = "Velocity"
  )
```

![](c02-michelson-assignment_files/figure-gfm/q5-task-1.png)<!-- -->

``` r
df_q2 %>%
  ggplot(aes(x = Date, y = VelocityVacuum, color = Temp)) +
  geom_point() +
  geom_line(
    data = . %>% 
      group_by(Date) %>%
      summarize(VelocityVacuum = mean(VelocityVacuum)),
    mapping = aes(linetype = "Mean Velocity by Day"),
    color = "red"
  ) +
  labs(
    title = "Velocity vs. Date categorized by Temperature",
    x = "Date",
    y = "Velocity"
  )
```

![](c02-michelson-assignment_files/figure-gfm/q5-task-2.png)<!-- -->

``` r
df_q2 %>%
  group_by(Distinctness) %>%
  ggplot(aes(x = VelocityVacuum, fill = Distinctness)) +
  geom_bar(position = "stack") +
  labs(
    title = "Velocity organized by Distinctness",
    x = "Velocity",
    y = "Count"
  )
```

![](c02-michelson-assignment_files/figure-gfm/q5-task-3.png)<!-- -->

**Observations**:

- The density plot for ‘VelocityVacuum’ shows that his values are in
  general higher than the “true value” for the speed of light
- The scatter plot attempts to see if there is any correlation between
  temperature and velocity or time of testing and velocity. From the
  graph, it doesn’t seem that the temperature cause a significant shift
  in results of tests.
- It also doesn’t seem to suggest that the experiments got closer to the
  true speed of light as he ran more test. This is indicated by the mean
  velocity by day line.
- The bar chart attempts to see if distinctness played a role in the
  accuracy of the test findings. Distinctness is grouped into three
  categories, but seemingly the light particles that were less distinct
  had a lower average than those that were more distinct.
- This suggest that the distinctness of the light sample in the test was
  not indicative of how accurate it was.

## Bibliography

- \[1\] Michelson, [Experimental Determination of the Velocity of
  Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
  (1880)
- \[2\] Henrion and Fischhoff, [Assessing Uncertainty in Physical
  Constants](https://www.cmu.edu/epp/people/faculty/research/Fischoff-Henrion-Assessing%20uncertainty%20in%20physical%20constants.pdf)
  (1986)
- \[3\] BYU video about a [Fizeau-Foucault
  apparatus](https://www.youtube.com/watch?v=Ik5ORaaeaME), similar to
  what Michelson used.
