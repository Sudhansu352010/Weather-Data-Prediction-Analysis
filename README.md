The objective of this project is to gather weather data through web scraping, clean and preprocess the data, conduct analysis to gain insights about weather patterns, and create a dashboard to visualize the findings.


# Weather-Data-Prediction-Analysis
import numpy as np 
import pandas as pd 
import seaborn as sns
import matplotlib.pyplot as plt
import requests  

page = requests.get("https://www.timeanddate.com/weather/india/chennai/historic")

from bs4 import BeautifulSoup
soup=BeautifulSoup(page.content,"html.parser")

table=soup.find_all("table",{"class":"zebra tb-wt fw va-m tb-hover"})
l=[]
for i,items in enumerate(table):
    for i,row in enumerate(items.find_all("tr")):
        d = {}
        try:
#             print(i , row.find_all("td",{"class":""})[0].text)
            d['Temp'] = row.find_all("td",{"class":""})[0].text
        except:
            d['Temp'] = np.nan
            
        try:
#             print(i , row.find("td",{"class":"small"}).text)
            d['Weather'] = row.find("td",{"class":"small"}).text
        except:
            d['Weather']= np.nan
            
        try:   
#             print(i , row.find_all("td",{"class":"sep"})[0].text)
            d['Wind'] = row.find_all("td",{"class":"sep"})[0].text
        except:
            d['Wind'] = np.nan
            
        try:  
#             print(i, row.find("span",{"class":"comp sa16"})['title'])
            d['Direction'] = row.find("span")["title"]
        except:
            try:
                d['Direction'] = row.find("span",{"class":"comp sa16"})["title"]
            except:
                d['Direction'] = np.nan
            
        try:
#             print(i , row.find_all("td",{"class":""})[2].text)
            d['Humidity'] = row.find_all("td",{"class":""})[2].text
        except:
            d['Humidity'] = np.nan
        try:
#             print(i , row.find_all("td",{"class":"sep"})[1].text)
            d['Barometer'] =  row.find_all("td",{"class":"sep"})[1].text
        except:
            d['Barometer'] = np.nan
    
        try:
#             print(i , row.find_all("td",{"class":""})[3].text)
            d['Visibility'] =  row.find_all("td",{"class":""})[3].text
        except:
             d['Visibility'] = np.nan
                
        l.append(d)
        
import pandas

df = pandas.DataFrame(l)

df.to_csv('data.csv', columns = ['Temp', 'Weather', 'Wind', 'Direction', 'Humidity', 'Barometer',
       'Visibility'])
       
df

#Drop the Duplicates Value from the dataframe
df.drop_duplicates(inplace=True)

#Check the null values in the dataframe
df.isnull().sum()

df.dropna(inplace=True)

# Check the shape of DataFrame
df.shape

# Check the Datatypes and Change them accordingly
df.dtypes

df

for i in df.columns:
    print(i,df[i].sort_values().unique(),'\n',sep='\n')
df['Wind'] = df['Wind'].str.replace('km/h', '').astype('int64')
df['Temp'] = df['Temp'].str.replace('\xa0', ' ').str.replace('°C', '')
df['Temp']=df['Temp'].astype('float64')
df['Humidity'] = df['Humidity'].str.extract('(\d+)', expand=False).astype(pd.Int64Dtype())
df['Barometer'] = df['Barometer'].str.extract('(\d+)', expand=False).astype(pd.Int64Dtype())
df['Visibility'] = df['Visibility'].str.extract('(\d+)', expand=False).astype(pd.Int64Dtype())

df.dtypes
# Separate Categorical and Numerical Columns
cat=[]
num=[]
for i in df.columns:
    if df[i].dtypes=="O":
        cat.append(i)
    else:
        num.append(i)
        
cat
num
df.describe()
for i in cat:
    print(df[i].unique())
    
# To show the Relationship between Different Varaibles
sns.heatmap(df.corr(),annot=True)

# Group by 'Weather' column and calculate average wind speed
average_wind_speed = df.groupby('Weather')['Wind'].mean()
print(average_wind_speed)

# Calculate the average temperature
average_temperature = df['Temp'].mean()
print(average_temperature)

import matplotlib.pyplot as plt

# Plot a histogram of the 'Humidity' column
plt.hist(df['Humidity'], bins=10)
plt.xlabel('Humidity')
plt.ylabel('Frequency')
plt.show()

#Scatter plot: Visualize the relationship between two numeric variables.
import matplotlib.pyplot as plt

# Scatter plot of 'Wind' vs 'Humidity'
plt.scatter(df['Wind'], df['Humidity'])
plt.xlabel('Wind')
plt.ylabel('Humidity')
plt.title('Scatter plot of Wind vs Humidity')
plt.show()

#Bar chart: Compare categorical variables or display aggregated values.
import matplotlib.pyplot as plt

# Bar chart of average wind speed by weather condition
average_wind_speed = df.groupby('Weather')['Wind'].mean()
average_wind_speed.plot(kind='bar')
plt.xlabel('Weather')
plt.ylabel('Average Wind Speed')
plt.title('Average Wind Speed by Weather Condition')
plt.show()

#Box plot: Visualize the distribution and identify outliers.
import seaborn as sns

# Box plot of 'Barometer' column
sns.boxplot(x=df['Barometer'])
plt.xlabel('Barometer')
plt.title('Box Plot of Barometer')
plt.show()

#Line plot: Track the changes of a variable over time.
import matplotlib.pyplot as plt

# Line plot of 'Temp' over time
plt.plot(df.index, df['Temp'])
plt.xlabel('Time')
plt.ylabel('Temperature')
plt.title('Temperature over Time')
plt.show()

#  Save this file to csv
df.to_csv('output.csv', index=False)
