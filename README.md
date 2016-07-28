---
title: "DC_parking_violations"
output: github_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Analysis plan

Parking tickets. The dread of city dwellers everywhere. The Washington DC government has published a wealth of data about the tickets it hands out, which provides an interesting opportunity for data-minded people.

- Lots of data cleaning to get all of the files together
- Moran's I (it'll definitely be significant)
- Most common states by month
- Ticket type by time of day
- Ticket volume by time of day
- Tickets by time of month
- Most common places where tickets are issued
- Most common places where tickets are issued to non-DMV cars

http://opendata.dc.gov/datasets/977602b156f74e41ae2dabbfaca42e20_3
http://opendata.dc.gov/datasets/825f603d4cfc41fe9cf9ee74c359f893_2
http://opendata.dc.gov/datasets/150cf72502b344448d900a4f1d779b3a_1
http://opendata.dc.gov/datasets/7e688a52e65d49c0beef48289860f465_0
http://opendata.dc.gov/datasets/2e967e9053144a309680fccea0f7b4e1_11
http://opendata.dc.gov/datasets/9b040759c7264e59b8943fea0f081725_10

## Analysis
When working with geospatial data, many people think every analysis should be a map. That approach can be useful, but shapefiles often contain valuable data.



```{r setup}
library(magrittr)
library(ggplot2)
library(scales)
library(ggthemes)
library(dplyr)
library(rgdal)
library(choroplethr)


library(lubridate)

# Unzip the files
unzip("./data/Parking_Violations_in_April_2016.zip", exdir = "./data", overwrite = TRUE)

# Read shapefiles
april16 <- readOGR(dsn = "./data" , layer = "Parking_Violations_in_April_2016")

```

Let's dive into the data! Each parking ticket records the state of the offending car. As you'd expect, most ticketed cars come from the DC area, but nearby Maryland eclipses DC for first place.
```{r states}

count(april16$RP_PLATE_S) %>%
  tbl_df %>%
  setNames(c("State", "Tickets")) %>%
  dplyr::arrange(desc(Tickets)) %>%
  top_n(3) %>%
  ggplot(., aes(x = State, y = Tickets)) +
  geom_bar(stat="identity") +
  ggtitle("Most frequent states") +
  theme_bw()

state.name[match(april16$RP_PLATE_S, state.abb)] %>% tolower %>%
  table %>% 
  data.frame %>%
  setNames(c("region", "value")) %>% state_choropleth

```


# Default language (will delete later)
## GitHub Documents

This is an R Markdown format used for publishing markdown documents to GitHub. When you click the **Knit** button all R code chunks are run and a markdown file (.md) suitable for publishing to GitHub is generated.

## Including Code

You can include R code in the document as follows:

```{r cars}
summary(cars)
```

## Including Plots

You can also embed plots, for example:

```{r pressure, echo=TRUE}
plot(pressure)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
