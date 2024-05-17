# International Debt

### Table of Contents

- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Questions](#questions)
- [Data Cleaning](#data-cleaning)
- [Data Analysis](#data-analysis)
- [Results](#results)
- [Limitations](#limitations)
- [Visualization](#visualization)
### Project Overview

The goal of this project is too analyze international debt data from available countries to determine which countries have the most debt. The data goes back to 1970 for certain countries and can show trends in debt accumulation.

### Data Source

The dataset used for this project comes from the "IDSData.csv" file in the World Bank International Debt Statistics Kaggle page. [Download here](https://www.kaggle.com/datasets/theworldbank/world-bank-international-debt-statistics/data). The data consists of internation debt totals from 1970 onwards in select countries.

### Tools
 - Excel
 - R - Data Cleaning
 - SQL - Data Analysis
 - Tableau - Data Visualization

### Questions

 - What is the total world debt in the dataset as of 2017 (the last year of data)?
 - What countries have the most debt as of 2017?
 - How fast has world debt increased recently?

### Data Cleaning

```r
load(dplyr)
#load dataset
debt <- read_csv("C:/Users/mason/OneDrive/Desktop/SQL_projects/int_debt/ids-csv-zip-3-mb-/IDSData.csv")

#Filter for country debt and pivot data to make a year column
debt = debt %>%
  filter(`Indicator Code` == 'DT.UND.DPPG.CD') %>%
  pivot_longer(cols = 5:61, names_to = "Year", values_to = "Debt") %>%
  filter(Year <= 2017 & Year >= 1970)

#removing non-countries and renaming columns
debt = debt %>%
  filter(Country != 'South Asia')
debt = filter(debt, !grepl("income",`Country Name`))
debt = debt[-c(2449:2496),]
debt = filter(debt, !grepl("developed",`Country Name`)) %>%
  mutate(Year = as.numeric(Year)) %>%
  select(`Country Name`, `Country Code`, Year, Debt) %>%
  rename(Country = `Country Name`) %>%
  rename(Country_code = `Country Code`)
```

### Data analysis

 - Question 1

```sql
select sum(Debt) as Total,
                 from debt
                 where Year = 2017;
```

 - Question 2

```sql
select  Country, Country_code,Debt
                  from debt
                  where Year = 2017
                  order by Debt desc
                  limit 5
```

 - Question 3

```sql
create table debt_in_2017 as (
select sum(Debt) as Debt_2017, (select sum(Debt)
                                                   from debt
                                                   where Year = 2010) as Debt_2010
                   from debt
                   where Year = 2017);
select Debt_2017 as Total_debt, Debt_2010, Debt_2017/Debt_2010 * 100 as Debt_increase
                   from debt_in_2017;
```

### Results

1. The total world debt from the countries in the sample in 2017 was over $584 billion.
2. The countries with the most debt in 2017 in the sample were Cameroon, India, Egpyt, Bangladesh, and Vietnam respectively.
3. From 2010 to 2017, the collective internation debt from the sample grew by roughly 160%.

### Limitations

The sample did not include many countries, including countries with known huge debt figures such as the United States. In additions, many countries had missing values for some years since 1970.

### Visualization

A Tableau visualization can be seen [here](https://public.tableau.com/app/profile/mason.bates/viz/WorldInternationalDebt/Dashboard1)

