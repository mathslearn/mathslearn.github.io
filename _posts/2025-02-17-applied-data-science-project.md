---
layout: post
author: Wong S M
title: "Applied Data Science Project Documentation"
categories: ITD214
---
**Nanyang Polytechnic**<br>
**Post-Diploma Certificate in Applied Data Science**<br>
**ITD214 Applied Data Science Project**<br>
**Wong S M**<br>
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

```python
Count number of duplicate rows: 210
```

```python
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
1. Convert 'reviews.date' column to datetime objects, specifying the correct format.

```python
df['reviews.date'] = pd.to_datetime(df['reviews.date'], format='ISO8601') # alternative for ISO8601 format
```

### Modelling
**Construct Data**
1. Extract year, month, day, weekofyear and day_of_week for time series analysis.
2. Apply one-hot encoding for day_of_week.

```python
# Extract year, month, day and weekofyear from 'reviews.date'.
df['year'] = df['reviews.date'].dt.year
df['month'] = df['reviews.date'].dt.month
df['day'] = df['reviews.date'].dt.day
df['weekofyear'] = df['reviews.date'].dt.isocalendar().week

# Extract day of the week (e.g., Mon, Tue, etc.)
df['day_of_week'] = df['reviews.date'].dt.strftime('%a')  # Short format (Mon, Tue)

# Apply one-hot encoding for df['day_of_week'] column.
df = pd.get_dummies(df, columns=['day_of_week'], dtype=int)
```

**Feature Selection: Correlation Analysis**
1. Heatmap shows sentiment to be highly positively correlated to reviews.rating which is expected as sentiment is a derived variable of reviews.rating.

Figure: Correlation Heatmap of df
![image](https://github.com/user-attachments/assets/79b83ff7-57ad-4c9b-bbad-ba6b418fe73f)

**Integrate Data**
1. Declare time-related variables as features and sentiment as target variable.

```python
# Declare features, X with columns: year, month, day, weekofyear, day_of_week_Mon, day_of_week_Tue, day_of_week_Wed, day_of_week_Thu, day_of_week_Fri, day_of_week_Sat, day_of_week_Sun.
x = df[['year', 'month', 'day', 'weekofyear', 'day_of_week_Mon', 'day_of_week_Tue', 'day_of_week_Wed',
        'day_of_week_Thu', 'day_of_week_Fri', 'day_of_week_Sat', 'day_of_week_Sun']]

# Declare target variable, Y with column: sentiment.
y = df['sentiment']
```

**Model Design**
1. Machine Learning Models
   * K-Nearest Neighbors: n_neighbors tuned from 5 to 50 in stepsize of 5
   * Support Vector Machine (SVM)
   * Logistic Regression
   * Decision Trees: min_samples_split tuned as 2, 10, 20, 30
   * Gaussian Naive Bayes
   * Random Forest: n_estimators tuned from 5 to 30 in stepsize of 5
   * Gradient Boosting

2. AutoRegressive Integrated Moving Average (ARIMA) Time Series Forecasting Model
   * daily
   * weekly
   * yearly

### Evaluation
**Machine Learning Models (first iteration)**
1. Performance metrics: Mean CV Accuracy, Train Accuracy, Test Accuracy, Train Precision, Test Precision.
2. Highest accuracies came from KNC models.
3. But model cannot predict negative sentiment.

![image](https://github.com/user-attachments/assets/f2b8c3d1-0fad-4512-8dfb-0ed7399f51a5)

![image](https://github.com/user-attachments/assets/471d2c53-e1a3-47e0-888b-d94d97f0c0f9)

**Machine Learning Models (second iteration)**
1. Tried undersampling of the majority class (sentiment=1) in the training set.
2. Highest accuracy came from GNB model.
3. Accuracies worsened from no sampling to undersampling of the majority class (sentiment=1) in the training set.
4. However model can predict negative sentiment but not very well.
5. Since the machine learning models do not seem to predict well, proceeded to try AutoRegressive Integrated Moving Average (ARIMA) time series forecasting model.

![image](https://github.com/user-attachments/assets/21947d68-7c68-4bff-8960-c1bfc5d57eb2)

![image](https://github.com/user-attachments/assets/04086bfa-70d0-4662-b419-a7a54d32abdf)

**AutoRegressive Integrated Moving Average (ARIMA) Time Series Forecasting Model**
1. Need check whether time series data is stationary.
2. Check reveals time series data is stationary.
3. The null hypothesis of the Augmented Dickey-Fuller (ADF) test is that the series is non-stationary.
4. Since the p-value is significantly less than 0.05 (in fact, it's very close to 0), you can reject the null hypothesis and conclude that the data is stationary.
5. The ADF Statistic (-12.11) is much smaller than the critical values at the 1%, 5%, and 10% levels (e.g., -3.43 at the 1% level). This further confirms that the data does not have a unit root and is indeed stationary.
6. ARIMA modelling needs time series to be stationary. Since the ADF test shows that the series is stationary, therefore can proceed to do ARIMA modelling.

```python
# Augmented Dickey-Fuller (ADF) test. The null hypothesis of the ADF test is that the series is non-stationary.
from statsmodels.tsa.stattools import adfuller

# Perform ADF test on the 'sentiment' column (you can replace with your target variable)
result = adfuller(df3['sentiment'])

# Print the results
print("ADF Statistic:", result[0])
print("p-value:", result[1])
print("Critical Values:", result[4])
```

```python
ADF Statistic: -12.111143448908674
p-value: 1.9181150644839028e-22
Critical Values: {'1%': -3.4310214251582605, '5%': -2.8618367291146485, '10%': -2.56692794378353}
```

**AutoRegressive Integrated Moving Average (ARIMA) Time Series Forecasting Model**
1. Three models were ran: Daily, weekly and monthly.
2. Performance metrics: Mean Absolute Error (MAE), Mean Squared Error (MSE), Root Mean Squared Error (RMSE).
3. Results were close to one another.
![image](https://github.com/user-attachments/assets/d100eae5-20a5-4ee7-8dd8-8975d026174a)
4. Monthly model most accurate.
5. Daily model the worst: More sensitive to short-term variations so less stable and harder to use for long-term forecasting.
6. Rather say which model is the best, better to view each model serves its own purpose.
7. Daily model helps with daily analysis of sentiment spike.
8. Weekly model can help to smooth out the daily fluctuations.
9. Monthly model gives a broader picture of sentiment trends which is useful for business or marketing strategies.

Figure: Daily model shows 30-day forecast of sentiment = 0.8 (80% likely positive)

![image](https://github.com/user-attachments/assets/c0cee53a-feb7-4a16-b035-ab8fabc0d029)

![image](https://github.com/user-attachments/assets/7f4351de-7bbf-4f69-a684-8371d6175244)

Figure: Weekly model shows 12-week forecast of sentiment = 0.8 (80% likely positive)

![image](https://github.com/user-attachments/assets/a9f9db7f-6963-4363-9d99-333b5bc6199d)

![image](https://github.com/user-attachments/assets/010187d7-1340-4c36-a5c8-83cb11f0106a)

Figure: Monthly model shows 12-month forecast of sentiment = 0.8 (80% likely positive)

![image](https://github.com/user-attachments/assets/b8ad0863-d46c-4108-b927-cc563e3b76d8)

![image](https://github.com/user-attachments/assets/8349abe6-4b97-439c-8598-fa3102e46416)

## Recommendation and Analysis
1. All 3 models (daily, weekly, monthly) shows forecast of sentiment = 0.8 (80% likely positive).
2. Means a likely 20% chance of getting negative sentiment.
3. Hotel can reschedule existing staff or hire more staff to have 20% more man hours to handle potential negative sentiment.

## AI Ethics
Discuss the potential data science ethics issues (privacy, fairness, accuracy, accountability, transparency) in your project. 



## Source Codes and Datasets
Upload your model files and dataset into a GitHub repo and add the link here. 
https://github.com/mathslearn/itd214_proj
