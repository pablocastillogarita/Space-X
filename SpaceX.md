SpaceX Launch Success Analysis with R
================
Pablo Castillo Garita
2024-06-25

## 1. GET API

``` r
library(httr)
library(ggplot2)
library(forcats)
library(purrr)
library(lubridate)

url <- "https://api.spacexdata.com/v4/launches"
parameters = list(format = "json")
api_call = GET(url = url, query = parameters)
response = content(api_call)
```

## 2. Create a Dataframe

``` r
launch_df <- map_df(
  response,
  ~data.frame(
    date = as.Date(.$date_utc),
    success = ifelse(is.null(.$success), FALSE, .$success),
    rocket_name = .$name
  )
)
```

## 3. Calculation of the Annual Success Rate of Launches

``` r
launch_df$year <- year(launch_df$date)

success_by_year <- aggregate(success ~ year, data = launch_df, FUN = function(x) sum(x) / length(x))
```

## 4. Graphic

``` r
ggplot(success_by_year, aes(x = year, y = success)) +
  geom_line(color = "blue") +
  geom_point(color = "blue") +
  labs(title = "Success Rate of SpaceX Launches Over Time", x = "Year", y = "Success Rate") +
  scale_y_continuous(labels = scales::percent_format()) +
  theme_minimal()
```

![](SpaceX_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
