RMS Titanic
================
Angela Sharer
2020-07-11

  - [First Look](#first-look)
  - [Deeper Look](#deeper-look)
      - [Purpose and Reading](#purpose-and-reading)
  - [Grading Rubric](#grading-rubric)
      - [Individual](#individual)
      - [Team](#team)
      - [Due Date](#due-date)
  - [Notes](#notes)

*Background*: The RMS Titanic sank on its maiden voyage in 1912; about
67% of its passengers died.

# First Look

<!-- -------------------------------------------------- -->

**q1** Perform a glimpse of `df_titanic`. What variables are in this
dataset?

``` r
## TASK: Perform a `glimpse` of df_titanic
glimpse(df_titanic)
```

    ## Rows: 32
    ## Columns: 5
    ## $ Class    <chr> "1st", "2nd", "3rd", "Crew", "1st", "2nd", "3rd", "Crew", "1…
    ## $ Sex      <chr> "Male", "Male", "Male", "Male", "Female", "Female", "Female"…
    ## $ Age      <chr> "Child", "Child", "Child", "Child", "Child", "Child", "Child…
    ## $ Survived <chr> "No", "No", "No", "No", "No", "No", "No", "No", "No", "No", …
    ## $ n        <dbl> 0, 0, 35, 0, 0, 0, 17, 0, 118, 154, 387, 670, 4, 13, 89, 3, …

**Observations**:

  - Class (this includes ticketed passengers’ cabin class, e.g. “1st,”
    and also includes employees, e.g. “Crew”)
  - Sex (“Male,” “Female”)
  - Age (this is not numerical–it’s a string, e.g. “Child”)
  - Survived (again, this is a string, e.g. “No”)
  - n (this is the only numerical data in df\_titanic, and looks to be a
    count of people of this class & sex & age & survival status)

**q2** Skim the [Wikipedia
article](https://en.wikipedia.org/wiki/RMS_Titanic) on the RMS Titanic,
and look for a total count of passengers. Compare against the total
computed below. Are there any differences? Are those differences large
or small? What might account for those differences?

``` r
## NOTE: No need to edit! We'll cover how to
## do this calculation in a later exercise.
df_titanic %>% summarize(total = sum(n))
```

    ## # A tibble: 1 x 1
    ##   total
    ##   <dbl>
    ## 1  2201

**Observations**:

  - According to Wikipedia, the Titanic carried 2,224 passengers and
    crew (estimated), and our sum is close to this–2,201, or only 23
    people short of Wikipedia’s estimate.
  - I would characterize this difference as small. 23 people is only
    about 1% of 2,224, so if Wikipedia’s larger figure is correct, we
    are only missing 1% of passengers and crew.
  - As for the differences, the number of deaths are disputed, due to
    some passengers canceling their trip at the last minute (or perhaps
    not making the sailing), but still being included in the ship’s
    passenger manifest; also, according to Wikipedia, some passengers
    traveled under aliases, and were counted twice in the casualties
    list.

**q3** Create a plot showing the count of passengers who *did* survive,
along with aesthetics for `Class` and `Sex`. Document your observations
below.

*Note*: There are many ways to do this.

``` r
## TASK: Visualize counts against `Class` and `Sex`
df_titanic %>%
  filter(Survived == "Yes") %>%
  ggplot() +
    geom_col(mapping = aes(x = Class, y = n, fill = Sex))
```

![](c01-titanic-assignment_files/figure-gfm/q3-task%20-%20class%20and%20sex%20counts,%20focused%20on%20class-1.png)<!-- -->

**Observations**:

  - Most survivors from 1st and 2nd class were women.
  - 3rd class survivors were more balanced in gender.
  - Crew survivors were mostly male.
  - More crew survived than any other class, which surprises me
    slightly. (Will need to compare totals for these groups.)
  - Among paying passengers, more 1st class passengers survived than
    from either other class.
  - More 3rd class passengers survived than 2nd class passengers, which
    also surprises me slightly. (Will need to compare totals for these
    groups.)

<!-- end list -->

``` r
## TASK: Visualize counts against `Class` and `Sex`
df_titanic %>%
  filter(Survived == "Yes") %>%
  ggplot() +
    geom_col(mapping = aes(x = Class, y = n, fill = Sex), position = "fill")
```

![](c01-titanic-assignment_files/figure-gfm/q3-task%20-%20class%20and%20sex%20counts,%20focused%20on%20class%20proportion-1.png)<!-- -->

**Observations**: - Proportionally, women in the richer classes (1st and
2nd) made up more of the survivors than did those from 3rd class, and
much more than the crew. - Without looking at the base proportions,
though, it’s not clear what this means.

``` r
## TASK: Visualize counts against `Class` and `Sex`
df_titanic %>%
  filter(Survived == "Yes") %>%
  ggplot() +
    geom_col(mapping = aes(x = Sex, y = n, fill = Class))
```

![](c01-titanic-assignment_files/figure-gfm/q3-task%20-%20class%20and%20sex%20counts,%20focused%20on%20sex-1.png)<!-- -->

**Observations**:

  - Ultimately, more men than women survived, but not by much.
  - If we exclude crew members, the reverse would be true: female
    survivors among passengers far outnumber the male survivors in this
    group. (It’s just the male crew members that throw things off here.)

<!-- end list -->

``` r
## TASK: Visualize counts against `Class` and `Sex`
df_titanic %>% 
  filter(Survived == "Yes") %>%
  ggplot() +
    geom_col(mapping = aes(x = Sex, y = n, fill = Class), position = "fill")
```

![](c01-titanic-assignment_files/figure-gfm/q3-task%20-%20class%20and%20sex%20counts,%20focused%20on%20sex,%20by%20proportion-1.png)<!-- -->

**Observations**:

  - Proportionally, most female survivors came from 1st or 2nd class,
    whereas most male survivors were crew.

# Deeper Look

<!-- -------------------------------------------------- -->

Raw counts give us a sense of totals, but they are not as useful for
understanding differences between groups. This is because the
differences we see in counts could be due to either the relative size of
the group OR differences in outcomes for those groups. To make
comparisons between groups, we should also consider *proportions*.\[1\]

The following code computes proportions within each `Class, Sex, Age`
group.

``` r
## NOTE: No need to edit! We'll cover how to
## do this calculation in a later exercise.
df_prop <-
  df_titanic %>%
  group_by(Class, Sex, Age) %>%
  mutate(
    Total = sum(n),
    Prop = n / Total
  ) %>%
  ungroup()
df_prop
```

    ## # A tibble: 32 x 7
    ##    Class Sex    Age   Survived     n Total    Prop
    ##    <chr> <chr>  <chr> <chr>    <dbl> <dbl>   <dbl>
    ##  1 1st   Male   Child No           0     5   0    
    ##  2 2nd   Male   Child No           0    11   0    
    ##  3 3rd   Male   Child No          35    48   0.729
    ##  4 Crew  Male   Child No           0     0 NaN    
    ##  5 1st   Female Child No           0     1   0    
    ##  6 2nd   Female Child No           0    13   0    
    ##  7 3rd   Female Child No          17    31   0.548
    ##  8 Crew  Female Child No           0     0 NaN    
    ##  9 1st   Male   Adult No         118   175   0.674
    ## 10 2nd   Male   Adult No         154   168   0.917
    ## # … with 22 more rows

**q4** Replicate your visual from q3, but display `Prop` in place of
`n`. Document your observations, and note any new/different observations
you make in comparison with q3.

**Observations**:

  - Write your observations here.

**q5** Create a plot showing the group-proportion of passengers who
*did* survive, along with aesthetics for `Class`, `Sex`, *and* `Age`.
Document your observations below.

*Hint*: Don’t forget that you can use `facet_grid` to help consider
additional variables\!

**Observations**:

  - Write your observations here.

## Purpose and Reading

*Purpose*: Most datasets have at least a few variables. Part of our task
in analyzing a dataset is to understand trends as they vary across these
different variables. Unless we’re careful and thorough, we can easily
miss these patterns. In this challenge you’ll analyze a dataset with a
small number of categorical variables and try to find differences among
the groups.

*Reading*: (Optional) [Wikipedia
article](https://en.wikipedia.org/wiki/RMS_Titanic) on the RMS Titanic.

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category    | Unsatisfactory                                                                   | Satisfactory                                                               |
| ----------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Effort      | Some task **q**’s left unattempted                                               | All task **q**’s attempted                                                 |
| Observed    | Did not document observations                                                    | Documented observations based on analysis                                  |
| Supported   | Some observations not supported by analysis                                      | All observations supported by analysis (table, graph, etc.)                |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Team

<!-- ------------------------- -->

| Category   | Unsatisfactory                                                                                   | Satisfactory                                       |
| ---------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| Documented | No team contributions to Wiki                                                                    | Team contributed to Wiki                           |
| Referenced | No team references in Wiki                                                                       | At least one reference in Wiki to member report(s) |
| Relevant   | References unrelated to assertion, or difficult to find related analysis based on reference text | Reference text clearly points to relevant analysis |

## Due Date

<!-- ------------------------- -->

All the deliverables stated in the rubrics above are due on the day of
the class discussion of that exercise. See the
[Syllabus](https://docs.google.com/document/d/1jJTh2DH8nVJd2eyMMoyNGroReo0BKcJrz1eONi3rPSc/edit?usp=sharing)
for more information.

# Notes

<!-- -------------------------------------------------- -->

\[1\] This is basically the same idea as [Dimensional
Analysis](https://en.wikipedia.org/wiki/Dimensional_analysis); computing
proportions is akin to non-dimensionalizing a quantity.
