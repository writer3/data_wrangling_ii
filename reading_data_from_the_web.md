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

Learning asssessment: Create a data frame that contains the cost of
living table for New York from
<https://www.bestplaces.net/cost_of_living/city/new_york/new_york>.

``` r
nyc_cost_df =
  read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") |> 
  html_table(header = TRUE) |> #header says first row is header
  first()
```

## CSS Selectors

``` r
url = "https://www.imdb.com/list/ls070150896/"

swm_html = read_html(url)
```

How to figure out which pieces from html I want. 1. Selector gadget 2.
pick movie title (green) 3. clear something else you dont want (red) 4.
At the bottom of web page, you’ll see the full CSS tag that you copy and
paste into html_element function

``` r
swm_html |> 
  html_elements(".ipc-title-link-wrapper .ipc-title__text") |> 
  html_text() #extract text from the element above
```

    ## [1] "1. Star Wars: Episode I - The Phantom Menace"     
    ## [2] "2. Star Wars: Episode II - Attack of the Clones"  
    ## [3] "3. Star Wars: Episode III - Revenge of the Sith"  
    ## [4] "4. Star Wars: Episode IV - A New Hope"            
    ## [5] "5. Star Wars: Episode V - The Empire Strikes Back"
    ## [6] "6. Star Wars: Episode VI - Return of the Jedi"    
    ## [7] "7. Star Wars: Episode VII - The Force Awakens"    
    ## [8] "8. Star Wars: Episode VIII - The Last Jedi"       
    ## [9] "9. Star Wars: Episode IX - The Rise of Skywalker"

``` r
swm_html |> 
  html_elements(".dli-title-metadata-item:nth-child(2)") |> 
  html_text()
```

    ## [1] "2h 16m" "2h 22m" "2h 20m" "2h 1m"  "2h 4m"  "2h 11m" "2h 18m" "2h 32m"
    ## [9] "2h 21m"

``` r
swm_html |> 
  html_elements(".metacritic-score-box") |> 
  html_text()
```

    ## [1] "51" "54" "68" "90" "82" "58" "80" "84" "53"

It would look like this to make a table:

``` r
title_vec = 
  swm_html |>
  html_elements(".ipc-title-link-wrapper .ipc-title__text") |>
  html_text()

metascore_vec = 
  swm_html |>
  html_elements(".metacritic-score-box") |>
  html_text()

runtime_vec = 
  swm_html |>
  html_elements(".dli-title-metadata-item:nth-child(2)") |>
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    score = metascore_vec,
    runtime = runtime_vec
  )
```

Learning assessment: This page contains some (made up) books. Use a
process similar to the one above to extract the book titles, stars, and
price from <https://books.toscrape.com/>

``` r
books_html = read_html("https://books.toscrape.com/")

books_html |> 
  html_elements(".product_pod a") |>
  html_text()
```

    ##  [1] ""                                     
    ##  [2] "A Light in the ..."                   
    ##  [3] ""                                     
    ##  [4] "Tipping the Velvet"                   
    ##  [5] ""                                     
    ##  [6] "Soumission"                           
    ##  [7] ""                                     
    ##  [8] "Sharp Objects"                        
    ##  [9] ""                                     
    ## [10] "Sapiens: A Brief History ..."         
    ## [11] ""                                     
    ## [12] "The Requiem Red"                      
    ## [13] ""                                     
    ## [14] "The Dirty Little Secrets ..."         
    ## [15] ""                                     
    ## [16] "The Coming Woman: A ..."              
    ## [17] ""                                     
    ## [18] "The Boys in the ..."                  
    ## [19] ""                                     
    ## [20] "The Black Maria"                      
    ## [21] ""                                     
    ## [22] "Starving Hearts (Triangular Trade ..."
    ## [23] ""                                     
    ## [24] "Shakespeare's Sonnets"                
    ## [25] ""                                     
    ## [26] "Set Me Free"                          
    ## [27] ""                                     
    ## [28] "Scott Pilgrim's Precious Little ..."  
    ## [29] ""                                     
    ## [30] "Rip it Up and ..."                    
    ## [31] ""                                     
    ## [32] "Our Band Could Be ..."                
    ## [33] ""                                     
    ## [34] "Olio"                                 
    ## [35] ""                                     
    ## [36] "Mesaerion: The Best Science ..."      
    ## [37] ""                                     
    ## [38] "Libertarianism for Beginners"         
    ## [39] ""                                     
    ## [40] "It's Only the Himalayas"

## Use API

API knows we are asking for data.

Get water data. 1.
<https://data.cityofnewyork.us/Environment/Water-Consumption-in-the-City-of-New-York/ia2d-e54m/about_data>
2. Actions 3. API 4. API endpoint -\> data format to CSV 5. Copy API
endpoint

``` r
nyc_water =
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |>   content() #program knows it's csv and outputs just the data
```

    ## Rows: 45 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Get Behavioral Risk Factor study data

``` r
brfss_df = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) |> #breaking the 1000 row limit to 5000 to get more data 
  content()
```

    ## Rows: 5000 Columns: 23
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Pokemon API

For Bulbasaur

``` r
poke = 
  GET("http://pokeapi.co/api/v2/pokemon/1") |>
  content()

poke[["name"]]
```

    ## [1] "bulbasaur"
