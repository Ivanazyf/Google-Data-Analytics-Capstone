# Google-Data-Analytics-Capstone
## Cyclisitic Bike-Share Analytics Project

## Scenario
Suppose you are a junior data analyst working for a fictional company, Cyclistic which is a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, our goal is to design a new marketing strategy to convert casual riders into annual members. Before that, we need to understand how casual riders and annual members use Cyclistic bikes differently.

I will use six steps analysis process to deal with this problem, including Ask, Prepare, Process, Analyze, Share and Act.

## Ask
Three questions will guide us through future analysis:

* How do annual members and casual riders use our Cyclistic bikes differently?
* Why would casual members buy Cyclistic annual memberships?
* How can Cyclistic use digital media to influence casual riders to become members?

## Prepare
We will use Cyclistic’s historical trip data from 2022 July to 2023 July to analyze and identify trends. [DataLink](https://divvy-tripdata.s3.amazonaws.com/index.html).
The data is first-party data collected by Cyclist so there is low chance of bias and with high credibility. The data also does ROCCC as it is reliable, original, comprehensive, current, and cited.

## Process
Since this is a large dataset, with 5,779,444 rows, I choose RStudio to process data.

### Install packages
```{r echo=T, results="hide"}
install.packages("tidyverse")
install.packages("ggplot2")
install.packages("janitor")
install.packages('lubridate')

library("janitor")
library('tidyverse')
library('ggplot2')
library('lubridate')
```

### Import Data
```{r echo=T, results="hide"}
Jul2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_07.csv")
Aug2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_08.csv")
Sep2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_09.csv")
Oct2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_10.csv")
Nov2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_11.csv")
Dec2022 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2022_12.csv")
Jan2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_01.csv")
Feb2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_02.csv")
Mar2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_03.csv")
Apr2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_04.csv")
May2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_05.csv")
Jun2023 <- read_csv("/Users/ivanazhao/Desktop/Coursera/Google Analytics/Capstone Project/monthly_trip_data/2023_06.csv")
```

```{r}
str(Jul2022)
str(Aug2022)
str(Sep2022)
str(Oct2022)
str(Nov2022)
str(Dec2022)
str(Jan2023)
str(Feb2023)
str(Mar2023)
str(Apr2023)
str(May2023)
str(Jun2023)
```

**After analyzing data structures, we noticed those datasets have exactly the same structure, so we decided to join them together**

### Merge and Clean Datasets
```{r}
df <- bind_rows(Jul2022, Aug2022, Sep2022, Oct2022, Nov2022, Dec2022, Jan2023, Feb2023, Mar2023, Apr2023, May2023, Jun2023)

head(df)
str(df)
```

```{r}
# Clean column names
clean_names(df)

# Remove empty rows and columns
remove_empty(df, which=c())
```

### Add New Columns
```{r}
# Calculate length of each ride
df <- df %>%
        mutate(ride_length = (ended_at - started_at)/60)

# Remove rows with negative ride_length values
df <- df[df$ride_length >= 0, ]

# Create column "day_of_week"
df <- df %>%
        mutate(day_of_week = wday(started_at))

# Create column "month"
df <- df %>%
        mutate(month = format(as.Date(started_at), "%m"))

# Change data type
df$ride_length <- as.numeric(df$ride_length)
df$month <- as.numeric(df$month)
df$day_of_week <- as.numeric(df$day_of_week)

df
summary(df)
```
**Note: For day_of_week, 1 indicates Sunday, 2 indicates Monday, and so on.**

## Analyze
### Descriptive Analysis
```{r}
df %>%
  group_by(member_casual) %>%
  drop_na() %>%
  summarise(avg_ride_length = mean(ride_length), std_ride_length = sd(ride_length),max_ride_length = max(ride_length),min_ride_length = min(ride_length))

```
```{r}
df %>%
  count(member_casual)
```

### Plots
```{r}
ggplot(data = df) +
  geom_bar(mapping = aes(x = month, fill = member_casual), position = 'dodge')+
  labs(x = 'Month', y = 'Number of rides', fill = 'Member type', title = 'Number of rides per month')
```
```{r}
ggplot(data = df) +
  geom_bar(mapping = aes(x = day_of_week, fill = member_casual), position = 'dodge')+
  labs(x = 'Day of week', y = 'Number of rides', fill = 'Member type', title = 'Number of rides per day of week')
```

## Share
### Export data to Tableau
```{r}
# write.csv(df, file="cleaned_bike_data.csv")
```

**[Link to Tableau Dashboard](https://public.tableau.com/app/profile/ivana.zhao/viz/GoogleDataAnalyticsCapstone_16953506078840/Dashboard1)**

## Act
Based on the dashboard, it seems annual members ride bikes on weekdays more frequently while casual members prefer weekends to ride. Therefore, we could infer that annual members are mainly composed of workers who ride bikes to commute while casual members are mostly tourists or families who ride bikes to enjoy the weekend.

We also noticed that the riding peak happened in summer, including June, July and August. Both casual and annual members prefer electric and classic bikes. Therefore, in terms of marketing strategies, we propose firstly, we could use social medias like Instagram & Facebook to advertise during summer time and focus on electric and classic bike types. Secondly, we could increase the price of single-day & full-day passes to appeal people to upgrade to annual membership. Thirdly, we could lower the price of annual membership during summer time.

Though the dataset is large, some restrictions still exist. For example, if we could have unique user IDs, it would be helpful for us to make some strategies specific to each individual.
