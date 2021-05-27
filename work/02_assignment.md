---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.10.3
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Working with Data


>Using the data below, answer the following questions:

>1) Which entities (top 5) had the largest population density in 2020?
>2) Which entities have more water area than land area?
>3) Which entities increased in population the most in the last 10 years?
>4) What state bird accounts for the largest population as of 2020? Land area?
>5) How many entities' largest city is their capital city?
>6) Which city has the largest percent drop from their largest city to their 5th largest? 100*(1st largest - 5th largest)/(1st largest)

```python
import pandas as pd
```

```python
facts = pd.read_csv('../data/state_facts.tsv',delimiter="\t")
facts.head(5)
facts
```

<!-- #raw -->
1) District of Columbia, New Jersey, Rhoade Island, Puerto Rico, Massachusetts
<!-- #endraw -->

```python
facts['PopD'] = facts['Pop_2020']/facts['Area_land']
```

```python
popd = facts.sort_values(by='PopD',ascending=False)
```

```python
popd.head()
```

<!-- #raw -->
2) American Samoa, Guam, Northern Mariana Islands, United States Virgin Islands
<!-- #endraw -->

```python
facts[facts['Area_water']>facts['Area_land']]
```

<!-- #raw -->
3) Texas, Florida, California, Georgia, Washington
<!-- #endraw -->

```python
facts['PopInc'] = facts['Pop_2020']-facts['Pop_2010']
PopInc = facts.sort_values(by='PopInc',ascending=False)
PopInc.head()
```

<!-- #raw -->
4)
<!-- #endraw -->

```python
birds1 = facts[['Pop_2020','State_bird']].groupby(by='State_bird').sum()

birds1.sort_values(by='Pop_2020',ascending=False).head()
```

```python
birds2 = facts[['Area_land','State_bird']].groupby(by='State_bird').sum()

birds2.sort_values(by='Area_land',ascending=False).head()
```

<!-- #raw -->
5) 17
<!-- #endraw -->

```python
facts[(facts['City_1']==facts['Capital'])].groupby(by='State').sum().count()
```

<!-- #raw -->
6) New York
<!-- #endraw -->

```python
facts['%dC1-5'] = 100*(facts['city_1_pop']-facts['city_5_pop'])/(facts['city_1_pop'])
facts.sort_values(by='%dC1-5',ascending=False).head()
```

>Using the "state_dates.tsv" data, answer the remaining questions. You will need to merge the two data sets together:

>7) Of the states that joined the United States before 1790, what is the most common state flower?
>8) Which has the larger population density, the most dense US Territory or the least dense state?
>9) Make a graph that plots the populations of the largest city in each entity in the order in which they joined the US. Make the bars black
>10) Make two additional graphs like the one above but one for land area (green bars) and one for water area (blue bars)

<!-- #raw -->
7) Mount Laurel (2)
<!-- #endraw -->

```python
dates = pd.read_csv('../data/state_dates.tsv',delimiter="\t")
dates.head()
```

```python
facts.head()
```

```python
states = pd.merge(facts,dates,left_on='USPS_code',right_on='Abbreviation',how='outer')
states['Date']=pd.to_datetime(states['Date'])
states[states['Date']<pd.datetime(1790,1,1)]['State_flower'].value_counts()
```

<!-- #raw -->
8) Puerto Rico & Alaska
<!-- #endraw -->

```python
ter = states[states['Status']=='Territory']
```

```python
sta = states[states['Status']=='State']
```

```python
ter['PopDensity2020'] = ter['Pop_2020']/ter['Area_land']
ter = ter.sort_values(by='PopDensity2020', ascending=False)
ter[['State','PopDensity2020']].head(1)
```

```python
sta['PopDensity2020'] = sta['Pop_2020']/sta['Area_land']
sta = sta.sort_values(by='PopDensity2020', ascending=True)
sta[['State','PopDensity2020']].head(1)
```

Hint: `pd.read_csv('../data/state_dates.tsv',delimiter="\t")`

Hint: You likely want to convert the Date column to datetime. You might have to correct errors in the data as well.

Hint: `states['Date']<pd.datetime(1790,1,1)`

Hint: `pd.merge(****,****,left_on='USPS_code',right_on='Abbreviation',how='outer')`

<!-- #raw -->
9)
<!-- #endraw -->

```python
statesbydate = states.sort_values(by='Date')
```

```python
import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['city_1_pop','Abbreviation']].plot(kind='bar',figsize=(10,4),color='black')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

<!-- #raw -->
10)
<!-- #endraw -->

```python
import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['Area_land','Abbreviation']].plot(kind='bar',figsize=(10,4),color='green')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

```python
import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['Area_water','Abbreviation']].plot(kind='bar',figsize=(10,4),color='blue')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

```python
# Sample code to help with the plots

#import matplotlib as plt
#%config InlineBackend.figure_format ='retina' #This makes your plot clearer


#plot = *your df by date*[[*column*,'Abbreviation']].plot(kind='bar',figsize=(10,4))
#plot.set_xticklabels(*your df by date*['Abbreviation']);
```
