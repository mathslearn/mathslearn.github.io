---
layout: post
author: Name
title: "Applied Data Science Project Documentation"
categories: ITD214
---
**Nanyang Polytechnic**<br>
**Post-Diploma Certificate in Applied Data Science**<br>
**ITD214 Applied Data Science Project**<br>
**2 Mar 2025**

## Project Background
**Scenario Background**
1. Hotels in the USA have collected quantitative data (reviews.rating) and qualitative data (reviews.text) over a period of 15 years 5 months from 1 Sep 2003 to 30 Jan 2019.
2. Would like to see what actionable insights can be derived from the collected data.

**Business Goal**
1. To help USA hotels to improve their service.

**Business Objectives (Group 4)**
1. Identify what key topics consumers typically reveal in their reviews (topic modelling by Hazizul)
2. Predict whether a review is a positive or negative sentiment (sentiment analysis by Teng Teng)
3. Identify time period that drives positive or negative sentiment (time series analysis by Shao Mun)

## Work Accomplished
1. Identify time period that drives positive or negative sentiment (time series analysis by Shao Mun)

### Data Preparation
**Data Collection Sources**
1. Kaggle

**Acquire/Select Data**
1. Searched for reviews and found hotel reviews dataset at https://www.kaggle.com/datasets/datafiniti/hotel-reviews
2. Three datasets after download, chose dataset where reviews.rating were integers, Datafiniti_Hotel_Reviews_Jun19.csv

**Data Fields Description**
1. Number of rows: 10000
2. Number of columns: 26
3. df.dtypes:
```python
id                       object
dateAdded                object
dateUpdated              object
address                  object
categories               object
primaryCategories        object
city                     object
country                  object
keys                     object
latitude                float64
longitude               float64
name                     object
postalCode               object
province                 object
reviews.date             object
reviews.dateAdded       float64
reviews.dateSeen         object
reviews.rating            int64
reviews.sourceURLs       object
reviews.text             object
reviews.title            object
reviews.userCity         object
reviews.userProvince     object
reviews.username         object
sourceURLs               object
websites                 object
```

**Data Exploration**
1. Three fields with lesser categories shortlisted to explore further for modelling: 'primaryCategories', 'province' and 'reviews.rating'.
```python
Number of unique values in columns of df:
id                      1433
dateAdded               1341
dateUpdated             1397
address                 1432
categories               631
primaryCategories          4
city                     842
country                    1
keys                    1433
latitude                1430
longitude               1431
name                    1311
postalCode              1149
province                  46
reviews.date            3370
reviews.dateAdded          0
reviews.dateSeen         701
reviews.rating             5
reviews.sourceURLs      8228
reviews.text            9770
reviews.title           8470
reviews.userCity        3101
reviews.userProvince     244
reviews.username        9222
sourceURLs              1433
websites                1327
```

2. 'reviews.rating' most promising categorical variable to use as five categories can be easily grouped.
3. 'province' not attractive because need spend effort to group 46 categories further.
```python
Unique values in column 'primaryCategories': ['Accommodation & Food Services'
 'Accommodation & Food Services,Arts Entertainment & Recreation'
 'Accommodation & Food Services,Administrative & Support & Waste Management & Remediation'
 'Accommodation & Food Services,Agriculture']
Unique values in column 'country': ['US']
Unique values in column 'province': ['CA' 'KY' 'LA' 'CO' 'IL' 'IN' 'FL' 'AK' 'GA' 'AL' 'AZ' 'AR' 'OR' 'WA'
 'UT' 'TX' 'TN' 'SC' 'PA' 'OH' 'NY' 'NM' 'MD' 'MI' 'MS' 'MO' 'IA' 'VA'
 'WI' 'HI' 'ID' 'NV' 'WV' 'WY' 'KS' 'MN' 'NE' 'ND' 'DE' 'OK' 'NC' 'MT'
 'SD' 'RI' 'NJ' 'MA']
Unique values in column 'reviews.dateAdded': [nan]
Unique values in column 'reviews.rating': [3 4 5 2 1]
```

**Data Quality**
1. Majority of fields have all rows filled, especially those with potential for modelling: 'reviews.date', 'reviews.rating' and 'reviews.text' (each with 10k rows).
```python
Count number of rows with non-empty values:
id                      10000
dateAdded               10000
dateUpdated             10000
address                 10000
categories              10000
primaryCategories       10000
city                    10000
country                 10000
keys                    10000
latitude                10000
longitude               10000
name                    10000
postalCode              10000
province                10000
reviews.date            10000
reviews.dateAdded           0
reviews.dateSeen        10000
reviews.rating          10000
reviews.sourceURLs      10000
reviews.text            10000
reviews.title            9999
reviews.userCity        10000
reviews.userProvince     9998
reviews.username        10000
sourceURLs              10000
websites                10000
```

**CLean Data**
```python
print('Count number of duplicate rows:', df.duplicated().sum())
Count number of duplicate rows: 210
```

Alternatively, if you want to make the output clearer, you can label it explicitly:

```md
```python
# Python code
print("Hello, World!")

### Modelling
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce bibendum neque eget nunc mattis eu sollicitudin enim tincidunt. Vestibulum lacus tortor, ultricies id dignissim ac, bibendum in velit. Proin convallis mi ac felis pharetra aliquam. Curabitur dignissim accumsan rutrum. In arcu magna, aliquet vel pretium et, molestie et arcu. Mauris lobortis nulla et felis ullamcorper bibendum. Phasellus et hendrerit mauris. Proin eget nibh a massa vestibulum pretium. Suspendisse eu nisl a ante aliquet bibendum quis a nunc. Praesent varius interdum vehicula. Aenean risus libero, placerat at vestibulum eget, ultricies eu enim. Praesent nulla tortor, malesuada adipiscing adipiscing sollicitudin, adipiscing eget est.

### Evaluation
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce bibendum neque eget nunc mattis eu sollicitudin enim tincidunt. Vestibulum lacus tortor, ultricies id dignissim ac, bibendum in velit. Proin convallis mi ac felis pharetra aliquam. Curabitur dignissim accumsan rutrum. In arcu magna, aliquet vel pretium et, molestie et arcu. Mauris lobortis nulla et felis ullamcorper bibendum. Phasellus et hendrerit mauris. Proin eget nibh a massa vestibulum pretium. Suspendisse eu nisl a ante aliquet bibendum quis a nunc. Praesent varius interdum vehicula. Aenean risus libero, placerat at vestibulum eget, ultricies eu enim. Praesent nulla tortor, malesuada adipiscing adipiscing sollicitudin, adipiscing eget est.

## Recommendation and Analysis
Explain the analysis and recommendations

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce bibendum neque eget nunc mattis eu sollicitudin enim tincidunt. Vestibulum lacus tortor, ultricies id dignissim ac, bibendum in velit. Proin convallis mi ac felis pharetra aliquam. Curabitur dignissim accumsan rutrum. In arcu magna, aliquet vel pretium et, molestie et arcu. Mauris lobortis nulla et felis ullamcorper bibendum. Phasellus et hendrerit mauris. Proin eget nibh a massa vestibulum pretium. Suspendisse eu nisl a ante aliquet bibendum quis a nunc. Praesent varius interdum vehicula. Aenean risus libero, placerat at vestibulum eget, ultricies eu enim. Praesent nulla tortor, malesuada adipiscing adipiscing sollicitudin, adipiscing eget est.

## AI Ethics
Discuss the potential data science ethics issues (privacy, fairness, accuracy, accountability, transparency) in your project. 

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce bibendum neque eget nunc mattis eu sollicitudin enim tincidunt. Vestibulum lacus tortor, ultricies id dignissim ac, bibendum in velit. Proin convallis mi ac felis pharetra aliquam. Curabitur dignissim accumsan rutrum. In arcu magna, aliquet vel pretium et, molestie et arcu. Mauris lobortis nulla et felis ullamcorper bibendum. Phasellus et hendrerit mauris. Proin eget nibh a massa vestibulum pretium. Suspendisse eu nisl a ante aliquet bibendum quis a nunc. Praesent varius interdum vehicula. Aenean risus libero, placerat at vestibulum eget, ultricies eu enim. Praesent nulla tortor, malesuada adipiscing adipiscing sollicitudin, adipiscing eget est.

## Source Codes and Datasets
Upload your model files and dataset into a GitHub repo and add the link here. 
https://github.com/mathslearn/itd214_proj
