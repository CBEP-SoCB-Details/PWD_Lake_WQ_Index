Exploring the Source Data File
================
12/19/2020

  - [Broad Outline of the Index](#broad-outline-of-the-index)
  - [Import Libraries](#import-libraries)
  - [Establish Folder Link](#establish-folder-link)
  - [Understanding the Source Data](#understanding-the-source-data)
  - [Reviewing Contents of
    Worksheets.](#reviewing-contents-of-worksheets.)
      - [“WQ 1 Existing 2017 chart”](#wq-1-existing-2017-chart)
      - [“WQ 2 Trend thru 2017”](#wq-2-trend-thru-2017)
      - [“WQ 3 Trend criteria”](#wq-3-trend-criteria)
      - [“WQ 4 Coefficient absolute
        value”](#wq-4-coefficient-absolute-value)
      - [“WQ 5 Trend chart”](#wq-5-trend-chart)
      - [“WQ Trend Chart for PP”](#wq-trend-chart-for-pp)
      - [“Final Fact Sheet Scores”](#final-fact-sheet-scores)
      - [“FINAL SCORES horizontal”](#final-scores-horizontal)
      - [“Final Land Cover Scores all”](#final-land-cover-scores-all)
      - [“Sheet2”](#sheet2)
      - [“Final Scores for all
        subwaters”](#final-scores-for-all-subwaters)
      - [" WQ and land correlation"](#wq-and-land-correlation)
      - [“LAND workbook”](#land-workbook)
      - [“LAND Green trend scores”](#land-green-trend-scores)
      - [“LAND Forest lost per century”](#land-forest-lost-per-century)
      - [“LAND Green trend values”](#land-green-trend-values)
      - [“LAND all 2018 data”](#land-all-2018-data)
      - [“Land Existing 2018”](#land-existing-2018)
      - [“LAND existing criteria”](#land-existing-criteria)
      - [“LAND existing 2018 ALL”](#land-existing-2018-all)
      - [“LAND %Green 87-18”](#land-green-87-18)
      - [“LAND Years to Tipping Point”](#land-years-to-tipping-point)
      - [“LAND Tipping Point final
        table”](#land-tipping-point-final-table)
      - [“LAND all data”](#land-all-data)
      - [“Raw LANDSAT data”](#raw-landsat-data)
      - [“LAND trend minus cloudy subw”](#land-trend-minus-cloudy-subw)
      - [“LAND percent cover by year”](#land-percent-cover-by-year)
      - [“LAND for GIS EXISTING 2018”](#land-for-gis-existing-2018)
      - [“LAND LANDSAT descript”](#land-landsat-descript)
      - [“LAND Trend graphs by lake”](#land-trend-graphs-by-lake)
      - [“LAND RAW DATA 09\_18”](#land-raw-data-09_18)
      - [“QC Midas”](#qc-midas)
  - [Data Review Conclusions](#data-review-conclusions)
  - [Understanding methods](#understanding-methods)
  - [List of Lakes](#list-of-lakes)

<img
  src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
  style="position:absolute;top:10px;right:50px;" />

# Broad Outline of the Index

The Core Senbago Lake Subwatershed Index is built from information
addressing only FOUR subcomponents Each of those four values is given a
score from one to five, which are summed to give an overall condition
index. The compoinents are as follows:

  - Current Water Quality – “Average Secchi transparency over the most
    recent 10+ Years”.
      - Scores
          - 1 SD \<= 4  
            \_ 2 4 \< SD \<= 5  
            \_ 3 5 \< SD \<= 6  
            \_ 4 6 \< SD \<= 7  
            \_ 5 7 \< SD
  - Water Quality Trend – “Trend in chlorophyll concentration over the
    most recent 10+ years”. Trend appears to be normalized, but the
    method is not clearly documented. (Can use negative of Secchi Depths
    instead?).
      - Scores
          - 1 Statistically significant (p\< 0.05) increase in ChlA \>
            3%  
            \_ 2 Non-significant increase in CHl-A greater than 3%  
            \_ 3 Less than 3% change in Chl-a  
            \_ 4 Non-significant decline in Chl-A greater than 3%  
            \_ 5 Statistically significant (P\< 0.05) decline in Chl\_A
  - Current Land Cover – “Percent of each subwatershed that is green” in
    2018 LANDSAT imagery. Apparently using unsupervised classification
    to segragate into water, bare, developed, and green lands.
      - Scores
          - 1 \< 75%  
            \_ 2 75 - 80  
            \_ 3 80 - 85  
            \_ 4 85 - 90  
            \_ 5 \> 90
  - Land Cover Trend – “The pace at which”green" is changing to
    “developed”. Classification based on successive LANDSAT images,
    1987, 1995, 2009, 2018. Linear regression extracts slope in pct per
    year. That slope is matched with current pct green to estimate “How
    many years until the tipping point” of 76% green.
      - Scores
          - 1 \< 20  
            \_ 2 20 - 30  
            \_ 3 30 - 50  
            \_ 4 50 - 100  
            \_ 5 \> 100

The resulting index (in principal) ranges from 4 to 20.

# Import Libraries

``` r
library(tidyverse)
#> -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
#> v ggplot2 3.3.2     v purrr   0.3.4
#> v tibble  3.0.4     v dplyr   1.0.2
#> v tidyr   1.1.2     v stringr 1.4.0
#> v readr   1.4.0     v forcats 0.5.0
#> -- Conflicts ------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
library(readxl)

library(CBEPgraphics)
load_cbep_fonts()
theme_set(theme_cbep())
```

# Establish Folder Link

``` r
sibfldnm <- 'Original_Data'
parent <- dirname(getwd())
sibling <- file.path(parent,sibfldnm)
fn <- 'Sebago Lake Subwatersheds Summary of Analyses through 2017.xlsx'
fpath <- file.path(sibling, fn)
```

# Understanding the Source Data

The Excel file we were given contains many worksheets (“tabs”). We need
to evaluate the contents of the file. We start by simply listing and
describing all the worksheets in the Excel File. We can use a function
from the `readxl` package to list all worksheets, then review them one
at a time.

``` r
(shts <- excel_sheets(fpath))
#>  [1] "WQ 1 Existing 2017 chart"        "WQ 2 Trend thru 2017"           
#>  [3] "WQ 3 Trend criteria"             "WQ 4 Coefficient absolute value"
#>  [5] "WQ 5 Trend chart"                "WQ Trend Chart for PP"          
#>  [7] "Final Fact Sheet Scores"         "FINAL SCORES horizontal"        
#>  [9] "Final Land Cover Scores all"     "Sheet2"                         
#> [11] "Final Scores for all subwaters"  " WQ and land correlation"       
#> [13] "LAND workbook"                   "LAND Green trend scores"        
#> [15] "LAND Forest lost per century"    "LAND Green trend values"        
#> [17] "LAND all 2018 data"              "Land Existing 2018"             
#> [19] "LAND existing criteria"          "LAND existing 2018 ALL"         
#> [21] "LAND %Green 87-18"               "LAND Years to Tipping Point"    
#> [23] "LAND Tipping Point final table"  "LAND all data "                 
#> [25] "Raw LANDSAT data"                "LAND trend minus cloudy subw"   
#> [27] "LAND percent cover by year"      "LAND for GIS EXISTING 2018"     
#> [29] "LAND LANDSAT descript"           "LAND Trend graphs by lake"      
#> [31] "LAND RAW DATA 09_18"             "QC Midas"
```

# Reviewing Contents of Worksheets.

We have 32 worksheets to review.

### “WQ 1 Existing 2017 chart”

Summary of recent Secchi depth data, for variable periods. Most lakes
have more than 10 years of record, some do not. This is the source of
the list of lakes we enter below. Includes two charts.

### “WQ 2 Trend thru 2017”

Provides summaries of trends for the 25 core lakes, including narrative
grouping of trends, scores, number of “observations” and “coefficients”.
It is not clear if “observations” refer to individual readings or years,
but numbers are under 50, and match “Yeas of record” in the WQ4 tab.
Slopes are presented as “coefficients”. Units not expressed.

### “WQ 3 Trend criteria”

Table of criteria. No relevant data.

### “WQ 4 Coefficient absolute value”

Similar data to WQ2, organized slightly differently, and with additional
data on the “green trend”. Slightly more clearly labeled.

### “WQ 5 Trend chart”

In addition to the chart, includes slopes, with units changes. A
“coefficient” of 0.10 on the previous sheets becomes a “Coefficient”
here of 10%. Percentage of what? Page includes P values and 1-p value
(unlabeled) for regressions, absent on other sheets.

### “WQ Trend Chart for PP”

Similar Data.

### “Final Fact Sheet Scores”

Includes final scores and sums, on color-coded grid. Multiple copies
Multiple copies, apparently all with similar data.

### “FINAL SCORES horizontal”

Includes final scores and sums, on color-coded grid. Single layout may
be easier to access. Lakes are columns, scores are rows.

### “Final Land Cover Scores all”

Final scores and score definitions only. Includes annotation that “%
Green = (BG+G)/{BG+G+D)” \[sic\]. Not sure what the annotation means.
Results included for over 50 lakes, not just the core 25 lakes with good
WQ data.

### “Sheet2”

Includes existing land cover scores and slopes (coefficients expressed
as percent change per century), but not Land Use Trend scores.
Calculation of time to reach 76 % not shown.

### “Final Scores for all subwaters”

Existing Land Use Final Scores.

### " WQ and land correlation"

Includes data for land use observations (% green; 1997-2018), land use
trend, expressed in a few different units, and the WQ trend (slope).
Includes a couple of charts. Dos not shoe time to reach 76%

### “LAND workbook”

Similar data to the last worksheet, with some extra columns showing
aggregate percent green for groups of lakes by WQ trend score. (showing
a not very close relationship).

### “LAND Green trend scores”

Includes Green trend scores (**using an alternate percent lost per
century statistic**), plus trend line slope, change (green to developed)
over 31 year record (calculated from slope) and approximate percent
forse4t lost at that rate over 100 years.

### “LAND Forest lost per century”

Appears to be identical to the prior worksheet.

### “LAND Green trend values”

Only includes the trend line slopes.

### “LAND all 2018 data”

Raw LANSDAT data and some related analyses. Columns with cryptic headers
that appear to refer to LANDSAT categories of some sort:

  - C\_18\_C  
  - C\_18\_W  
  - C\_18\_BG  
  - C\_18\_GS  
  - C\_18\_D

These are then rescaled to be a percentage omitting the “C” category.
Those are then combined into “2018 Green (GS+BG)” amd “2018 all land
(GS+BG+D)”

### “Land Existing 2018”

Pulls percent green from prior page, and assigns current land use score.

### “LAND existing criteria”

Shows existing land use category criteria.

### “LAND existing 2018 ALL”

Percent green and land use scores for more than 50 lakes.

### “LAND %Green 87-18”

Raw data on percent green for each of the 25 lakes with decent WQ data,
from four different LANDSAT years.

### “LAND Years to Tipping Point”

Takes data from prior worksheet, and calculates number of years to reach
76%. Also expresses that as the estimated year of reaching the tipping
point.

### “LAND Tipping Point final table”

Extracts years to tipping point and the year in which the tipping point
would be reached from the prior worksheet, and organizes them as a
simpler table.

### “LAND all data”

Similar to the raw data from “LAND all 2018 data”, without most of the
calculations, but including LANDSAT data from all years.

### “Raw LANDSAT data”

Provides raw LANDSAT data, with explanation for what the letter codes
mean:

  - C = Clouds  
  - W = Water  
  - BG = Bare Ground  
  - GS = Green Space  
  - D = Developed

### “LAND trend minus cloudy subw”

Summarized “percent green” data, by year, including trendline slopes,
percent change per century, and years to 76%. Includes land use change
scores. “%Div/0” for some lakes, presumably those with cloudy conditions
for some years.

### “LAND percent cover by year”

Similar to the previous worksheet, but with fewer data columns, slightly
better presented.

### “LAND for GIS EXISTING 2018”

Simple table of Perent Green in 2018 for 25 selected lakes.

### “LAND LANDSAT descript”

**Column Key**

|                                                                                                             |
| :---------------------------------------------------------------------------------------------------------- |
| Area in acres no water or clouds = GS+BG                                                                    |
| Acres of green space and bare ground in 2009 = GS + BG                                                      |
| % green space and bare ground in 2009 = (GS+BG)/(GS+BG+D)                                                   |
| % Change in green space and bare ground between 1987-2009 = 1987 (GS+BG)/(GS+BG+D) – 2009 (GS+BG)/(GS+BG+D) |
| Acres of green space and bare ground in 2018 = GS + BG                                                      |
| % green space and bare ground in 2018 = (GS+BG)/(GS+BG+D)                                                   |
| % Change in green space and bare ground between 2009-2018 = 2009 (GS+BG)/(GS+BG+D) – 2018 (GS+BG)/(GS+BG+D) |

### “LAND Trend graphs by lake”

Pivoted lake percent green data, used to create graphs and regression
lines. many charts.

### “LAND RAW DATA 09\_18”

Nicely presented raw landsat data, for 2009 and 2018 only, with
calculation of 9 year “percent loss”

### “QC Midas”

Complicated cross-classification of MIDAS codes, apparently to determine
which lakes have sufficient data to analyze.

# Data Review Conclusions

  - We can extract the PWD Scores from several places, but all scores
    are available in one place (for core lakes) on the “Final Fact Sheet
    Scores” worksheet a1:h:26

  - We do not have raw data in which the water quality scores are based,
    only derived statistics.
    
      - Recent mean Secchi Depth data is available from the “WQ 1
        Existing 2017” Worksheet. The data, however, appears to be based
        on variable length records, including some shorter than the
        nominal 10 years they want to use.
    
      - Water Quality trends and P values are available (unlabeled) from
        the “WQ 4 Coefficient absolute value” worksheet. or from “WQ 5
        Trend Chart”, where slopes have been expressed as a percent. It
        is not clear how that step is legitimate, unless original
        regressions were carried out on normalized values.

  - Land cover data is available in multiple locations.
    
      - “Percent Green” values for multiple years, along with “Green
        Trend” and “Percent change Per century” values are available on
        the “LAND percent cover by year” worksheet. Similar databut with
        the “Years to 76%” data added are available in “LAND trend minus
        cloudy subw”.
    
      - Raw LANDSAT data are available in “LAND data all” or “Raw
        LANDSAT data”

# Understanding methods

We have some ambiguity about the methods used, as different sources
provide different details. It is not entirely clear that the Excel
spreadsheet, the PowerPoint slide deck, and the NALMS publication all
refer to exactly the same methods.

For example, both the PowerPoint and the Excel spreadsheet use recent
data on Secchi Depth to assign Current Water Quality Scores. The
publication implies the data was based on Maine’s Trophic State Index.

The Publication discusses water quality trends in terms of a
Kendall-Mann test. But Kendall-Mann test does not produce a slope, but
Kendall’s Tau, which is a robust relative of the correlation
coefficient, with a range of -1 to 1. It is likely that the
“Coefficients” listed in the Excel Spreadsheets and discussed in the
Powerpoint are Kendall’s tau.

As the transformation from Secchi Depth to TSI (or from Chlorophyll a to
TSI) is monotonic, the value of Kendall’s Tau should be unaffected by
shifting from raw observations to TSI.

There is some confusion in the Excel spreadsheet between Years of Record
and Sample Sizes, which suggests the data being analysed are annual
summaries, rather than raw individual observations, but that is not
certain.

# List of Lakes

We entered this list, of the lakes for which PWD had enough information
to calculate the index, by hand.

| Lake Name            | MIDAS Number |
| -------------------- | ------------ |
| ADAMS POND           | 3396         |
| BRANDY POND          | 9685         |
| BEAR POND            | 3420         |
| COLD RAIN POND       | 3376         |
| CRESCENT LAKE        | 3696         |
| CRYSTAL POND         | 3452         |
| HIGHLAND LAKE        | 3454         |
| INGALLS FOSTERS POND | 3188         |
| ISLAND POND          | 3448         |
| KEOKA LAKE           | 3416         |
| LONG LAKE            | 5780         |
| MCWAIN POND          | 3418         |
| MOOSE POND           | 3424         |
| OTTER POND           | 3458         |
| PANTHER POND         | 3694         |
| PAPOOSE POND         | 3414         |
| PEABODY POND         | 3374         |
| PLEASANT LAKE        | 3446         |
| RAYMOND POND         | 3690         |
| SEBAGO LAKE          | 5876         |
| SONGO POND           | 3262         |
| STEARNS POND         | 3234         |
| THOMAS POND          | 3392         |
| TRICKEY POND         | 3382         |
| WOODS POND           | 3456         |

``` r
lakes_list <- tribble(
  ~Lake, ~MIDAS,
"ADAMS POND",     3396,
"BRANDY POND",    9685,
"BEAR POND",      3420,
"COLD RAIN POND", 3376,
"CRESCENT LAKE",  3696,
"CRYSTAL POND",   3452,
"HIGHLAND LAKE",  3454,
"INGALLS (FOSTERS) POND", 3188,
"ISLAND POND",    3448,
"KEOKA LAKE",     3416,
"LONG LAKE",      5780,
"MCWAIN POND",    3418,
"MOOSE POND",     3424,
"OTTER POND",     3458,
"PANTHER POND",   3694,
"PAPOOSE POND",   3414,
"PEABODY POND",   3374,
"PLEASANT LAKE",  3446,
"RAYMOND POND",   3690,
"SEBAGO LAKE",    5876,
"SONGO POND",     3262,
"STEARNS POND",   3234,
"THOMAS POND",    3392,
"TRICKEY POND",   3382,
"WOODS POND",     3456
)
```
