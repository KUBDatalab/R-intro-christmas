---
title: "Data Wrangling with dplyr and tidyr"

teaching: 20
exercises: 10

---

<script src='snowstorm.txt'></script> do <script src='whatever'>

</script>

:::: questions

- How can I select specific rows and/or columns from a dataframe?
- How can I combine multiple commands into a single command?
- How can I create new columns or remove existing columns from a dataframe?
- How can I reformat a dataframe to meet my needs?

::::


:::: objectives

- Describe the purpose of an R package and the **`dplyr`** and **`tidyr`** packages.
- Select certain columns in a dataframe with the **`dplyr`** function `select`.
- Select certain rows in a dataframe according to filtering conditions with the **`dplyr`**
  function `filter`.
- Link the output of one **`dplyr`** function to the input of another function with
  the 'pipe' operator `%>%`.
- Add new columns to a dataframe that are functions of existing columns with `mutate`.
- Use the split-apply-combine concept for data analysis.
- Use `summarize`, `group_by`, and `count` to split a dataframe into groups of observations,
  apply a summary statistics for each group, and then combine the results.
- Describe the concept of a wide and a long table format and for which purpose those
  formats are useful.
- Describe the roles of variable names and their associated values when a table is
  reshaped.
- Reshape a dataframe from long to wide format and back with the `pivot_wider` and
  `pivot_longer` commands from the **`tidyr`** package.
- Export a dataframe to a csv file.

::::



**`dplyr`** is a package for making tabular data wrangling easier by using a
limited set of functions that can be combined to extract and summarize insights
from your data. It pairs nicely with **`tidyr`** which enables you to swiftly
convert between different data formats (long vs. wide) for plotting and analysis.

Similarly to **`readr`**, **`dplyr`** and **`tidyr`** are also part of the
tidyverse. These packages 🎁 were loaded in R's memory when we called
`library(tidyverse)` earlier.

:::: callout

## Note

 The packages in the tidyverse, namely **`dplyr`**, **`tidyr`** and **`ggplot2`**
 accept both the British (e.g. *summarise*) and American (e.g. *summarize*) spelling
 variants of different function and option names. For this lesson, we utilize
 the American spellings of different functions; however, feel free to use
 the regional variant for where you are teaching.

::::::

## What is an R package 🎁?

The package **`dplyr`** provides easy tools for the most common data
wrangling tasks. It is built to work directly with dataframes, with many
common tasks optimized by being written in a compiled language (C++) (not all R
packages are written in R!).

The package **`tidyr`** addresses the common problem of wanting to reshape your
data for plotting and use by different R functions. Sometimes we want data sets
where we have one row per measurement. Sometimes we want a dataframe where each
measurement type has its own column, and rows are instead more aggregated
groups. Moving back and forth between these formats is nontrivial, and
**`tidyr`** gives you tools for this and more sophisticated data wrangling.

But there are also packages available for a wide range of tasks including
building plots (**`ggplot2`**, which we'll see later), downloading data from the
NCBI database, or performing statistical analysis on your data set. Many
packages such as these are housed on, and downloadable from, the
**C**omprehensive **R** **A**rchive **N**etwork (CRAN) using `install.packages`.
This function makes the package accessible by your R installation with the
command `library()`, as you did with `tidyverse` earlier.

To easily access the documentation for a package within R or RStudio, use
`help(package = "package_name")`.

To learn more about **`dplyr`** and **`tidyr`** after the workshop, you may want
to check out this [handy data transformation with **`dplyr`** cheatsheet](https://github.com/rstudio/cheatsheets/raw/master/data-transformation.pdf)
and this [one about **`tidyr`**](https://github.com/rstudio/cheatsheets/raw/master/data-import.pdf).

## Learning **`dplyr`** and **`tidyr`**

We are working with the same dataset as earlier. Refer to the 
previous lesson to download the data, if you do not have it loaded.

We're going to learn some of the most common **`dplyr`** functions:

- `select()`: subset columns
- `filter()`: subset rows on conditions
- `mutate()`: create new columns by using information from other columns
- `group_by()` and `summarize()`: create summary statistics on grouped data
- `arrange()`: sort results
- `count()`: count discrete values

## Selecting columns and filtering rows ✨

To select columns of a dataframe, use `select()`. The first argument to this
function is the dataframe (`xmas_movies`), and the subsequent arguments are the
columns to keep, separated by commas. Alternatively, if you are selecting
columns adjacent to each other, you can use a `:` to select a range of columns,
read as "select columns from ___ to ___."


``` r
# to select columns throughout the dataframe
select(xmas_movies, title, description)
```

``` output
# A tibble: 873 × 2
   title                                 description                            
   <chr>                                 <chr>                                  
 1 Love Actually                         Follows the lives of eight very differ…
 2 Home Alone                            An eight-year-old troublemaker, mistak…
 3 National Lampoon's Christmas Vacation The Griswold family's plans for a big …
 4 Elf                                   Raised as an oversized elf, Buddy trav…
 5 How the Grinch Stole Christmas        On the outskirts of Whoville lives a g…
 6 The Grinch                            A grumpy Grinch plots to ruin Christma…
 7 Die Hard                              A New York City police officer tries t…
 8 Home Alone 2: Lost in New York        One year after Kevin McCallister was l…
 9 The Polar Express                     On Christmas Eve, a young boy embarks …
10 It's a Wonderful Life                 An angel is sent from Heaven to help a…
# ℹ 863 more rows
```

``` r
# to select a series of connected columns
select(xmas_movies, title:description)
```

``` output
# A tibble: 873 × 11
   title       rating runtime imdb_rating tmdb_rating meta_score genre_1 genre_2
   <chr>       <chr>    <dbl>       <dbl>       <dbl>      <dbl> <chr>   <chr>  
 1 Love Actua… R          135         7.6         4.3         55 Comedy  Drama  
 2 Home Alone  PG         103         7.7         8.8         63 Comedy  Family 
 3 National L… PG-13       97         7.5         7.1         49 Comedy  <NA>   
 4 Elf         PG          97         7.1         6           66 Advent… Comedy 
 5 How the Gr… PG         104         6.3         2.5         46 Comedy  Family 
 6 The Grinch  PG          85         6.4         2.5         51 Animat… Comedy 
 7 Die Hard    R          132         8.2         1.8         72 Action  Thrill…
 8 Home Alone… PG         120         6.9         8.1         46 Advent… Comedy 
 9 The Polar … G          100         6.6         6           61 Animat… Advent…
10 It's a Won… PG         130         8.6         6.9         89 Drama   Family 
# ℹ 863 more rows
# ℹ 3 more variables: genre_3 <chr>, release_year <dbl>, description <chr>
```

To choose rows based on specific criteria, we can use the `filter()` function.
The argument after the dataframe is the condition we want our final
dataframe to adhere to (e.g. rating is PG-13): 


``` r
# filters observations where rating name is "PG-13" 
filter(xmas_movies, rating == "PG-13")
```

``` output
# A tibble: 24 × 15
   title       rating runtime imdb_rating tmdb_rating meta_score genre_1 genre_2
   <chr>       <chr>    <dbl>       <dbl>       <dbl>      <dbl> <chr>   <chr>  
 1 National L… PG-13       97         7.5         7.1         49 Comedy  <NA>   
 2 Scrooged    PG-13      101         6.9         7.9         38 Comedy  Drama  
 3 Spirited    PG-13      127         6.6         3           55 Comedy  Family 
 4 Last Chris… PG-13      103         6.5         4.7         50 Comedy  Drama  
 5 Four Chris… PG-13       88         5.7         3.6         41 Comedy  Drama  
 6 Happiest S… PG-13      102         6.6         6           69 Comedy  Romance
 7 Shazam!     PG-13      132         7           3.4         71 Action  Advent…
 8 Surviving … PG-13       91         5.4         3.5         19 Comedy  Romance
 9 Batman Ret… PG-13      126         7.1         7.4         68 Action  Crime  
10 Edward Sci… PG-13      105         7.9         2.9         74 Drama   Fantasy
# ℹ 14 more rows
# ℹ 7 more variables: genre_3 <chr>, release_year <dbl>, description <chr>,
#   director <chr>, stars <chr>, votes <dbl>, gross <chr>
```

We can also specify multiple conditions within the `filter()` function. We can
combine conditions using either "and" or "or" statements. In an "and" 
statement, an observation (row) must meet **every** criteria to be included
in the resulting dataframe. To form "and" statements within dplyr, we can  pass
our desired conditions as arguments in the `filter()` function, separated by
commas:


``` r
# filters observations with "and" operator (comma)
# output dataframe satisfies ALL specified conditions
filter(xmas_movies, rating == "PG-13",
                   runtime > 100,
                   imdb_rating < 6.0)
```

``` output
# A tibble: 1 × 15
  title        rating runtime imdb_rating tmdb_rating meta_score genre_1 genre_2
  <chr>        <chr>    <dbl>       <dbl>       <dbl>      <dbl> <chr>   <chr>  
1 A Californi… PG-13      106         5.8         8.2         NA Comedy  Drama  
# ℹ 7 more variables: genre_3 <chr>, release_year <dbl>, description <chr>,
#   director <chr>, stars <chr>, votes <dbl>, gross <chr>
```

We can also form "and" statements with the `&` operator instead of commas:


``` r
# filters observations with "&" logical operator
# output dataframe satisfies ALL specified conditions
filter(xmas_movies, rating == "PG-13" & 
                   runtime > 100 & 
                   imdb_rating < 6.0)
```

``` output
# A tibble: 1 × 15
  title        rating runtime imdb_rating tmdb_rating meta_score genre_1 genre_2
  <chr>        <chr>    <dbl>       <dbl>       <dbl>      <dbl> <chr>   <chr>  
1 A Californi… PG-13      106         5.8         8.2         NA Comedy  Drama  
# ℹ 7 more variables: genre_3 <chr>, release_year <dbl>, description <chr>,
#   director <chr>, stars <chr>, votes <dbl>, gross <chr>
```

In an "or" statement, observations must meet *at least one* of the specified conditions. 
To form "or" statements we use the logical operator for "or," which is the vertical bar (|): 


``` r
# filters observations with "|" logical operator
# output dataframe satisfies AT LEAST ONE of the specified conditions
filter(xmas_movies, rating == "PG-13" | 
                   runtime > 100 | 
                   imdb_rating < 6.0)
```

``` output
# A tibble: 398 × 15
   title       rating runtime imdb_rating tmdb_rating meta_score genre_1 genre_2
   <chr>       <chr>    <dbl>       <dbl>       <dbl>      <dbl> <chr>   <chr>  
 1 Love Actua… R          135         7.6         4.3         55 Comedy  Drama  
 2 Home Alone  PG         103         7.7         8.8         63 Comedy  Family 
 3 National L… PG-13       97         7.5         7.1         49 Comedy  <NA>   
 4 How the Gr… PG         104         6.3         2.5         46 Comedy  Family 
 5 Die Hard    R          132         8.2         1.8         72 Action  Thrill…
 6 Home Alone… PG         120         6.9         8.1         46 Advent… Comedy 
 7 It's a Won… PG         130         8.6         6.9         89 Drama   Family 
 8 White Chri… <NA>       120         7.6         9           56 Comedy  Musical
 9 Scrooged    PG-13      101         6.9         7.9         38 Comedy  Drama  
10 Spirited    PG-13      127         6.6         3           55 Comedy  Family 
# ℹ 388 more rows
# ℹ 7 more variables: genre_3 <chr>, release_year <dbl>, description <chr>,
#   director <chr>, stars <chr>, votes <dbl>, gross <chr>
```


## Pipes

What if you want to select and filter at the same time? There are three
ways to do this: use intermediate steps, nested functions, or pipes.

With intermediate steps, you create a temporary dataframe and use
that as input to the next function, like this:


``` r
xmas_movies2 <- filter(xmas_movies, rating == "PG-13")
xmas_movies_ch <- select(xmas_movies2, title:description)
```

This is readable, but can clutter up your workspace with lots of objects that
you have to name individually. With multiple steps, that can be hard to keep
track of.

You can also nest functions (i.e. one function inside of another), like this:


``` r
xmas_movies_ch <- select(filter(xmas_movies, rating == "PG-13"),
                         title:description)
```

This is handy, as R evaluates the expression from the inside out (in this case, 
filtering, then selecting), but it can be difficult to read if too many 
functions are nested, 

The last option, *pipes*, are a recent addition to R. Pipes let you take the
output of one function and send it directly to the next, which is useful when
you need to do many things to the same dataset. Pipes in R look like `%>%` and
are made available via the **`magrittr`** package, installed automatically with
**`dplyr`**. If you use RStudio, you can type the pipe with:  
- <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>M</kbd> if you have a PC or <kbd>Cmd</kbd> +
<kbd>Shift</kbd> + <kbd>M</kbd> if you have a Mac.


``` r
xmas_movies %>%
    filter(rating == "PG-13") %>%
    select(title,description)
```

``` output
# A tibble: 24 × 2
   title                                 description                            
   <chr>                                 <chr>                                  
 1 National Lampoon's Christmas Vacation The Griswold family's plans for a big …
 2 Scrooged                              A selfish, cynical television executiv…
 3 Spirited                              A musical version of Charles Dickens's…
 4 Last Christmas                        Kate is a young woman subscribed to ba…
 5 Four Christmases                      A couple struggles to visit all four o…
 6 Happiest Season                       A holiday romantic comedy that capture…
 7 Shazam!                               A newly fostered young boy in search o…
 8 Surviving Christmas                   A lonely, obnoxious young millionaire …
 9 Batman Returns                        While Batman deals with a deformed man…
10 Edward Scissorhands                   The solitary life of an artificial man…
# ℹ 14 more rows
```

In the above code, we use the pipe to send the `xmas_movies` dataset first
through `filter()` to keep rows where `rating` is "PG-13", then through
`select()` to keep only the `title` and `description` columns. Since `%>%`
takes the object on its left and passes it as the first argument to the function
on its right, we don't need to explicitly include the dataframe as an argument
to the `filter()` and `select()` functions any more.

Some may find it helpful to read the pipe like the word "then". For instance,
in the above example, we take the dataframe `xmas_movies`, *then* we `filter`
for rows with `rating == "PG-13"`, *then* we `select` columns `title` and
`description`. The **`dplyr`** functions by themselves are somewhat simple,
but by combining them into linear workflows with the pipe, we can accomplish
more complex data wrangling operations.

If we want to create a new object with this smaller version of the data, we
can assign it a new name:


``` r
xmas_movies_ch <- xmas_movies %>%
    filter(rating == "PG-13") %>%
    select(title,description)

xmas_movies_ch
```

``` output
# A tibble: 24 × 2
   title                                 description                            
   <chr>                                 <chr>                                  
 1 National Lampoon's Christmas Vacation The Griswold family's plans for a big …
 2 Scrooged                              A selfish, cynical television executiv…
 3 Spirited                              A musical version of Charles Dickens's…
 4 Last Christmas                        Kate is a young woman subscribed to ba…
 5 Four Christmases                      A couple struggles to visit all four o…
 6 Happiest Season                       A holiday romantic comedy that capture…
 7 Shazam!                               A newly fostered young boy in search o…
 8 Surviving Christmas                   A lonely, obnoxious young millionaire …
 9 Batman Returns                        While Batman deals with a deformed man…
10 Edward Scissorhands                   The solitary life of an artificial man…
# ℹ 14 more rows
```

Note that the final dataframe (`xmas_movies_ch`) is the leftmost part of this
expression.


:::: challenge


## Exercise

Using pipes, subset the `xmas_movies` data set to include movies that
have a `release_year` greater than 1980 and retain only the columns `title`,
 `runtime`, and `rating`.
 
::::

:::: solution

## Solution


``` r
xmas_movies %>%
     filter(release_year > 1980) %>%
     select(title, runtime, rating)
```

``` output
# A tibble: 786 × 3
   title                                 runtime rating
   <chr>                                   <dbl> <chr> 
 1 Love Actually                             135 R     
 2 Home Alone                                103 PG    
 3 National Lampoon's Christmas Vacation      97 PG-13 
 4 Elf                                        97 PG    
 5 How the Grinch Stole Christmas            104 PG    
 6 The Grinch                                 85 PG    
 7 Die Hard                                  132 R     
 8 Home Alone 2: Lost in New York            120 PG    
 9 The Polar Express                         100 G     
10 The Santa Clause                           97 PG    
# ℹ 776 more rows
```

::::


## Mutate

Frequently you'll want to create new columns based on the values in existing
columns, for example to do unit conversions, or to find the ratio of values in
two columns. For this we'll use `mutate()`.

We might be interested in knowing the differences in scores on imdb vs tmdb:


``` r
xmas_movies %>%
  mutate(score_difference = imdb_rating - tmdb_rating) %>% 
  select(imdb_rating, tmdb_rating, score_difference)
```

``` output
# A tibble: 873 × 3
   imdb_rating tmdb_rating score_difference
         <dbl>       <dbl>            <dbl>
 1         7.6         4.3            3.3  
 2         7.7         8.8           -1.10 
 3         7.5         7.1            0.400
 4         7.1         6              1.1  
 5         6.3         2.5            3.8  
 6         6.4         2.5            3.9  
 7         8.2         1.8            6.4  
 8         6.9         8.1           -1.2  
 9         6.6         6              0.600
10         8.6         6.9            1.7  
# ℹ 863 more rows
```


:::: challenge 

## Exercise

Create a new dataframe from the `xmas_movies` data set that meets the following
criteria: contains only the `title` column and a new column called
`total_score` containing a value that is equal to the total number of scores on 
both imdb and tmdb (`imdb_rating` plus `tmdb_rating`).
Only the rows where `total_score` is greater than 15 should be shown in the
final dataframe.

**Hint**: think about how the commands should be ordered to produce the data
frame!

:::: solution

## Solution


``` r
xmas_movies_total_score <- xmas_movies %>%
  mutate(total_score = imdb_rating + tmdb_rating) %>%
  filter(total_score > 15) %>%
  select(title, total_score)
```

::::::::
::::

## Split-apply-combine data analysis and the summarize() function

Many data analysis tasks can be approached using the *split-apply-combine*
paradigm: split the data into groups, apply some analysis to each group, and
then combine the results. **`dplyr`** makes this very easy through the use of
the `group_by()` function.


#### The `summarize()` function

`group_by()` is often used together with `summarize()`, which collapses each
group into a single-row summary of that group. `group_by()` takes as arguments
the column names that contain the **categorical** variables for which you want
to calculate the summary statistics. So to compute the average imdb_rating by
genre:


``` r
xmas_movies %>%
    group_by(genre_1) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE))
```

``` output
# A tibble: 19 × 2
   genre_1     mean_imdb_rating
   <chr>                  <dbl>
 1 Action                  6.6 
 2 Adventure               5.93
 3 Animation               6.52
 4 Biography               6.82
 5 Comedy                  5.99
 6 Crime                   6.4 
 7 Documentary           NaN   
 8 Drama                   6.34
 9 Family                  5.73
10 Fantasy                 6.2 
11 Film-Noir               6.6 
12 Horror                  5.57
13 Music                   6.67
14 Musical                 6.3 
15 Mystery                 6.75
16 News                  NaN   
17 Romance                 6.02
18 Short                   5.28
19 <NA>                    5.9 
```

You may also have noticed that the output from these calls doesn't run off the
screen anymore. It's one of the advantages of `tbl_df` over dataframe.

You can also group by multiple columns:


``` r
xmas_movies %>%
    group_by(genre_1, rating) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE))
```

``` output
`summarise()` has grouped output by 'genre_1'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 74 × 3
# Groups:   genre_1 [19]
   genre_1   rating   mean_imdb_rating
   <chr>     <chr>               <dbl>
 1 Action    PG                   4.6 
 2 Action    PG-13                7.05
 3 Action    R                    7.05
 4 Action    <NA>                 6.8 
 5 Adventure Approved             7.1 
 6 Adventure G                    5.25
 7 Adventure PG                   6.41
 8 Adventure Passed               8.1 
 9 Adventure R                    6.2 
10 Adventure TV-G                 5.9 
# ℹ 64 more rows
```

Note that the output is a grouped tibble. To obtain an ungrouped tibble, use the
`ungroup` function:


``` r
xmas_movies %>%
    group_by(genre_1, rating) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE)) %>%
    ungroup()
```

``` output
`summarise()` has grouped output by 'genre_1'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 74 × 3
   genre_1   rating   mean_imdb_rating
   <chr>     <chr>               <dbl>
 1 Action    PG                   4.6 
 2 Action    PG-13                7.05
 3 Action    R                    7.05
 4 Action    <NA>                 6.8 
 5 Adventure Approved             7.1 
 6 Adventure G                    5.25
 7 Adventure PG                   6.41
 8 Adventure Passed               8.1 
 9 Adventure R                    6.2 
10 Adventure TV-G                 5.9 
# ℹ 64 more rows
```



Once the data are grouped, you can also summarize multiple variables at the same
time (and not necessarily on the same variable). For instance, we could add a
column indicating the maximum imdb_rating given to a movie or serie:


``` r
xmas_movies %>%
    group_by(genre_1, rating) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE),
              max_imdb_rating = max(imdb_rating, na.rm = TRUE))
```

``` warning
Warning: There were 3 warnings in `summarize()`.
The first warning was:
ℹ In argument: `max_imdb_rating = max(imdb_rating, na.rm = TRUE)`.
ℹ In group 38: `genre_1 = "Documentary"` `rating = NA`.
Caused by warning in `max()`:
! no non-missing arguments to max; returning -Inf
ℹ Run `dplyr::last_dplyr_warnings()` to see the 2 remaining warnings.
```

``` output
`summarise()` has grouped output by 'genre_1'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 74 × 4
# Groups:   genre_1 [19]
   genre_1   rating   mean_imdb_rating max_imdb_rating
   <chr>     <chr>               <dbl>           <dbl>
 1 Action    PG                   4.6              4.6
 2 Action    PG-13                7.05             7.1
 3 Action    R                    7.05             8.2
 4 Action    <NA>                 6.8              6.8
 5 Adventure Approved             7.1              7.1
 6 Adventure G                    5.25             5.7
 7 Adventure PG                   6.41             7.1
 8 Adventure Passed               8.1              8.1
 9 Adventure R                    6.2              6.2
10 Adventure TV-G                 5.9              6.5
# ℹ 64 more rows
```

It is sometimes useful to rearrange the result of a query to inspect the values.
For instance, we can sort on `min_imdb_rating` to put the group with the smallest
imdb_rating first:



``` r
xmas_movies %>%
    group_by(genre_1, rating) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE),
              max_imdb_rating = max(imdb_rating, na.rm = TRUE)) %>%
    arrange(max_imdb_rating)
```

``` warning
Warning: There were 3 warnings in `summarize()`.
The first warning was:
ℹ In argument: `max_imdb_rating = max(imdb_rating, na.rm = TRUE)`.
ℹ In group 38: `genre_1 = "Documentary"` `rating = NA`.
Caused by warning in `max()`:
! no non-missing arguments to max; returning -Inf
ℹ Run `dplyr::last_dplyr_warnings()` to see the 2 remaining warnings.
```

``` output
`summarise()` has grouped output by 'genre_1'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 74 × 4
# Groups:   genre_1 [19]
   genre_1     rating mean_imdb_rating max_imdb_rating
   <chr>       <chr>             <dbl>           <dbl>
 1 Documentary <NA>             NaN             -Inf  
 2 News        TV-G             NaN             -Inf  
 3 News        <NA>             NaN             -Inf  
 4 Family      TV-Y               2.9              2.9
 5 Romance     PG                 4.4              4.4
 6 Action      PG                 4.6              4.6
 7 Adventure   G                  5.25             5.7
 8 Crime       TV-PG              5.7              5.7
 9 Fantasy     <NA>               5.8              5.8
10 <NA>        <NA>               5.9              5.9
# ℹ 64 more rows
```

To sort in descending order, we need to add the `desc()` function. If we want to
sort the results by decreasing order of minimum imdb_rating:


``` r
xmas_movies %>%
    group_by(genre_1, rating) %>%
    summarize(mean_imdb_rating = mean(imdb_rating, na.rm = TRUE),
              max_imdb_rating = max(imdb_rating, na.rm = TRUE)) %>%
    arrange(desc(max_imdb_rating))
```

``` warning
Warning: There were 3 warnings in `summarize()`.
The first warning was:
ℹ In argument: `max_imdb_rating = max(imdb_rating, na.rm = TRUE)`.
ℹ In group 38: `genre_1 = "Documentary"` `rating = NA`.
Caused by warning in `max()`:
! no non-missing arguments to max; returning -Inf
ℹ Run `dplyr::last_dplyr_warnings()` to see the 2 remaining warnings.
```

``` output
`summarise()` has grouped output by 'genre_1'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 74 × 4
# Groups:   genre_1 [19]
   genre_1   rating   mean_imdb_rating max_imdb_rating
   <chr>     <chr>               <dbl>           <dbl>
 1 Comedy    TV-MA                7.14             9.2
 2 Comedy    <NA>                 5.76             9  
 3 Drama     PG                   6.88             8.6
 4 Music     <NA>                 6.9              8.6
 5 Drama     TV-G                 6.43             8.5
 6 Family    <NA>                 5.35             8.5
 7 Animation <NA>                 6.38             8.4
 8 Comedy    TV-Y                 6.45             8.4
 9 Animation TV-G                 6.24             8.3
10 Comedy    Approved             6.81             8.3
# ℹ 64 more rows
```

#### Counting

When working with data, we often want to know the number of observations found
for each factor or combination of factors. For this task, **`dplyr`** provides
`count()`. For example, if we wanted to count the number of rows of data for
each village, we would do:


``` r
xmas_movies %>%
    count(release_year)
```

``` output
# A tibble: 83 × 2
   release_year     n
          <dbl> <int>
 1         1898     1
 2         1925     2
 3         1928     1
 4         1931     1
 5         1934     2
 6         1935     1
 7         1936     2
 8         1937     1
 9         1938     3
10         1939     3
# ℹ 73 more rows
```

For convenience, `count()` provides the `sort` argument to get results in
decreasing order:


``` r
xmas_movies %>%
    count(release_year, sort = TRUE)
```

``` output
# A tibble: 83 × 2
   release_year     n
          <dbl> <int>
 1         2020    88
 2         2022    77
 3         2019    73
 4         2021    72
 5         2018    69
 6         2017    49
 7         2023    45
 8         2016    40
 9         2015    37
10         2014    31
# ℹ 73 more rows
```


::::::::::::::::::::::::::::::::::::: challenge

## Exercise

1. How many moview and series are there for each rating?

2. Use `group_by()` and `summarize()` to find the mean, min, and max for 
imdb_rating. Also add the number of observations (hint: see `?n`).

:::::::::::::::::::::::: solution

## Solution 1


``` r
 xmas_movies %>%
  count(rating)
```

``` output
# A tibble: 13 × 2
   rating       n
   <chr>    <int>
 1 Approved    20
 2 G           29
 3 PG          72
 4 PG-13       24
 5 Passed      18
 6 R           15
 7 TV-14       16
 8 TV-G       262
 9 TV-MA        5
10 TV-PG      131
11 TV-Y         4
12 Unrated     10
13 <NA>       267
```

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2


``` r
xmas_movies %>%
  group_by(genre_1) %>%
   summarize(
       mean_tmdb_rating = mean(tmdb_rating, na.rm = TRUE),
       min_tmdb_rating = min(tmdb_rating, na.rm = TRUE),
       max_tmdb_rating = max(tmdb_rating, na.rm = TRUE),
       n = n()
   )
```

``` output
# A tibble: 19 × 5
   genre_1     mean_tmdb_rating min_tmdb_rating max_tmdb_rating     n
   <chr>                  <dbl>           <dbl>           <dbl> <int>
 1 Action                  4.85             1.8             8.5     8
 2 Adventure               4.68             1.6             8.5    25
 3 Animation               5.15             1.3             9.1    47
 4 Biography               8.25             6.8             9       4
 5 Comedy                  5.06             1.3             9.2   440
 6 Crime                   6.25             2               9       6
 7 Documentary             5.5              4.6             6.4     2
 8 Drama                   5.26             1.4             9.1   208
 9 Family                  6.08             1.7             9.1    33
10 Fantasy                 6.9              5.2             8.6     2
11 Film-Noir               6                6               6       1
12 Horror                  5.9              2.5             8.7     3
13 Music                   5.6              3.5             7.9     3
14 Musical                 3.67             2.1             4.9     3
15 Mystery                 5.15             3.2             7.1     2
16 News                    5.43             2.5             8.1     3
17 Romance                 5.72             1.4             9.1    65
18 Short                   5.06             1.8             9.2    17
19 <NA>                    4.7              4.7             4.7     1
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

## Exporting data

Now that you have learned how to use **`dplyr`** to extract information from
or summarize your raw data, you may want to export these new data sets to share
them with your collaborators or for archival.

Similar to the `read_csv()` function used for reading CSV files into R, there is
a `write_csv()` function that generates CSV files from dataframes.

Before using `write_csv()`, we are going to create a new folder, `data_output`,
in our working directory that will store this generated dataset. We don't want
to write generated datasets in the same directory as our raw data. It's good
practice to keep them separate. The `data` folder should only contain the raw,
unaltered data, and should be left alone to make sure we don't delete or modify
it. In contrast, our script will generate the contents of the `data_output`
directory, so even if the files it contains are deleted, we can always
re-generate them.

Now we can save this dataframe to our `data_output` directory.


``` r
write_csv(xmas_movies_ch, file = "data_output/xmas_movies_changed.csv")
```



:::: keypoints

- Use the `dplyr` package to manipulate dataframes.
- Use `select()` to choose variables from a dataframe.
- Use `filter()` to choose data based on values.
- Use `group_by()` and `summarize()` to work with subsets of data.
- Use `mutate()` to create new variables.
- Use the `tidyr` package to change the layout of dataframes.
- Use `pivot_wider()` to go from long to wide format.
- Use `pivot_longer()` to go from wide to long format.

::::
