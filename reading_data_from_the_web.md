Reading Data From the Web
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
library(httr)
```

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

``` r
marj_use_df = 
  drug_use_html |> 
  html_table() |> 
  first() |> #takes first element out of the table or first table, can type in command line ?first to see if you can import an nth function to specify other elements
  slice(-1) #subtracts first row
```

Learning asssessment: import cost of living score table from
<https://www.bestplaces.net/>.

``` r
nyc_cost_df =
  read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") |> 
  html_table(header = TRUE) |> #header says first row is header
  first()
```
