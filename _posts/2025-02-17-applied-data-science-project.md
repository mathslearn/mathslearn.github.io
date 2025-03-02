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

**Clean Data**
1. A total of 210+1+18=229 rows were dropped.
2. Number of rows were decreased from 10,000 to 9,771.

```python
print('Count number of duplicate rows:', df.duplicated().sum())
```

```
Count number of duplicate rows: 210
```

```
# Identify duplicate rows with keep='first' (this marks duplicates as True except for the first occurrence).
duplicates_to_drop = df.duplicated(keep='first') # This shows all rows, each with its Boolean result, True or False.

# Drop the duplicated rows directly from df.
df = df[~duplicates_to_drop]

# Reset the index of df.
df.reset_index(drop=True, inplace=True)

# Drop row with index 3404 because it is the same as row with index 3403.
df = df.drop(index=3404)

# Drop 18 rows where reviews.text=MoreMore.
df = df[df['reviews.text'] != 'MoreMore']
```

**Construct Data**
1. reviews.rating = 1, 2, 3, 4 or 5.
2. reviews.rating = 1 or 2 is for negative sentiment.
3. reviews.rating = 4 or 5 is for positive sentiment.
4. reviews.rating = 3 after checking first 20 rows is for negative sentiment.
5. create new variable 'sentiment' where 0 represents negative sentiment and 1 represent positive sentiment.

```python
     reviews.rating                                       reviews.text
0                 3  This hotel was nice and quiet. Did not know, t...
2                 3  Parking was horrible, somebody ran into my ren...
11                3  I stayed here for three nights while I explore...
13                3  The water is very hot and there's no cold wate...
18                3  The Whitney Hotel is ideally located to see mo...
45                3  The bar closed at 10pm which was poorOnly 3 da...
86                3  We stayed here after traveling through Rocky M...
98                3  The issues started the first night. Do Not sta...
105               3  If you are driving then this is for you! A bit...
106               3  First impression, I see vehicles parked tightl...
113               3  The hotel staff was friendly and engaging. The...
115               3  I had a friend in town, so was looking for a S...
128               3  This hotel is in a great location and the room...
132               3  super friendly staff, average breakfast, free ...
144               3  Nice place, but.........21 to park!Wifi would ...
160               3  Average place , over priced and they charge yo...
175               3  I chose this hotel because it was close to the...
179               3  Although the Best Western is aesthetically ple...
183               3  we checked in around 8pm room air conditioner ...
185               3  This is a 1960's era resort motel, a bit out d...
```

Figure: Number of rows with sentiment = 0 (negative) per month for each year
![image](https://github.com/user-attachments/assets/6ed64bef-56a7-4556-8d6f-32a57716d220)

Figure: Number of rows with sentiment = 1 (positive) per month for each year
![image](https://github.com/user-attachments/assets/5e1d3f0d-37db-477c-9c51-ebc6a065baba)

Figure: Number of Reviews per Sentiment per Year
![image](https://github.com/user-attachments/assets/a606b80b-ecbc-48ae-9bdb-31026fb9f946)

Figure: Percentage of Reviews per Sentiment per Year
![image](https://github.com/user-attachments/assets/ca87b939-ab17-453e-bf53-ae8cb1c59184)

6. Distribution of negative sentiment (sentiment=0) and positive sentiment (sentiment=1).
```python
   sentiment  count  percentage
1          0   2253   23.058029
0          1   7518   76.941971
```

8. Considered 'primaryCategories' but too highly imbalanced so will not be selected as feature to model.
```python
# Count the occurrences of each unique value in 'primaryCategories'
category_counts = df['primaryCategories'].value_counts()

# Print the result
print(category_counts)
```

```python
primaryCategories
Accommodation & Food Services                                                              9762
Accommodation & Food Services,Arts Entertainment & Recreation                                 7
Accommodation & Food Services,Administrative & Support & Waste Management & Remediation       1
Accommodation & Food Services,Agriculture                                                     1
```

**Format Data**
```python
# Convert 'reviews.date' column to datetime objects, specifying the correct format
df['reviews.date'] = pd.to_datetime(df['reviews.date'], format='ISO8601') # alternative for ISO8601 format
```

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
