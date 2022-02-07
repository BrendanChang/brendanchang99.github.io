Case Study - How can a wellness company play it smart?
================
Chang Jun Cheng
1/2/2022

## Data background

The data was collected on Kaggle for case study purpose, updated version
of the original data sources by the maintainer *Kaggler* Möbius. Dataset
date range from 4.12.2016 to 5.12.2016.

Below are the original dataset from <https://zenodo.org/>

Furberg, R., Brinton, J., Keating, M., & Ortiz, A. (2016). Crowd-sourced
Fitbit datasets 03.12.2016-05.12.2016 \[Data set\]. Zenodo.
<https://doi.org/10.5281/zenodo.53894>

### Install and load R packages

Install and load R packages, including `tidyverse`, `janitor`, `reshape`
and `lubridate`

``` r
install.packages("tidyverse")
```

    ## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.1'
    ## (as 'lib' is unspecified)

``` r
install.packages("janitor")
```

    ## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.1'
    ## (as 'lib' is unspecified)

``` r
install.packages("lubridate")
```

    ## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.1'
    ## (as 'lib' is unspecified)

``` r
install.packages("reshape")
```

    ## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.1'
    ## (as 'lib' is unspecified)

``` r
library("tidyverse")
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.6     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.4     ✓ stringr 1.4.0
    ## ✓ readr   2.1.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library("janitor")
```

    ## 
    ## Attaching package: 'janitor'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library("lubridate")
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
library("reshape")
```

    ## 
    ## Attaching package: 'reshape'

    ## The following object is masked from 'package:lubridate':
    ## 
    ##     stamp

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     rename

    ## The following objects are masked from 'package:tidyr':
    ## 
    ##     expand, smiths

### Load CSV files to R

Upload and load the CSV files that are stored in Big Query

**daily_activity_ver1** is the **original** version with 33 unique
respondents

**daily_activity_ver2** is the **updated** version with 28 unique
respondents by removing the rows with \*\*sum of different levels of
intensity minutes not equal to 1440 (60\*24)\*\*

For more information, click here
[link](https://docs.google.com/spreadsheets/d/1YsUJBvP4Y285mPF_mBcTmMiZ435MLFQF_Tnt-ikPCeE/edit#gid=0)

``` r
daily_activity_ver1 <- read.csv("dailyActivity_merged.csv")  
daily_activity_ver2  <- read.csv("daily_data.csv")           
daily_sleep <- read.csv("day_sleep.csv")
hourly_activity  <- read.csv("hourly_data.csv")
```

### Inspecting and exploring the key tables

``` r
head(daily_activity_ver1)
```

    ##           Id ActivityDate TotalSteps TotalDistance TrackerDistance
    ## 1 1503960366    4/12/2016      13162          8.50            8.50
    ## 2 1503960366    4/13/2016      10735          6.97            6.97
    ## 3 1503960366    4/14/2016      10460          6.74            6.74
    ## 4 1503960366    4/15/2016       9762          6.28            6.28
    ## 5 1503960366    4/16/2016      12669          8.16            8.16
    ## 6 1503960366    4/17/2016       9705          6.48            6.48
    ##   LoggedActivitiesDistance VeryActiveDistance ModeratelyActiveDistance
    ## 1                        0               1.88                     0.55
    ## 2                        0               1.57                     0.69
    ## 3                        0               2.44                     0.40
    ## 4                        0               2.14                     1.26
    ## 5                        0               2.71                     0.41
    ## 6                        0               3.19                     0.78
    ##   LightActiveDistance SedentaryActiveDistance VeryActiveMinutes
    ## 1                6.06                       0                25
    ## 2                4.71                       0                21
    ## 3                3.91                       0                30
    ## 4                2.83                       0                29
    ## 5                5.04                       0                36
    ## 6                2.51                       0                38
    ##   FairlyActiveMinutes LightlyActiveMinutes SedentaryMinutes Calories
    ## 1                  13                  328              728     1985
    ## 2                  19                  217              776     1797
    ## 3                  11                  181             1218     1776
    ## 4                  34                  209              726     1745
    ## 5                  10                  221              773     1863
    ## 6                  20                  164              539     1728

``` r
head(daily_sleep)
```

    ##           Id              SleepDay TotalSleepRecords TotalMinutesAsleep
    ## 1 1503960366 4/12/2016 12:00:00 AM                 1                327
    ## 2 1503960366 4/13/2016 12:00:00 AM                 2                384
    ## 3 1503960366 4/15/2016 12:00:00 AM                 1                412
    ## 4 1503960366 4/16/2016 12:00:00 AM                 2                340
    ## 5 1503960366 4/17/2016 12:00:00 AM                 1                700
    ## 6 1503960366 4/19/2016 12:00:00 AM                 1                304
    ##   TotalTimeInBed
    ## 1            346
    ## 2            407
    ## 3            442
    ## 4            367
    ## 5            712
    ## 6            320

``` r
head(hourly_activity)
```

    ##           Id         ActivityHour StepTotal TotalIntensity AverageIntensity
    ## 1 8053475328 2016-04-15T22:00:00Z       770             41         0.683333
    ## 2 8053475328 2016-04-20T21:00:00Z       775             44         0.733333
    ## 3 8053475328 2016-05-05T14:00:00Z      3848             93         1.550000
    ## 4 8053475328 2016-04-25T18:00:00Z      1289             42         0.700000
    ## 5 8053475328 2016-04-30T15:00:00Z      3851             91         1.516667
    ## 6 8053475328 2016-04-19T15:00:00Z      1293             42         0.700000
    ##   Calories
    ## 1      169
    ## 2      174
    ## 3      348
    ## 4      184
    ## 5      369
    ## 6      187

### Understanding metadata of the key tables

``` r
str(daily_activity_ver1)
```

    ## 'data.frame':    940 obs. of  15 variables:
    ##  $ Id                      : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityDate            : chr  "4/12/2016" "4/13/2016" "4/14/2016" "4/15/2016" ...
    ##  $ TotalSteps              : int  13162 10735 10460 9762 12669 9705 13019 15506 10544 9819 ...
    ##  $ TotalDistance           : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ TrackerDistance         : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ LoggedActivitiesDistance: num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ VeryActiveDistance      : num  1.88 1.57 2.44 2.14 2.71 ...
    ##  $ ModeratelyActiveDistance: num  0.55 0.69 0.4 1.26 0.41 ...
    ##  $ LightActiveDistance     : num  6.06 4.71 3.91 2.83 5.04 ...
    ##  $ SedentaryActiveDistance : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ VeryActiveMinutes       : int  25 21 30 29 36 38 42 50 28 19 ...
    ##  $ FairlyActiveMinutes     : int  13 19 11 34 10 20 16 31 12 8 ...
    ##  $ LightlyActiveMinutes    : int  328 217 181 209 221 164 233 264 205 211 ...
    ##  $ SedentaryMinutes        : int  728 776 1218 726 773 539 1149 775 818 838 ...
    ##  $ Calories                : int  1985 1797 1776 1745 1863 1728 1921 2035 1786 1775 ...

``` r
str(daily_activity_ver2)
```

    ## 'data.frame':    478 obs. of  15 variables:
    ##  $ Id                      : num  1.62e+09 1.64e+09 1.64e+09 1.64e+09 1.64e+09 ...
    ##  $ ActivityDate            : chr  "2016-05-01" "2016-04-14" "2016-04-19" "2016-04-28" ...
    ##  $ TotalSteps              : int  36019 11037 11256 9405 12850 15112 13379 14687 13175 10771 ...
    ##  $ TotalDistance           : num  28.03 8.02 8.18 6.84 9.34 ...
    ##  $ TrackerDistance         : num  28.03 8.02 8.18 6.84 9.34 ...
    ##  $ LoggedActivitiesDistance: num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ VeryActiveDistance      : num  21.92 0.36 0.36 0.2 0.72 ...
    ##  $ ModeratelyActiveDistance: num  4.19 2.56 2.53 2.32 4.09 ...
    ##  $ LightActiveDistance     : num  1.91 5.1 5.3 4.31 4.54 ...
    ##  $ SedentaryActiveDistance : num  0.02 0 0 0 0 ...
    ##  $ VeryActiveMinutes       : int  186 5 5 3 10 48 35 8 33 70 ...
    ##  $ FairlyActiveMinutes     : int  63 58 58 53 94 63 47 122 45 113 ...
    ##  $ LightlyActiveMinutes    : int  171 252 278 227 221 276 297 151 262 178 ...
    ##  $ SedentaryMinutes        : int  1020 1125 1099 1157 1115 1053 1061 1159 1100 1079 ...
    ##  $ Calories                : int  2690 3226 3300 3108 3324 2897 2709 1667 3425 3727 ...

``` r
str(daily_sleep)
```

    ## 'data.frame':    413 obs. of  5 variables:
    ##  $ Id                : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ SleepDay          : chr  "4/12/2016 12:00:00 AM" "4/13/2016 12:00:00 AM" "4/15/2016 12:00:00 AM" "4/16/2016 12:00:00 AM" ...
    ##  $ TotalSleepRecords : int  1 2 1 2 1 1 1 1 1 1 ...
    ##  $ TotalMinutesAsleep: int  327 384 412 340 700 304 360 325 361 430 ...
    ##  $ TotalTimeInBed    : int  346 407 442 367 712 320 377 364 384 449 ...

``` r
str(hourly_activity)
```

    ## 'data.frame':    22099 obs. of  6 variables:
    ##  $ Id              : num  8.05e+09 8.05e+09 8.05e+09 8.05e+09 8.05e+09 ...
    ##  $ ActivityHour    : chr  "2016-04-15T22:00:00Z" "2016-04-20T21:00:00Z" "2016-05-05T14:00:00Z" "2016-04-25T18:00:00Z" ...
    ##  $ StepTotal       : int  770 775 3848 1289 3851 1293 1042 2835 3604 3349 ...
    ##  $ TotalIntensity  : int  41 44 93 42 91 42 36 83 86 83 ...
    ##  $ AverageIntensity: num  0.683 0.733 1.55 0.7 1.517 ...
    ##  $ Calories        : int  169 174 348 184 369 187 162 281 343 313 ...

### Identify unique respondents for each dataset

``` r
n_distinct(daily_activity_ver1$Id)
```

    ## [1] 33

``` r
n_distinct(daily_activity_ver2$Id)
```

    ## [1] 28

``` r
n_distinct(daily_sleep$Id)
```

    ## [1] 24

``` r
n_distinct(hourly_activity$Id)
```

    ## [1] 33

### Data Cleaning

#### Data cleaning - clean names for all variables

In this part, do ensure that `janitor` packages is installed and loaded.

``` r
daily_activity_ver1 <- daily_activity_ver1 %>% clean_names()
daily_activity_ver2 <- daily_activity_ver2 %>%  clean_names()
daily_sleep <- daily_sleep %>%  clean_names()
hourly_activity <- hourly_activity %>%  clean_names()
```

#### Data cleaning - rename all date related variable to date

Rename the date related variable to date for consistency and later part
of analysis.

``` r
daily_activity_ver1 <- dplyr::rename(daily_activity_ver1, date = activity_date)
daily_activity_ver2 <- dplyr::rename(daily_activity_ver2, date = activity_date)
daily_sleep <-  dplyr::rename(daily_sleep, date = sleep_day)
hourly_activity <-  dplyr::rename(hourly_activity, date = activity_hour)
```

#### Data cleaning - change all date related variable from chr to date data type

Change data type for date variable

``` r
daily_activity_ver1$date <- as.Date(daily_activity_ver1$date, "%m/%d/%Y")
str(daily_activity_ver1)
```

    ## 'data.frame':    940 obs. of  15 variables:
    ##  $ id                        : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ date                      : Date, format: "2016-04-12" "2016-04-13" ...
    ##  $ total_steps               : int  13162 10735 10460 9762 12669 9705 13019 15506 10544 9819 ...
    ##  $ total_distance            : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ tracker_distance          : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ logged_activities_distance: num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ very_active_distance      : num  1.88 1.57 2.44 2.14 2.71 ...
    ##  $ moderately_active_distance: num  0.55 0.69 0.4 1.26 0.41 ...
    ##  $ light_active_distance     : num  6.06 4.71 3.91 2.83 5.04 ...
    ##  $ sedentary_active_distance : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ very_active_minutes       : int  25 21 30 29 36 38 42 50 28 19 ...
    ##  $ fairly_active_minutes     : int  13 19 11 34 10 20 16 31 12 8 ...
    ##  $ lightly_active_minutes    : int  328 217 181 209 221 164 233 264 205 211 ...
    ##  $ sedentary_minutes         : int  728 776 1218 726 773 539 1149 775 818 838 ...
    ##  $ calories                  : int  1985 1797 1776 1745 1863 1728 1921 2035 1786 1775 ...

``` r
daily_activity_ver2$date <- as.Date(daily_activity_ver2$date, "%m/%d/%Y")
daily_sleep$date <- as.Date(daily_sleep$date, "%m/%d/%Y")
hourly_activity$date <- as.POSIXct(hourly_activity$date, format="%Y-%m-%dT%H:%M")
str(hourly_activity)
```

    ## 'data.frame':    22099 obs. of  6 variables:
    ##  $ id               : num  8.05e+09 8.05e+09 8.05e+09 8.05e+09 8.05e+09 ...
    ##  $ date             : POSIXct, format: "2016-04-15 22:00:00" "2016-04-20 21:00:00" ...
    ##  $ step_total       : int  770 775 3848 1289 3851 1293 1042 2835 3604 3349 ...
    ##  $ total_intensity  : int  41 44 93 42 91 42 36 83 86 83 ...
    ##  $ average_intensity: num  0.683 0.733 1.55 0.7 1.517 ...
    ##  $ calories         : int  169 174 348 184 369 187 162 281 343 313 ...

### Identify the maximum and minimum date

``` r
max_date = max(daily_activity_ver2$date)
min_date = min(daily_activity_ver2$date)
```

### Analyze and visualize data (minutes spent on every intensity level in a day)

Deep dive into **daily_activity_ver2** to explore and discover
relationship between the variables. \* discover the average portion of
intensity level minutes in a day \* unpivot the 4 columns (wide format
to long format)

install `reshape` packages to `melt` (unpivot the columns)

``` r
daily_activity_ver2_1 <- daily_activity_ver2 %>% 
  select(id, very_active_minutes, fairly_active_minutes, lightly_active_minutes, sedentary_minutes) 
  
melted_daily_activity <- melt(daily_activity_ver2_1, id = c("id"),
                              variable_name = c('intensity_level'))

melted_daily_activity_2 <- melted_daily_activity %>% select(intensity_level, value) %>% 
  group_by(intensity_level) %>% 
  summarize(mean_minutes = mean(value))
```

-   visualize data (observe the weight of each intensity level for a
    day)

``` r
portion_sedentary = round(melted_daily_activity_2$mean_minutes[4]/1440*100,2)

p1 <- melted_daily_activity_2 %>% ggplot(mapping = aes(x=intensity_level, y=mean_minutes, fill=intensity_level))+geom_col()
  
p1 + labs(title = "Minutes spent on every intensity level in a day", x = "intensity level", y = "average minutes", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  theme(axis.text.x = element_text(angle = 25) )+
  annotate(geom = "text", x = "sedentary_minutes", y = 800, label =paste0(portion_sedentary, "%"))
```

![](case_study_files/figure-gfm/visualize%20minutes%20spent%20on%20every%20intensity%20level-1.png)<!-- -->
### Inferencial statistics - hypothesis testing (t-test)

``` r
df1 <- melted_daily_activity %>% 
  select(intensity_level, value) %>% 
  filter(intensity_level == "very_active_minutes" | 
           intensity_level == "sedentary_minutes")

t.test(data = df1, value ~ intensity_level)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  value by intensity_level
    ## t = -190.32, df = 524.91, p-value < 2.2e-16
    ## alternative hypothesis: true difference in means between group very_active_minutes and group sedentary_minutes is not equal to 0
    ## 95 percent confidence interval:
    ##  -1229.431 -1204.310
    ## sample estimates:
    ## mean in group very_active_minutes   mean in group sedentary_minutes 
    ##                          19.08159                        1235.95188

Conclusion: On average, respondent spent **85.83%** of a day in
sedentary behavior.

### Analyze and visualize data 2 (relationship between total steps, average intensities and calories)

Total Steps Taken vs Average intensity

``` r
corr1 <- round(cor(hourly_activity$average_intensity, hourly_activity$step_total),4)

p2 <- hourly_activity %>%  ggplot(aes(x=average_intensity, y=step_total))+
  geom_point()+geom_smooth()


p2 + labs(title = "Total Steps Taken vs Average intensity", x = "Average intensity", y = "Total Steps Taken", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 1.5, y = 7500, label =paste0("correlation, r: ", corr1), color = "red")
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Total%20Steps%20Taken%20vs%20Average%20intensity-1.png)<!-- -->

**Conclusion: the higher the intensity, the more steps taken in a day.**

Calories burned vs Average intensity

``` r
corr2 <- round(cor(hourly_activity$average_intensity, hourly_activity$calories),4)

p3 <- hourly_activity %>%  ggplot(aes(x=average_intensity, y=calories))+
  geom_point()+geom_smooth()


p3 + labs(title = "Calories burned vs Average intensity", x = "Average intensity", y = "Calories burned", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 1.5, y = 750, label =paste0("correlation, r: ", corr2), color = "red")
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Calories%20burned%20vs%20Average%20intensity-1.png)<!-- -->

**Conclusion: the higher the intensity, the more calories burned in a
day.**

Calories burned vs Total steps taken

``` r
corr3 <- round(cor(hourly_activity$step_total, hourly_activity$calories),4)

p4 <- hourly_activity %>%  ggplot(aes(x=step_total, y=calories))+
  geom_point()+geom_smooth()


p4 + labs(title = "Calories burned vs Total steps taken", x = "Total steps taken", y = "Calories burned", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 5000, y = 750, label =paste0("correlation, r: ", corr3), color = "red")
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Calories%20burned%20vs%20Total%20steps%20taken-1.png)<!-- -->

**Conclusion: the more steps taken, the more calories burned in a day.**

Calories burned vs Total distance

``` r
corr4 <- round(cor(daily_activity_ver2$total_distance,daily_activity_ver2$calories),4)

p5 <- daily_activity_ver2 %>% ggplot(aes(x=total_distance, y=calories))+geom_point()+geom_smooth()

p5 + labs(title = "Calories burned vs Total distance", x = "Total distance", y = "Calories burned", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 10, y = 4500, label =paste0("correlation, r: ", corr4), color = "red")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Calories%20burned%20vs%20Total%20distance-1.png)<!-- -->
**Conclusion: the more distance go for a day, the more calories burned
in a day.**

## Findings 1

**Findings: With high intensity, high total steps taken and more
distance taken, more calories will be burned as well**

### Analyze and visualize data 3 (explore relationship between intensity, total distance and sleep)

-   `inner_join` the daily sleep table and daily activity ver 1

Total distance vs Sedentary minutes

``` r
corr5 <- round(cor(daily_activity_ver2$total_distance,daily_activity_ver2$sedentary_minutes),4)

p6 <- daily_activity_ver2 %>% ggplot(aes(x=sedentary_minutes, y=total_distance))+geom_point()+geom_smooth()

p6 + labs(title = "Total distance vs Sedentary minutes", x = "Sedentary minutes", y = "Total distance", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 1250, y = 25, label =paste0("correlation, r: ", corr5), color = "red")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Total%20distance%20vs%20Sedentary%20minutes-1.png)<!-- -->
**Conclusion: the lesser minutes spent on sedentary behavior, the more
the distance taken.**

Sedentary minutes vs Total minutes asleep

``` r
combined_data <-inner_join(daily_sleep, daily_activity_ver1, by=c("id", "date"))
```

``` r
corr6<- round(cor(combined_data$total_minutes_asleep,combined_data$sedentary_minutes),4)

p7 <- combined_data %>% ggplot(aes(x=total_minutes_asleep,y=sedentary_minutes))+geom_point()+geom_smooth()

p7 + labs(title = "Sedentary minutes vs Total minutes asleep", x = "Total minutes asleep", y = "Sedentary minutes", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 500, y = 1200, label =paste0("correlation, r: ", corr6), color = "red")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Sedentary%20minutes%20vs%20Total%20minutes%20asleep-1.png)<!-- -->

**Conclusions: The more minutes a people fall asleep, the lesser minutes
spent on sedentary behavior**

## Findings 2

**Findings: With more minutes a people fall asleep, the person will
spend less minutes on sedentary behavior and take more distance.**

### Analyze and visualize data 4 (explore sleeping habits)

``` r
corr7 <- round(cor(daily_sleep$total_time_in_bed, daily_sleep$total_minutes_asleep),4)

p8 <- daily_sleep %>% ggplot(aes(x=total_time_in_bed, y=total_minutes_asleep))+geom_point()+geom_smooth()

p8 + labs(title = "Total minutes asleep vs Total time in bed", x = "Total time in bed", y = "Total minutes asleep", 
          caption=paste0("data collected from ", min_date, " to ",  max_date))+
  annotate(geom = "text", x = 500, y = 760, label =paste0("correlation: ", corr5), color = "red")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](case_study_files/figure-gfm/analyze%20&%20visualize%20Total%20minutes%20asleep%20vs%20Total%20time%20in%20bed-1.png)<!-- -->

**Findings: the more time on bed, the more minutes fall asleep.**

### Summary of findings:

-   **With more time on bed, people will fall asleep for more minutes.
    With good quality sleeping, the person will spend less minutes on
    sedentary behavior and take more distance.**
-   **With high intensity, high total steps taken and more distance
    taken, more calories will be burned as well**

### Linear regression (y = Sedentary minutes, x = total minutes asleep)

``` r
summary(lm(data = combined_data, sedentary_minutes ~ total_minutes_asleep))
```

    ## 
    ## Call:
    ## lm(formula = sedentary_minutes ~ total_minutes_asleep, data = combined_data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -810.91  -57.61    7.55   71.53  355.57 
    ## 
    ## Coefficients:
    ##                        Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)          1064.75015   24.12981   44.13   <2e-16 ***
    ## total_minutes_asleep   -0.84054    0.05537  -15.18   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 133 on 411 degrees of freedom
    ## Multiple R-squared:  0.3593, Adjusted R-squared:  0.3577 
    ## F-statistic: 230.5 on 1 and 411 DF,  p-value: < 2.2e-16

### Conclusions:

-   **Causation implied.**
-   **Total minutes asleep negatively affects sedentary behavior, On
    average, for 1 minutes increase in total minutes asleep, the minutes
    spent on sedentary behavior will decrease by 0.84 minutes**

### High-level recommendation:

-   **Add in new features to current smart device to track user’s
    sleeping habits.**
-   **Design a dashboard specifying the impact of good quality sleeping,
    eg. how it affects your daily life using data.**

### Next Analysis:

-   **Identify more on user’s sleeping habits.**
-   **Determine the 3 most important factors for a good quality
    sleeping.**
