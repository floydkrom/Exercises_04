---
title: 'Weekly Exercises #4'
author: "Floyd Krom"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    theme: journal
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for data cleaning and plotting
```

```
## ── Attaching packages ──────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
## ✓ readr   1.3.1     ✓ forcats 0.5.0
```

```
## ── Conflicts ─────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
```

```r
library(openintro)     # for the abbr2state() function
```

```
## Loading required package: airports
```

```
## Loading required package: cherryblossom
```

```
## Loading required package: usdata
```

```r
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
```

```
## 
## Attaching package: 'maps'
```

```
## The following object is masked from 'package:purrr':
## 
##     map
```

```r
library(ggmap)         # for mapping points on maps
```

```
## Google's Terms of Service: https://cloud.google.com/maps-platform/terms/.
```

```
## Please cite ggmap if you use it! See citation("ggmap") for details.
```

```r
library(gplots)        # for col2hex() function
```

```
## 
## Attaching package: 'gplots'
```

```
## The following object is masked from 'package:stats':
## 
##     lowess
```

```r
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
```

```
## Linking to GEOS 3.8.1, GDAL 3.1.1, PROJ 6.3.1
```

```r
library(leaflet)       # for highly customizable mapping
library(carData)       # for Minneapolis police stops data
library(ggthemes)      # for more themes (including theme_map())
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())
```


```r
# Starbucks locations
Starbucks <- read_csv("https://www.macalester.edu/~ajohns24/Data/Starbucks.csv")
```

```
## Parsed with column specification:
## cols(
##   Brand = col_character(),
##   `Store Number` = col_character(),
##   `Store Name` = col_character(),
##   `Ownership Type` = col_character(),
##   `Street Address` = col_character(),
##   City = col_character(),
##   `State/Province` = col_character(),
##   Country = col_character(),
##   Postcode = col_character(),
##   `Phone Number` = col_character(),
##   Timezone = col_character(),
##   Longitude = col_double(),
##   Latitude = col_double()
## )
```

```r
starbucks_us_by_state <- Starbucks %>% 
  filter(Country == "US") %>% 
  count(`State/Province`) %>% 
  mutate(state_name = str_to_lower(abbr2state(`State/Province`))) 

# Lisa's favorite St. Paul places - example for you to create your own data
favorite_stp_by_lisa <- tibble(
  place = c("Home", "Macalester College", "Adams Spanish Immersion", 
            "Spirit Gymnastics", "Bama & Bapa", "Now Bikes",
            "Dance Spectrum", "Pizza Luce", "Brunson's"),
  long = c(-93.1405743, -93.1712321, -93.1451796, 
           -93.1650563, -93.1542883, -93.1696608, 
           -93.1393172, -93.1524256, -93.0753863),
  lat = c(44.950576, 44.9378965, 44.9237914,
          44.9654609, 44.9295072, 44.9436813, 
          44.9399922, 44.9468848, 44.9700727)
  )

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")
```

```
## Parsed with column specification:
## cols(
##   date = col_date(format = ""),
##   state = col_character(),
##   fips = col_character(),
##   cases = col_double(),
##   deaths = col_double()
## )
```

## Warm-up exercises from tutorial

These exercises will reiterate what you learned in the "Mapping data with R" tutorial. If you haven't gone through the tutorial yet, you should do that first.

### Starbucks locations (`ggmap`)

  1. Add the `Starbucks` locations to a world map. Add an aesthetic to the world map that sets the color of the points according to the ownership type. What, if anything, can you deduce from this visualization?  
  

```r
world <- get_stamenmap(
    bbox = c(left = -180, bottom = -57, right = 179, top = 82.1), 
    maptype = "terrain",
    zoom = 2)
```

```
## Source : http://tile.stamen.com/terrain/2/0/0.png
```

```
## Source : http://tile.stamen.com/terrain/2/1/0.png
```

```
## Source : http://tile.stamen.com/terrain/2/2/0.png
```

```
## Source : http://tile.stamen.com/terrain/2/3/0.png
```

```
## Source : http://tile.stamen.com/terrain/2/0/1.png
```

```
## Source : http://tile.stamen.com/terrain/2/1/1.png
```

```
## Source : http://tile.stamen.com/terrain/2/2/1.png
```

```
## Source : http://tile.stamen.com/terrain/2/3/1.png
```

```
## Source : http://tile.stamen.com/terrain/2/0/2.png
```

```
## Source : http://tile.stamen.com/terrain/2/1/2.png
```

```
## Source : http://tile.stamen.com/terrain/2/2/2.png
```

```
## Source : http://tile.stamen.com/terrain/2/3/2.png
```

```r
ggmap(world) + 
  geom_point(data = Starbucks, 
             aes(x = Longitude, y = Latitude, color = `Ownership Type`), 
             alpha = .3, 
             size = .1) +
  theme_map() +
  theme(legend.background = element_blank())
```

```
## Warning: Removed 1 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

  2. Construct a new map of Starbucks locations in the Twin Cities metro area (approximately the 5 county metro area).  
  

```r
Twin_Cities <- get_stamenmap(
    bbox = c(left = -93.67, bottom = 44.75, right = -92.59, top = 45.18), 
    maptype = "terrain",
    zoom = 11)
```

```
## Source : http://tile.stamen.com/terrain/11/491/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/492/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/493/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/494/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/495/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/496/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/497/735.png
```

```
## Source : http://tile.stamen.com/terrain/11/491/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/492/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/493/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/494/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/495/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/496/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/497/736.png
```

```
## Source : http://tile.stamen.com/terrain/11/491/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/492/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/493/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/494/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/495/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/496/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/497/737.png
```

```
## Source : http://tile.stamen.com/terrain/11/491/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/492/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/493/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/494/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/495/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/496/738.png
```

```
## Source : http://tile.stamen.com/terrain/11/497/738.png
```

```r
ggmap(Twin_Cities) + 
  geom_point(data = Starbucks, 
             aes(x = Longitude, y = Latitude, color = `Ownership Type`), 
             size = 3) +
  theme_map() +
  theme(legend.background = element_blank())
```

```
## Warning: Removed 25469 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-2-1.png)<!-- -->
  
  3. In the Twin Cities plot, play with the zoom number. What does it do?  (just describe what it does - don't actually include more than one map).  
  
  The zoom number allows us to include more or less details in the plot. If the zoom number is small then it shows less detail,  if the zoom number is large then it shows more detail. 

  4. Try a couple different map types (see `get_stamenmap()` in help and look at `maptype`). Include a map with one of the other map types.  
  
  Different maptypes available with get_stamenmap: "terrain",
  "terrain-background", "terrain-labels", "terrain-lines", "toner",
  "toner-2010", "toner-2011", "toner-background", "toner-hybrid",
  "toner-labels", "toner-lines", "toner-lite", "watercolor"
  

```r
Twin_Cities <- get_stamenmap(
    bbox = c(left = -93.67, bottom = 44.75, right = -92.59, top = 45.18), 
    maptype = "toner-2011",
    zoom = 11)
```

```
## Source : http://tile.stamen.com/toner-2011/11/491/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/492/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/493/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/494/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/495/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/496/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/497/735.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/491/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/492/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/493/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/494/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/495/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/496/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/497/736.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/491/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/492/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/493/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/494/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/495/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/496/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/497/737.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/491/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/492/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/493/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/494/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/495/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/496/738.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/497/738.png
```

```r
ggmap(Twin_Cities) + 
  geom_point(data = Starbucks, 
             aes(x = Longitude, y = Latitude, color = `Ownership Type`), 
             size = 3) +
  theme_map() +
  theme(legend.background = element_blank())
```

```
## Warning: Removed 25469 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

  5. Add a point to the map that indicates Macalester College and label it appropriately. There are many ways you can do think, but I think it's easiest with the `annotate()` function (see `ggplot2` cheatsheet).
  

```r
Twin_Cities <- get_stamenmap(
    bbox = c(left = -93.67, bottom = 44.75, right = -92.59, top = 45.18), 
    maptype = "toner-2011",
    zoom = 11)

ggmap(Twin_Cities) + 
  geom_point(data = Starbucks, 
             aes(x = Longitude, y = Latitude, color = `Ownership Type`), 
             size = 3) +
  theme_map() +
  theme(legend.background = element_blank()) +
  annotate(geom = "text", x = -93.1712321, y = 44.9308890, label = "Macalester College") +
  annotate(geom = "point", x = -93.1712321, y = 44.9378965, color = "Dark Blue", size = 3)
```

```
## Warning: Removed 25469 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

### Choropleth maps with Starbucks data (`geom_map()`)

The example I showed in the tutorial did not account for population of each state in the map. In the code below, a new variable is created, `starbucks_per_10000`, that gives the number of Starbucks per 10,000 people. It is in the `starbucks_with_2018_pop_est` dataset.


```r
census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))
```

```
## Parsed with column specification:
## cols(
##   state = col_character(),
##   est_pop_2018 = col_double()
## )
```

```r
starbucks_with_2018_pop_est <-
  starbucks_us_by_state %>% 
  left_join(census_pop_est_2018,
            by = c("state_name" = "state")) %>% 
  mutate(starbucks_per_10000 = (n/est_pop_2018)*10000)
```

  6. **`dplyr` review**: Look through the code above and describe what each line of code does.
  
  Line : Reads in the US Census 2018 Estimated State Population Number 
  line : Separates the dot and the state from each other, then merges the state name. Due to the separation, the default would otherwise leave out the second word of a state with two words in its name, such as New Mexico and New Jersey
  line : Deletes the dot as a column
  line : Mutates all state names to lower cases 
  line : This line of code saves starbucks_us_by_state to starbucks_with_2018_pop_est
  line : Left join of the Census Population Estimate 2018 data set by state name
  line : We create a new variable called starbucks_per_1000 which first divides the total number of Starbucks in a state by the estimated population number in 2018, and then multiplies that number by 10,000. 

  7. Create a choropleth map that shows the number of Starbucks per 10,000 people on a map of the US. Use a new fill color, add points for all Starbucks in the US (except Hawaii and Alaska), add an informative title for the plot, and include a caption that says who created the plot (you!). Make a conclusion about what you observe.
  

```r
states_map <- map_data("state")

starbucks_with_2018_pop_est %>% 
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state_name,
               fill = starbucks_per_10000)) +
  geom_point(data = Starbucks %>% filter(!`State/Province` %in% c("HI", "AK"), `Country` == "US"),
             aes(x = Longitude, y = Latitude),
             size = .05,
             alpha = .2, 
             color = "goldenrod") +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  labs(title = "Starbucks in the United States",
       caption = "Created by Floyd Krom") +
  theme(legend.background = element_blank()) + 
  theme_map() +
  scale_fill_viridis_c()
```

![](04_exercises_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

### A few of your favorite things (`leaflet`)

  8. In this exercise, you are going to create a single map of some of your favorite places! The end result will be one map that satisfies the criteria below. 

  * Create a data set using the `tibble()` function that has 10-15 rows of your favorite places. The columns will be the name of the location, the latitude, the longitude, and a column that indicates if it is in your top 3 favorite locations or not. For an example of how to use `tibble()`, look at the `favorite_stp_by_lisa` I created in the data R code chunk at the beginning.  
  

```r
favorite_step_by_floyd <- tibble(
  place = c("Neighborhood Development Center", "Los Ocampo", "Allianz Field", "Home", "Stone Arch Bridge", "Trader Joe's", "Midtown Global Market", "Wakame Sushi & Asian Bistro", "Macalester College", "Starks Clips"),
  long = c(-93.128475, -93.125621, -93.165083, -93.246913, -93.253369, -93.258221, -93.260421, -93.321944, -93.1712321, -93.164284),
  lat = c(44.956134, 44.956154, 44.953209, 44.981674, 44.980943, 44.976722, 44.948709, 44.947059, 44.9378965, 44.940497),
  favorite_place = c("No", "No", "No", "No", "Yes", "No", "Yes", "No", "No", "Yes")
  )
```
  
  * Create a `leaflet` map that uses circles to indicate your favorite places. Label them with the name of the place. Choose the base map you like best. Color your 3 favorite places differently than the ones that are not in your top 3 (HINT: `colorFactor()`). Add a legend that explains what the colors mean.  
  

```r
  pal <- colorFactor(
  palette = c("#111D4A", "#5998C5"),
  domain = favorite_step_by_floyd$favorite_place)

leaflet(data = favorite_step_by_floyd) %>% 
  addProviderTiles(providers$CartoDB.DarkMatter)  %>% 
  addCircles(lng = ~long, 
             lat = ~lat, 
             label = ~place, 
             weight = 10, 
             opacity = 1, 
             color = ~pal(favorite_place)) %>% 
  addLegend(pal = pal, 
            values = ~favorite_place, 
            opacity = 1, 
            title = "Favorite Place",
            position = "bottomright") %>%
  addPolylines(lng = ~long, 
               lat = ~lat, 
               color = c("#829399"))
```

<!--html_preserve--><div id="htmlwidget-81651381d88100516e8c" style="width:672px;height:480px;" class="leaflet html-widget"></div>
<script type="application/json" data-for="htmlwidget-81651381d88100516e8c">{"x":{"options":{"crs":{"crsClass":"L.CRS.EPSG3857","code":null,"proj4def":null,"projectedBounds":null,"options":{}}},"calls":[{"method":"addProviderTiles","args":["CartoDB.DarkMatter",null,null,{"errorTileUrl":"","noWrap":false,"detectRetina":false}]},{"method":"addCircles","args":[[44.956134,44.956154,44.953209,44.981674,44.980943,44.976722,44.948709,44.947059,44.9378965,44.940497],[-93.128475,-93.125621,-93.165083,-93.246913,-93.253369,-93.258221,-93.260421,-93.321944,-93.1712321,-93.164284],10,null,null,{"interactive":true,"className":"","stroke":true,"color":["#111D4A","#111D4A","#111D4A","#111D4A","#5998C5","#111D4A","#5998C5","#111D4A","#111D4A","#5998C5"],"weight":10,"opacity":1,"fill":true,"fillColor":["#111D4A","#111D4A","#111D4A","#111D4A","#5998C5","#111D4A","#5998C5","#111D4A","#111D4A","#5998C5"],"fillOpacity":0.2},null,null,["Neighborhood Development Center","Los Ocampo","Allianz Field","Home","Stone Arch Bridge","Trader Joe's","Midtown Global Market","Wakame Sushi &amp; Asian Bistro","Macalester College","Starks Clips"],{"interactive":false,"permanent":false,"direction":"auto","opacity":1,"offset":[0,0],"textsize":"10px","textOnly":false,"className":"","sticky":true},null,null]},{"method":"addLegend","args":[{"colors":["#111D4A","#5998C5"],"labels":["No","Yes"],"na_color":null,"na_label":"NA","opacity":1,"position":"bottomright","type":"factor","title":"Favorite Place","extra":null,"layerId":null,"className":"info legend","group":null}]},{"method":"addPolylines","args":[[[[{"lng":[-93.128475,-93.125621,-93.165083,-93.246913,-93.253369,-93.258221,-93.260421,-93.321944,-93.1712321,-93.164284],"lat":[44.956134,44.956154,44.953209,44.981674,44.980943,44.976722,44.948709,44.947059,44.9378965,44.940497]}]]],null,null,{"interactive":true,"className":"","stroke":true,"color":"#829399","weight":5,"opacity":0.5,"fill":false,"fillColor":"#829399","fillOpacity":0.2,"smoothFactor":1,"noClip":false},null,null,null,{"interactive":false,"permanent":false,"direction":"auto","opacity":1,"offset":[0,0],"textsize":"10px","textOnly":false,"className":"","sticky":true},null]}],"limits":{"lat":[44.9378965,44.981674],"lng":[-93.321944,-93.125621]}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
  
## Revisiting old datasets

This section will revisit some datasets we have used previously and bring in a mapping component. 

### Bicycle-Use Patterns

The data come from Washington, DC and cover the last quarter of 2014.

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usualy, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`. This code reads in the large dataset right away.


```r
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

```
## Parsed with column specification:
## cols(
##   name = col_character(),
##   lat = col_double(),
##   long = col_double(),
##   nbBikes = col_double(),
##   nbEmptyDocks = col_double()
## )
```

  9. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. This time, plot the points on top of a map. Use any of the mapping tools you'd like.
  

```r
Stations2 <- Stations %>%
  left_join(Trips,
  by = c("name" = "sstation")) %>%
  group_by(long, lat) %>% 
  summarize(total_departures = n())
```

```
## `summarise()` regrouping output by 'long' (override with `.groups` argument)
```

```r
Washington_DC <- get_stamenmap(
    bbox = c(left = -77.1732, bottom = 38.8159, right = -76.7990, top = 39.0058), 
    maptype = "toner-2011",
    zoom = 11)
```

```
## Source : http://tile.stamen.com/toner-2011/11/584/782.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/585/782.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/586/782.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/587/782.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/584/783.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/585/783.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/586/783.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/587/783.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/584/784.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/585/784.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/586/784.png
```

```
## Source : http://tile.stamen.com/toner-2011/11/587/784.png
```

```r
ggmap(Washington_DC) + 
  geom_point(data = Stations2, 
             aes(x = long, y = lat, color = total_departures), 
             size = 3) +
  theme_map() +
  theme(legend.background = element_blank())
```

```
## Warning: Removed 32 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
  scale_color_viridis_c()
```

```
## <ScaleContinuous>
##  Range:  
##  Limits:    0 --    1
```
  
  10. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? Also plot this on top of a map. I think it will be more clear what the patterns are.
  

```r
Stations2 <- Stations %>%
  left_join(Trips,
  by = c("name" = "sstation")) %>%
  group_by(long, lat) %>% 
  summarize(percent_casual= mean(client == "Casual")) 
```

```
## `summarise()` regrouping output by 'long' (override with `.groups` argument)
```

```r
Washington_DC <- get_stamenmap(
    bbox = c(left = -77.1732, bottom = 38.8159, right = -76.7990, top = 39.0058), 
    maptype = "toner-2011",
    zoom = 11)

ggmap(Washington_DC) + 
  geom_point(data = Stations2, 
             aes(x = long, y = lat, color = percent_casual), 
             size = 3) +
  theme_map() +
  scale_color_viridis_c()
```

```
## Warning: Removed 32 rows containing missing values (geom_point).
```

![](04_exercises_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
  As I guessed in problem set 3, there is a high percentage of casual riders that rent out their bikes from stations in the downtown area, especially along the river. It is very common for tourists exploring the city, which can be identified as casual riders, to take a bike ride along a scenic river route. This therefore explains the high percentage of casual riders at these stations in the downtown area and along the river side of the downtown area. 
  
### COVID-19 data

The following exercises will use the COVID-19 data from the NYT.

  11. Create a map that colors the states by the most recent cumulative number of COVID-19 cases (remember, these data report cumulative numbers so you don't need to compute that). Describe what you see. What is the problem with this map?
  

```r
states_map <- map_data("state")

covid19 %>% 
  group_by(state) %>%
  summarize(cumulative_cases = max(cases)) %>%
  mutate(state = str_to_lower(state)) %>%
  ggplot(aes(fill = cumulative_cases)) +
  geom_map(map = states_map,
           aes(map_id = state)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  labs(title = "Cumulative COVID-19 cases in the United States") +
  theme(legend.background = element_blank()) + 
  theme_map() +
  scale_fill_viridis_c()
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

![](04_exercises_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
In this visualization, the number of COVID-19 cases are not proportional to the population number in each state, therefore this visualization is not necessarily a fair way to compare states. 

  12. Now add the population of each state to the dataset and color the states by most recent cumulative cases/10,000 people. See the code for doing this with the Starbucks data. You will need to make some modifications. 
  

```r
covid19_population <-
  covid19 %>% 
  mutate(state = str_to_lower(state)) %>%
  left_join(census_pop_est_2018,
            by = "state") %>% 
  group_by(state, est_pop_2018) %>%
  summarize(cumulative_cases = max(cases)) %>%
  mutate(cases_per_10000 = (cumulative_cases/est_pop_2018)*10000)
```

```
## `summarise()` regrouping output by 'state' (override with `.groups` argument)
```

```r
states_map <- map_data("state")

covid19_population %>% 
  mutate(state = str_to_lower(state)) %>%
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state, fill = cases_per_10000)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  labs(title = "Cumulative COVID-19 cases per 10,000 people in the United States") +
  theme(legend.background = element_blank()) + 
  theme_map() +
  scale_fill_viridis_c()
```

![](04_exercises_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
  
  13. **CHALLENGE** Choose 4 dates spread over the time period of the data and create the same map as in exercise 12 for each of the dates. Display the four graphs together using faceting. What do you notice?
  

```r
covid19_date <-
  covid19 %>% 
  filter(date %in% ymd(c("2020-04-04", "2020-06-14", "2020-07-17", "2020-09-25"))) %>% 
  mutate(state = str_to_lower(state)) %>%
  left_join(census_pop_est_2018,
            by = "state") %>% 
  mutate(cases_per_10000 = (cases/est_pop_2018)*10000)

covid19_date %>%
  mutate(state = str_to_lower(state)) %>%
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state, fill = cases_per_10000)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  facet_wrap(~date) +
  labs(title = "Cumulative COVID-19 cases per 10,000 people in the United States") +
  theme_map() +
  theme(legend.background = element_blank()) +
  scale_fill_viridis_c()
```

![](04_exercises_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
  
  
## Minneapolis police stops

This exercise uses the datasets `MplsStops` and `MplsDemo` from the `carData` library. Search for them in Help to find out more information.

  14. Use the `MplsStops` dataset to find out how many stops there were for each neighborhood and the proportion of stops that were for a suspicious vehicle or person. Sort the results from most to least number of stops. Save this as a dataset called `mpls_suspicious` and display the table.  
  

```r
MplsStops %>%
  group_by(neighborhood) %>%
  count(problem)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["neighborhood"],"name":[1],"type":["fctr"],"align":["left"]},{"label":["problem"],"name":[2],"type":["fctr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"Armatage","2":"suspicious","3":"65"},{"1":"Armatage","2":"traffic","3":"12"},{"1":"Audubon Park","2":"suspicious","3":"206"},{"1":"Audubon Park","2":"traffic","3":"348"},{"1":"Bancroft","2":"suspicious","3":"113"},{"1":"Bancroft","2":"traffic","3":"21"},{"1":"Beltrami","2":"suspicious","3":"53"},{"1":"Beltrami","2":"traffic","3":"158"},{"1":"Bottineau","2":"suspicious","3":"96"},{"1":"Bottineau","2":"traffic","3":"281"},{"1":"Bryant","2":"suspicious","3":"77"},{"1":"Bryant","2":"traffic","3":"19"},{"1":"Bryn - Mawr","2":"suspicious","3":"78"},{"1":"Bryn - Mawr","2":"traffic","3":"47"},{"1":"Camden Industrial","2":"suspicious","3":"12"},{"1":"Camden Industrial","2":"traffic","3":"22"},{"1":"CARAG","2":"suspicious","3":"234"},{"1":"CARAG","2":"traffic","3":"325"},{"1":"Cedar - Isles - Dean","2":"suspicious","3":"54"},{"1":"Cedar - Isles - Dean","2":"traffic","3":"99"},{"1":"Cedar Riverside","2":"suspicious","3":"563"},{"1":"Cedar Riverside","2":"traffic","3":"262"},{"1":"Central","2":"suspicious","3":"528"},{"1":"Central","2":"traffic","3":"304"},{"1":"Cleveland","2":"suspicious","3":"206"},{"1":"Cleveland","2":"traffic","3":"150"},{"1":"Columbia Park","2":"suspicious","3":"64"},{"1":"Columbia Park","2":"traffic","3":"87"},{"1":"Como","2":"suspicious","3":"138"},{"1":"Como","2":"traffic","3":"314"},{"1":"Cooper","2":"suspicious","3":"56"},{"1":"Cooper","2":"traffic","3":"56"},{"1":"Corcoran","2":"suspicious","3":"220"},{"1":"Corcoran","2":"traffic","3":"140"},{"1":"Diamond Lake","2":"suspicious","3":"115"},{"1":"Diamond Lake","2":"traffic","3":"34"},{"1":"Downtown East","2":"suspicious","3":"143"},{"1":"Downtown East","2":"traffic","3":"119"},{"1":"Downtown West","2":"suspicious","3":"3338"},{"1":"Downtown West","2":"traffic","3":"1071"},{"1":"East Harriet","2":"suspicious","3":"80"},{"1":"East Harriet","2":"traffic","3":"89"},{"1":"East Isles","2":"suspicious","3":"112"},{"1":"East Isles","2":"traffic","3":"418"},{"1":"East Phillips","2":"suspicious","3":"959"},{"1":"East Phillips","2":"traffic","3":"428"},{"1":"ECCO","2":"suspicious","3":"188"},{"1":"ECCO","2":"traffic","3":"120"},{"1":"Elliot Park","2":"suspicious","3":"424"},{"1":"Elliot Park","2":"traffic","3":"120"},{"1":"Ericsson","2":"suspicious","3":"105"},{"1":"Ericsson","2":"traffic","3":"31"},{"1":"Field","2":"suspicious","3":"68"},{"1":"Field","2":"traffic","3":"19"},{"1":"Folwell","2":"suspicious","3":"485"},{"1":"Folwell","2":"traffic","3":"745"},{"1":"Fulton","2":"suspicious","3":"90"},{"1":"Fulton","2":"traffic","3":"40"},{"1":"Hale","2":"suspicious","3":"48"},{"1":"Hale","2":"traffic","3":"13"},{"1":"Harrison","2":"suspicious","3":"245"},{"1":"Harrison","2":"traffic","3":"156"},{"1":"Hawthorne","2":"suspicious","3":"785"},{"1":"Hawthorne","2":"traffic","3":"1246"},{"1":"Hiawatha","2":"suspicious","3":"161"},{"1":"Hiawatha","2":"traffic","3":"74"},{"1":"Holland","2":"suspicious","3":"319"},{"1":"Holland","2":"traffic","3":"850"},{"1":"Howe","2":"suspicious","3":"146"},{"1":"Howe","2":"traffic","3":"50"},{"1":"Humboldt Industrial Area","2":"suspicious","3":"4"},{"1":"Humboldt Industrial Area","2":"traffic","3":"6"},{"1":"Jordan","2":"suspicious","3":"813"},{"1":"Jordan","2":"traffic","3":"1262"},{"1":"Keewaydin","2":"suspicious","3":"105"},{"1":"Keewaydin","2":"traffic","3":"10"},{"1":"Kenny","2":"suspicious","3":"65"},{"1":"Kenny","2":"traffic","3":"53"},{"1":"Kenwood","2":"suspicious","3":"48"},{"1":"Kenwood","2":"traffic","3":"145"},{"1":"King Field","2":"suspicious","3":"249"},{"1":"King Field","2":"traffic","3":"597"},{"1":"Lind - Bohanon","2":"suspicious","3":"272"},{"1":"Lind - Bohanon","2":"traffic","3":"72"},{"1":"Linden Hills","2":"suspicious","3":"124"},{"1":"Linden Hills","2":"traffic","3":"94"},{"1":"Logan Park","2":"suspicious","3":"79"},{"1":"Logan Park","2":"traffic","3":"276"},{"1":"Longfellow","2":"suspicious","3":"441"},{"1":"Longfellow","2":"traffic","3":"162"},{"1":"Loring Park","2":"suspicious","3":"548"},{"1":"Loring Park","2":"traffic","3":"193"},{"1":"Lowry Hill","2":"suspicious","3":"117"},{"1":"Lowry Hill","2":"traffic","3":"126"},{"1":"Lowry Hill East","2":"suspicious","3":"603"},{"1":"Lowry Hill East","2":"traffic","3":"888"},{"1":"Lyndale","2":"suspicious","3":"872"},{"1":"Lyndale","2":"traffic","3":"1282"},{"1":"Lynnhurst","2":"suspicious","3":"86"},{"1":"Lynnhurst","2":"traffic","3":"159"},{"1":"Marcy Holmes","2":"suspicious","3":"517"},{"1":"Marcy Holmes","2":"traffic","3":"1281"},{"1":"Marshall Terrace","2":"suspicious","3":"93"},{"1":"Marshall Terrace","2":"traffic","3":"262"},{"1":"McKinley","2":"suspicious","3":"315"},{"1":"McKinley","2":"traffic","3":"457"},{"1":"Mid - City Industrial","2":"suspicious","3":"77"},{"1":"Mid - City Industrial","2":"traffic","3":"201"},{"1":"Midtown Phillips","2":"suspicious","3":"767"},{"1":"Midtown Phillips","2":"traffic","3":"252"},{"1":"Minnehaha","2":"suspicious","3":"95"},{"1":"Minnehaha","2":"traffic","3":"18"},{"1":"Morris Park","2":"suspicious","3":"71"},{"1":"Morris Park","2":"traffic","3":"3"},{"1":"Near - North","2":"suspicious","3":"937"},{"1":"Near - North","2":"traffic","3":"1319"},{"1":"Nicollet Island - East Bank","2":"suspicious","3":"163"},{"1":"Nicollet Island - East Bank","2":"traffic","3":"782"},{"1":"North Loop","2":"suspicious","3":"333"},{"1":"North Loop","2":"traffic","3":"466"},{"1":"Northeast Park","2":"suspicious","3":"130"},{"1":"Northeast Park","2":"traffic","3":"196"},{"1":"Northrop","2":"suspicious","3":"172"},{"1":"Northrop","2":"traffic","3":"17"},{"1":"Page","2":"suspicious","3":"32"},{"1":"Page","2":"traffic","3":"9"},{"1":"Phillips West","2":"suspicious","3":"466"},{"1":"Phillips West","2":"traffic","3":"260"},{"1":"Powderhorn Park","2":"suspicious","3":"746"},{"1":"Powderhorn Park","2":"traffic","3":"309"},{"1":"Prospect Park - East River Road","2":"suspicious","3":"210"},{"1":"Prospect Park - East River Road","2":"traffic","3":"384"},{"1":"Regina","2":"suspicious","3":"108"},{"1":"Regina","2":"traffic","3":"34"},{"1":"Seward","2":"suspicious","3":"380"},{"1":"Seward","2":"traffic","3":"130"},{"1":"Sheridan","2":"suspicious","3":"101"},{"1":"Sheridan","2":"traffic","3":"217"},{"1":"Shingle Creek","2":"suspicious","3":"106"},{"1":"Shingle Creek","2":"traffic","3":"26"},{"1":"St. Anthony East","2":"suspicious","3":"66"},{"1":"St. Anthony East","2":"traffic","3":"152"},{"1":"St. Anthony West","2":"suspicious","3":"128"},{"1":"St. Anthony West","2":"traffic","3":"347"},{"1":"Standish","2":"suspicious","3":"180"},{"1":"Standish","2":"traffic","3":"32"},{"1":"Steven's Square - Loring Heights","2":"suspicious","3":"447"},{"1":"Steven's Square - Loring Heights","2":"traffic","3":"559"},{"1":"Sumner - Glenwood","2":"suspicious","3":"70"},{"1":"Sumner - Glenwood","2":"traffic","3":"53"},{"1":"Tangletown","2":"suspicious","3":"98"},{"1":"Tangletown","2":"traffic","3":"449"},{"1":"University of Minnesota","2":"suspicious","3":"84"},{"1":"University of Minnesota","2":"traffic","3":"134"},{"1":"Ventura Village","2":"suspicious","3":"766"},{"1":"Ventura Village","2":"traffic","3":"330"},{"1":"Victory","2":"suspicious","3":"265"},{"1":"Victory","2":"traffic","3":"233"},{"1":"Waite Park","2":"suspicious","3":"144"},{"1":"Waite Park","2":"traffic","3":"100"},{"1":"Webber - Camden","2":"suspicious","3":"385"},{"1":"Webber - Camden","2":"traffic","3":"271"},{"1":"Wenonah","2":"suspicious","3":"101"},{"1":"Wenonah","2":"traffic","3":"11"},{"1":"West Calhoun","2":"suspicious","3":"45"},{"1":"West Calhoun","2":"traffic","3":"35"},{"1":"Whittier","2":"suspicious","3":"1351"},{"1":"Whittier","2":"traffic","3":"1977"},{"1":"Willard - Hay","2":"suspicious","3":"532"},{"1":"Willard - Hay","2":"traffic","3":"675"},{"1":"Windom","2":"suspicious","3":"183"},{"1":"Windom","2":"traffic","3":"221"},{"1":"Windom Park","2":"suspicious","3":"158"},{"1":"Windom Park","2":"traffic","3":"303"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
  
  15. Use a `leaflet` map and the `MplsStops` dataset to display each of the stops on a map as a small point. Color the points differently depending on whether they were for suspicious vehicle/person or a traffic stop (the `problem` variable). HINTS: use `addCircleMarkers`, set `stroke = FAlSE`, use `colorFactor()` to create a palette.  
  
  16. Save the folder from moodle called Minneapolis_Neighborhoods into your project/repository folder for this assignment. Make sure the folder is called Minneapolis_Neighborhoods. Use the code below to read in the data and make sure to **delete the `eval=FALSE`**. Although it looks like it only links to the .sph file, you need the entire folder of files to create the `mpls_nbhd` data set. These data contain information about the geometries of the Minneapolis neighborhoods. Using the `mpls_nbhd` dataset as the base file, join the `mpls_suspicious` and `MplsDemo` datasets to it by neighborhood (careful, they are named different things in the different files). Call this new dataset `mpls_all`.


```r
mpls_nbhd <- st_read("Minneapolis_Neighborhoods/Minneapolis_Neighborhoods.shp", quiet = TRUE)
```

  17. Use `leaflet` to create a map from the `mpls_all` data  that colors the neighborhoods by `prop_suspicious`. Display the neighborhood name as you scroll over it. Describe what you observe in the map.
  
  18. Use `leaflet` to create a map of your own choosing. Come up with a question you want to try to answer and use the map to help answer that question. Describe what your map shows. 
  
  
## GitHub link

  19. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 04_exercises.Rmd, provide a link to the 04_exercises.md file, which is the one that will be most readable on GitHub.


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
