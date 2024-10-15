Strings and Factors
================

Viridis

Load the necessary libraries

``` r
library(rvest) #facilitates web scraping
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(p8105.datasets)
```

## Let’s do strings

``` r
string_vec = c("my", "name", "is", "jeff")

str_detect(string_vec, "jeff") #looking for __ (case sensitive)
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_replace(string_vec, "jeff", "Jeff") #replaces strings that matches "jeff" to "Jeff"
```

    ## [1] "my"   "name" "is"   "Jeff"

``` r
str_replace(string_vec, "e", "E") #replaces strings that matches "e" to "E"
```

    ## [1] "my"   "namE" "is"   "jEff"

``` r
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun actually",
  "it will be fun, i think"
  )

str_detect(string_vec, "i think")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
str_detect(string_vec, "^i think") #to find the str that STARTS with "i think"
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
str_detect(string_vec, "i think$") #to find the str that ENDS with "i think"
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
string_vec = c(
  "Time for a Pumpkin Spice Latte!",
  "went to the #pumpkinpatch last weekend",
  "Pumpkin Pie is obviously the best pie",
  "SMASHING PUMPKINS -- LIVE IN CONCERT!!"
  )

str_detect(string_vec, "pumpkin")
```

    ## [1] FALSE  TRUE FALSE FALSE

``` r
str_detect(string_vec, "Pumpkin")
```

    ## [1]  TRUE FALSE  TRUE FALSE

``` r
str_detect(string_vec, "PUMPKIN")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_detect(string_vec,"[Pp]umpkin") #allow multiple options: either "P" or "p" and anything else after that to match "umpkin"
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )

#pattern shows a number followed by two letters on three of them

str_detect(string_vec, "[0-9][a-zA-Z]") #give me any number between 0 and 9 and letter a to z and A to Z
```

    ## [1]  TRUE  TRUE FALSE  TRUE

More patterns to detect…

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
  )

#pattern: something that matches 7 something 11, can use "." because it will detect anything between 7 and 11

str_detect(string_vec, "7.11")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

How things start to get real strange

``` r
string_vec = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on pages [6-7]'
  )

#say you're looking for special characters because by themselves there's a special meaning like "["?

str_detect(string_vec, "\\[") # "\" alone is a special character, so need "\\" before the special character you want to look for
```

    ## [1]  TRUE FALSE  TRUE  TRUE

## Factors …

``` r
sex_vec = factor(c("male", "male", "female", "female"))

#this automatically puts female first level alphabetically

as.numeric(sex_vec)
```

    ## [1] 2 2 1 1

do some releveling…

``` r
sex_vec = fct_relevel(sex_vec, "male") #can use relevel function to tell it which comes first

as.numeric(sex_vec)
```

    ## [1] 1 1 2 2

## Revisit a lot of examples

NSDUH

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

Get the pieces I actually need

``` r
marj_use_df = 
  drug_use_html |> 
  html_table() |> 
  first() |> #takes first element out of the table or first table, can type in command line ?first to see if you can import an nth function to specify other elements
  slice(-1) #subtracts first row
```

Get rid of p value Go from wide to long format Separate age and year Get
rid of parentheses Get rid of abc’s Change percent to numerical format

``` r
marj_use_df = 
  drug_use_html |> 
  html_table() |> 
  first() |> #takes first element out of the table or first table, can type in command line ?first to see if you can import an nth function to specify other elements
  slice(-1) |>  #subtracts first row
  select(-contains("P Value")) |> 
  pivot_longer(
    cols = -State,
    names_to = "age_year",
    values_to = "percent"
  ) |> 
  separate(age_year, into = c("age", "year"), sep = "\\(") |> 
  mutate(
    year = str_replace(year, "\\)", ""), #get rid of ")"
    percent = str_remove(percent, "[a-c]$"), #in the percent variable, remove "[a-c]" to get rid of abc's based on the dataset 
    percent = as.numeric(percent) #convert percent to numeric
  )
```

Plot the proportion of ppl that said “yes” only among kids who are age
12 - 17

``` r
marj_use_df |> 
  filter(age == "12-17") |> 
  mutate(
    State = fct_reorder(State, percent) #reorder State (categorical) using percent (numerical) bc without this the plots are all over the place
  ) |> 
  ggplot(aes(x = State, y = percent, color = year)) +
  geom_point() +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1)) #rotate the x-axis names by 90 degrees
```

<img src="strings_and_factors_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />
