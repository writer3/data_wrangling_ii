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
drug_use_html |> 
  html_table() |> 
  first() #takes first element out of the table
```

    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "12.90a"         "13.36"          "0.002"        "13.28b"          
    ##  3 "Northea… "13.88a"         "14.66"          "0.005"        "13.98"           
    ##  4 "Midwest" "12.40b"         "12.76"          "0.082"        "12.45"           
    ##  5 "South"   "11.24a"         "11.64"          "0.029"        "12.02"           
    ##  6 "West"    "15.27"          "15.62"          "0.262"        "15.53a"          
    ##  7 "Alabama" "9.98"           "9.60"           "0.426"        "9.90"            
    ##  8 "Alaska"  "19.60a"         "21.92"          "0.010"        "17.30"           
    ##  9 "Arizona" "13.69"          "13.12"          "0.364"        "15.12"           
    ## 10 "Arkansa… "11.37"          "11.59"          "0.678"        "12.79"           
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
