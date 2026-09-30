# Getting Started with laOpenData

``` r

knitr::opts_chunk$set(warning = FALSE, message = FALSE)
library(laOpenData)
library(ggplot2)
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

## Introduction

Welcome to the `laOpenData` package, an R package dedicated to helping R
users connect to the [Los Angeles Open Data
Portal](https://data.lacity.org/)!

The `laOpenData` package provides a streamlined interface for accessing
Los Angeles’ vast open data resources. It connects directly to official
City of Los Angeles open data portals, including datasets hosted across
Socrata-powered city domains, helping users bridge the gap between raw
city APIs and tidy data analysis. This package is part of a broader
ecosystem of open data tools designed to provide a consistent interface
across cities. It does this in two ways:

### The `la_pull_dataset()` function

The primary way to pull data in this package is the
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
function, which works in tandem with
[`la_list_datasets()`](https://martinezc1.github.io/laOpenData/reference/la_list_datasets.md).
You do not need to know anything about API keys or authentication.

The first step would be to call the
[`la_list_datasets()`](https://martinezc1.github.io/laOpenData/reference/la_list_datasets.md)
to see what datasets are in the list and available to use in the
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
function. This provides information for thousands of datasets found on
the portal.

``` r

la_list_datasets() |> head()
#> # A tibble: 6 × 96
#>   key   id    name  attribution attributionLink category createdAt dataUpdatedAt
#>   <chr> <chr> <chr> <chr>       <chr>           <chr>    <chr>     <chr>        
#> 1 buil… dqq4… Buil… TSB         NA              Public … 2026-09-… 2026-09-29T1…
#> 2 buil… ke3z… Buil… TSB         NA              Public … 2026-08-… 2026-09-29T1…
#> 3 x202… hiwe… 2025… NA          NA              Transpo… 2026-07-… 2026-08-18T1…
#> 4 idle… g268… Idle… NA          NA              Audits … 2026-06-… 2026-06-24T2…
#> 5 buil… 9i2j… Buil… TSB         NA              City In… 2026-05-… 2026-09-27T0…
#> 6 x202… 5ypd… 2026… NA          NA              Communi… 2026-05-… 2026-07-13T1…
#> # ℹ 88 more variables: dataUri <chr>, description <chr>, domain <chr>,
#> #   externalId <lgl>, hideFromCatalog <lgl>, hideFromDataJson <lgl>,
#> #   license <chr>, metadataUpdatedAt <chr>, provenance <chr>, updatedAt <chr>,
#> #   webUri <chr>, approvals <list>, tags <list>,
#> #   `customFields.Committed Update Frequency.Refresh rate` <chr>,
#> #   `customFields.Automated?.Automated?` <chr>,
#> #   `customFields.Location Specified.Does this data have a Location column? (Yes or No)` <chr>, …
```

The output includes columns such as the dataset title, description, and
link to the source. The most important fields are the dataset `key` and
`id`. You need **either** in order to use the
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
function. You can put **either** the key value or id value into the
`dataset =` filter inside of
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md).

For instance, if we want to pull the dataset
`Building and Safety - Vacant Building Abatement`, we can use either of
the methods below:

``` r

la_building_safety <- la_pull_dataset(
  dataset = "i4em-afmq", limit = 2, timeout_sec = 90)

la_building_safety <- la_pull_dataset(
  dataset = "building_safety", limit = 2, timeout_sec = 90)
```

No matter if we put the `id` or the `key` as the value for `dataset =`,
we successfully get the data!

### The `la_any_dataset()` function

The easiest workflow is to use
[`la_list_datasets()`](https://martinezc1.github.io/laOpenData/reference/la_list_datasets.md)
together with
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md).

In the event that you have a particular dataset you want to use in R
that is not in the list, you can use the
[`la_any_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_any_dataset.md).
The only requirement is the dataset’s API endpoint (a URL provided by
the Los Angeles Open Data portal). Here are the steps to get it:

1.  On the Los Angeles Open Data Portal, go to the dataset you want to
    work with.
2.  Click on “Export” (next to the actions button on the right hand
    side).
3.  Click on “API Endpoint”.
4.  Click on “SODA2” for “Version”.
5.  Copy the API Endpoint.

Below is an example of how to use the
[`la_any_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_any_dataset.md)
once the API endpoint has been discovered, that will pull the same data
as the
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
example:

``` text
la_motor_vehicle_collisions_data <- la_any_dataset(json_link = "https://data.lacity.org/resource/q3ak-s5hy.json", limit = 2)
```

### Rule of Thumb

While both functions provide access to Los Angeles Open Data, they serve
slightly different purposes.

In general:

- Use
  [`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
  when the dataset is available in
  [`la_list_datasets()`](https://martinezc1.github.io/laOpenData/reference/la_list_datasets.md)
- Use
  [`la_any_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_any_dataset.md)
  when working with datasets outside the catalog

Together, these functions allow users to either quickly access the
datasets or flexibly query any dataset available on the Los Angeles Open
Data portal.

## Real World Example

Los Angeles has a lot of people, and just as many businesses, and the
list of active businesses in LA is contained in the dataset, [found
here](https://data.lacity.org/Administration-Finance/Listing-of-Active-Businesses/6rrh-rzua/about_data).
In R, the `laOpenData` package can be used to pull this data directly.

By using the
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
function, we can gather information on these businesses, and filter
based upon any of the columns inside the dataset.

Let’s take an example of 3 requests that occur in the actual city of Los
Angeles. The
[`la_pull_dataset()`](https://martinezc1.github.io/laOpenData/reference/la_pull_dataset.md)
function can filter based off any of the columns in the dataset. To
filter, we add `filters = list()` and put whatever filters we would like
inside. From our `colnames` call before, we know that there is a column
called “city” which we can use to accomplish this.

``` r


la_businesses <- la_pull_dataset(dataset = "6rrh-rzua",limit = 3, timeout_sec = 90, filters = list(city = "LOS ANGELES"))
la_businesses
#> # A tibble: 3 × 11
#>   location_account  business_name                  street_address city  zip_code
#>   <chr>             <chr>                          <chr>          <chr> <chr>   
#> 1 0002829017-0001-5 RICHARD JOHN SHERMAN           2010 LA BREA … LOS … 90046-2…
#> 2 0000111620-0001-4 SOUTHERN CALIFORNIA GRANTMAKE… 1000 N ALAMED… LOS … 90012-1…
#> 3 0003293756-0001-5 BHI RESIDENTIAL LONG TERM COR… 732 S SPRING … LOS … 90014-3…
#> # ℹ 6 more variables: location_description <chr>, council_district <dbl>,
#> #   location_start_date <dttm>, location_1_latitude <dbl>,
#> #   location_1_longitude <dbl>, location_1_human_address <chr>

# Checking to see the filtering worked
la_businesses |>
  distinct(city)
#> # A tibble: 1 × 1
#>   city       
#>   <chr>      
#> 1 LOS ANGELES
```

Success! From calling the `la_businesses` dataset we see there are only
3 rows of data, and from the
[`distinct()`](https://dplyr.tidyverse.org/reference/distinct.html) call
we see the only location featured in our dataset is LOS ANGELES.

One of the strongest qualities this function has is its ability to
filter based off of multiple columns. Let’s put everything together and
get a dataset of *50* businesses that occur in LOS ANGELES in council
district 8.

``` r

# Creating the dataset
la_businesses_8 <- la_pull_dataset(dataset = "6rrh-rzua", limit = 50, timeout_sec = 90, filters = list(city = "LOS ANGELES", council_district = 8))

# Calling head of our new dataset
la_businesses_8 |>
  slice_head(n = 6)
#> # A tibble: 6 × 17
#>   location_account  business_name         dba_name street_address city  zip_code
#>   <chr>             <chr>                 <chr>    <chr>          <chr> <chr>   
#> 1 0000688586-0001-8 G & P RECYCLING INC   G & P R… 1329 W JEFFER… LOS … 90007-3…
#> 2 0000609648-0002-5 LARRY & DARNELLA SCA… L AND D… 6715 2ND AVEN… LOS … 90043-4…
#> 3 0002865522-0001-3 DOLORES G AMAYA       NA       1601 W 46TH S… LOS … 90062-1…
#> 4 0002983638-0001-6 ANTHONY CANTERO       THE MEN… 4427 S NORMAN… LOS … 90037-2…
#> 5 0002109702-0001-5 FRANCISCO DIAZ        NA       1228 W 25TH S… LOS … 90007-1…
#> 6 0002277536-0001-3 SAUNDRA BISHOP TRUST  NA       627 W IMPERIA… LOS … 90044-4…
#> # ℹ 11 more variables: location_description <chr>, mailing_address <chr>,
#> #   mailing_city <chr>, mailing_zip_code <chr>, naics <dbl>,
#> #   primary_naics_description <chr>, council_district <dbl>,
#> #   location_start_date <dttm>, location_1_latitude <dbl>,
#> #   location_1_longitude <dbl>, location_1_human_address <chr>

# Quick check to make sure our filtering worked
la_businesses_8 |>
  summarize(rows = n())
#> # A tibble: 1 × 1
#>    rows
#>   <int>
#> 1    50

la_businesses_8 |>
  distinct(city)
#> # A tibble: 1 × 1
#>   city       
#>   <chr>      
#> 1 LOS ANGELES

la_businesses_8 |>
  distinct(council_district)
#> # A tibble: 1 × 1
#>   council_district
#>              <dbl>
#> 1                8
```

We successfully created our dataset that contains 50 requests regarding
the businesses in district 8 of LA.

### Mini analysis

Now that we have successfully pulled the data and have it in R, let’s do
a mini analysis on using the `primary_naics_description` column, to
figure out what are the main types of businesses

To do this, we will create a bar graph of the business types.

``` r

# Visualizing the distribution, ordered by frequency
la_businesses_8 |>
  count(primary_naics_description) |>
  ggplot(aes(
    x = n,
    y = reorder(primary_naics_description, n)
  )) +
  geom_col(fill = "steelblue") +
  theme_minimal() +
  labs(
    title = "Top 50 Business Types in District 8 of LA",
    x = "Number of Businesses",
    y = "Business Type"
  )
```

![Bar chart showing the frequency of business types in LA in district
8.](getting-started_files/figure-html/complaint-type-graph-1.png)

Bar chart showing the frequency of business types in LA in district 8.

This graph shows us not only *which* businesses are in the area, but
*how many* of each there are.

## Summary

The `laOpenData` package serves as a robust interface for the Los
Angeles Open Data portal, streamlining the path from raw city APIs to
actionable insights. By abstracting the complexities of data
acquisition—such as pagination, type-casting, and complex filtering—it
allows users to focus on analysis rather than data engineering.

As demonstrated in this vignette, the package provides a seamless
workflow for targeted data retrieval, automated filtering, and rapid
visualization.

## How to Cite

If you use this package for research or educational purposes, please
cite it as follows:

Martinez C (2026). laOpenData: Convenient Access to Los Angeles Open
Data API Endpoints. R package version 0.1.0,
<https://martinezc1.github.io/laOpenData/>.
