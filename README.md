
# Analysis
1.There is a strong correlation between the cities that are closer to the equator and higher temperatures.


2.There doesn't appear to be a correlation between latitude and cloudiness, as it is random and seems to be cloudy evenly everywhere.


3.There seems to generally be higher wind speeds south of the equator.


```python
#Dependencies
import requests
import matplotlib.pyplot as plt
import matplotlib
import pandas as pd
from citipy import citipy
import csv
import random
import math
from config import api_key
import numpy as np
import time
```


```python
# Build data frame with random lat/lon and closest city, country
lat_zone = np.arange(-45, 45,15)
lon_zone = np.arange(-90,90,15)

# Set up DataFrame to hold randomly picked lat/lon coordinates
cities_df = pd.DataFrame()
cities_df["Latitude"]=""
cities_df["Longitude"]=""
# Pick random coordinates
for x in lat_zone:
    for y in lon_zone:
        x_values = list(np.arange(x, x+15, .01, dtype = float))
        y_values = list(np.arange(y, y+15, .01, dtype = float))
        lats = random.sample(x_values, 10)
        lons = random.sample(y_values, 10)
        lat_samples = [(x+ran_lat) for ran_lat in lats]
        lon_samples = [(y+ran_lon) for ran_lon in lons]
        cities_df = cities_df.append(pd.DataFrame.from_dict({"Latitude":lat_samples,"Longitude":lon_samples}))

cities_df = cities_df.reset_index(drop=True)
cities_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-81.82</td>
      <td>-171.70</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-89.52</td>
      <td>-165.10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-79.95</td>
      <td>-175.02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-88.77</td>
      <td>-172.33</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-87.05</td>
      <td>-166.65</td>
    </tr>
  </tbody>
</table>
</div>




```python
cities_df = cities_df.drop_duplicates()
cities_df.shape
```




    (720, 2)




```python
cities_df["Closest City Name"]=''
cities_df["Closest Country Code"]=''
for index, row in cities_df.iterrows():
    city=citipy.nearest_city(row["Latitude"],row["Longitude"])
    cities_df.set_value(index, "Closest City Name", city.city_name)
    cities_df.set_value(index, "Closest Country Code", city.country_code)
cities_df.head()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:5: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      """
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:6: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Closest City Name</th>
      <th>Closest Country Code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-81.82</td>
      <td>-171.70</td>
      <td>vaini</td>
      <td>to</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-89.52</td>
      <td>-165.10</td>
      <td>mataura</td>
      <td>pf</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-79.95</td>
      <td>-175.02</td>
      <td>vaini</td>
      <td>to</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-88.77</td>
      <td>-172.33</td>
      <td>vaini</td>
      <td>to</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-87.05</td>
      <td>-166.65</td>
      <td>mataura</td>
      <td>pf</td>
    </tr>
  </tbody>
</table>
</div>




```python
selected_cities = cities_df.sample(500)
selected_cities = selected_cities.reset_index(drop=True)
selected_cities
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Closest City Name</th>
      <th>Closest Country Code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>72.58</td>
      <td>-22.57</td>
      <td>illoqqortoormiut</td>
      <td>gl</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-88.77</td>
      <td>-172.33</td>
      <td>vaini</td>
      <td>to</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8.85</td>
      <td>-22.36</td>
      <td>praia</td>
      <td>cv</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-20.30</td>
      <td>103.93</td>
      <td>carnarvon</td>
      <td>au</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.62</td>
      <td>7.79</td>
      <td>port-gentil</td>
      <td>ga</td>
    </tr>
    <tr>
      <th>5</th>
      <td>-76.05</td>
      <td>95.57</td>
      <td>albany</td>
      <td>au</td>
    </tr>
    <tr>
      <th>6</th>
      <td>67.01</td>
      <td>122.90</td>
      <td>zhigansk</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>7</th>
      <td>-23.04</td>
      <td>69.48</td>
      <td>grand river south east</td>
      <td>mu</td>
    </tr>
    <tr>
      <th>8</th>
      <td>13.73</td>
      <td>-78.40</td>
      <td>san andres</td>
      <td>co</td>
    </tr>
    <tr>
      <th>9</th>
      <td>66.77</td>
      <td>63.53</td>
      <td>yeletskiy</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>10</th>
      <td>-77.57</td>
      <td>-24.10</td>
      <td>ushuaia</td>
      <td>ar</td>
    </tr>
    <tr>
      <th>11</th>
      <td>71.04</td>
      <td>123.47</td>
      <td>zhigansk</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>12</th>
      <td>36.82</td>
      <td>-22.31</td>
      <td>vila franca do campo</td>
      <td>pt</td>
    </tr>
    <tr>
      <th>13</th>
      <td>36.14</td>
      <td>-108.44</td>
      <td>farmington</td>
      <td>us</td>
    </tr>
    <tr>
      <th>14</th>
      <td>74.33</td>
      <td>100.93</td>
      <td>khatanga</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>15</th>
      <td>-78.72</td>
      <td>-135.59</td>
      <td>rikitea</td>
      <td>pf</td>
    </tr>
    <tr>
      <th>16</th>
      <td>70.47</td>
      <td>-27.91</td>
      <td>illoqqortoormiut</td>
      <td>gl</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2.06</td>
      <td>156.01</td>
      <td>namatanai</td>
      <td>pg</td>
    </tr>
    <tr>
      <th>18</th>
      <td>37.77</td>
      <td>-18.38</td>
      <td>camacha</td>
      <td>pt</td>
    </tr>
    <tr>
      <th>19</th>
      <td>-54.10</td>
      <td>72.63</td>
      <td>souillac</td>
      <td>mu</td>
    </tr>
    <tr>
      <th>20</th>
      <td>-52.26</td>
      <td>0.70</td>
      <td>cape town</td>
      <td>za</td>
    </tr>
    <tr>
      <th>21</th>
      <td>11.42</td>
      <td>-143.02</td>
      <td>hilo</td>
      <td>us</td>
    </tr>
    <tr>
      <th>22</th>
      <td>-21.24</td>
      <td>90.54</td>
      <td>bengkulu</td>
      <td>id</td>
    </tr>
    <tr>
      <th>23</th>
      <td>-77.58</td>
      <td>-107.66</td>
      <td>punta arenas</td>
      <td>cl</td>
    </tr>
    <tr>
      <th>24</th>
      <td>65.91</td>
      <td>-136.67</td>
      <td>mayo</td>
      <td>ca</td>
    </tr>
    <tr>
      <th>25</th>
      <td>-19.72</td>
      <td>155.54</td>
      <td>yeppoon</td>
      <td>au</td>
    </tr>
    <tr>
      <th>26</th>
      <td>-49.70</td>
      <td>11.80</td>
      <td>hermanus</td>
      <td>za</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1.27</td>
      <td>122.07</td>
      <td>gorontalo</td>
      <td>id</td>
    </tr>
    <tr>
      <th>28</th>
      <td>10.93</td>
      <td>-106.49</td>
      <td>lazaro cardenas</td>
      <td>mx</td>
    </tr>
    <tr>
      <th>29</th>
      <td>42.54</td>
      <td>-138.07</td>
      <td>port hardy</td>
      <td>ca</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>470</th>
      <td>13.59</td>
      <td>-114.04</td>
      <td>cabo san lucas</td>
      <td>mx</td>
    </tr>
    <tr>
      <th>471</th>
      <td>-16.39</td>
      <td>92.56</td>
      <td>bengkulu</td>
      <td>id</td>
    </tr>
    <tr>
      <th>472</th>
      <td>-17.00</td>
      <td>-55.86</td>
      <td>barao de melgaco</td>
      <td>br</td>
    </tr>
    <tr>
      <th>473</th>
      <td>-17.73</td>
      <td>73.47</td>
      <td>grand river south east</td>
      <td>mu</td>
    </tr>
    <tr>
      <th>474</th>
      <td>-81.92</td>
      <td>-24.31</td>
      <td>ushuaia</td>
      <td>ar</td>
    </tr>
    <tr>
      <th>475</th>
      <td>-48.46</td>
      <td>8.43</td>
      <td>cape town</td>
      <td>za</td>
    </tr>
    <tr>
      <th>476</th>
      <td>66.72</td>
      <td>11.14</td>
      <td>sandnessjoen</td>
      <td>no</td>
    </tr>
    <tr>
      <th>477</th>
      <td>2.30</td>
      <td>4.05</td>
      <td>yenagoa</td>
      <td>ng</td>
    </tr>
    <tr>
      <th>478</th>
      <td>-56.50</td>
      <td>131.79</td>
      <td>new norfolk</td>
      <td>au</td>
    </tr>
    <tr>
      <th>479</th>
      <td>41.85</td>
      <td>123.92</td>
      <td>fushun</td>
      <td>cn</td>
    </tr>
    <tr>
      <th>480</th>
      <td>1.76</td>
      <td>162.87</td>
      <td>butaritari</td>
      <td>ki</td>
    </tr>
    <tr>
      <th>481</th>
      <td>-21.64</td>
      <td>101.74</td>
      <td>carnarvon</td>
      <td>au</td>
    </tr>
    <tr>
      <th>482</th>
      <td>-76.20</td>
      <td>-146.26</td>
      <td>mataura</td>
      <td>pf</td>
    </tr>
    <tr>
      <th>483</th>
      <td>-22.87</td>
      <td>121.88</td>
      <td>port hedland</td>
      <td>au</td>
    </tr>
    <tr>
      <th>484</th>
      <td>-47.72</td>
      <td>98.77</td>
      <td>busselton</td>
      <td>au</td>
    </tr>
    <tr>
      <th>485</th>
      <td>71.57</td>
      <td>95.59</td>
      <td>khatanga</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>486</th>
      <td>66.20</td>
      <td>-137.99</td>
      <td>mayo</td>
      <td>ca</td>
    </tr>
    <tr>
      <th>487</th>
      <td>-47.15</td>
      <td>-51.79</td>
      <td>mar del plata</td>
      <td>ar</td>
    </tr>
    <tr>
      <th>488</th>
      <td>63.48</td>
      <td>96.99</td>
      <td>baykit</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>489</th>
      <td>14.31</td>
      <td>38.14</td>
      <td>aksum</td>
      <td>et</td>
    </tr>
    <tr>
      <th>490</th>
      <td>63.25</td>
      <td>-26.05</td>
      <td>olafsvik</td>
      <td>is</td>
    </tr>
    <tr>
      <th>491</th>
      <td>38.54</td>
      <td>125.79</td>
      <td>seoul</td>
      <td>kr</td>
    </tr>
    <tr>
      <th>492</th>
      <td>-46.17</td>
      <td>-28.24</td>
      <td>arraial do cabo</td>
      <td>br</td>
    </tr>
    <tr>
      <th>493</th>
      <td>-49.22</td>
      <td>62.76</td>
      <td>saint-philippe</td>
      <td>re</td>
    </tr>
    <tr>
      <th>494</th>
      <td>43.06</td>
      <td>-140.95</td>
      <td>sitka</td>
      <td>us</td>
    </tr>
    <tr>
      <th>495</th>
      <td>3.24</td>
      <td>129.72</td>
      <td>ternate</td>
      <td>id</td>
    </tr>
    <tr>
      <th>496</th>
      <td>-24.63</td>
      <td>162.88</td>
      <td>poya</td>
      <td>nc</td>
    </tr>
    <tr>
      <th>497</th>
      <td>63.44</td>
      <td>-55.25</td>
      <td>maniitsoq</td>
      <td>gl</td>
    </tr>
    <tr>
      <th>498</th>
      <td>-23.36</td>
      <td>-49.83</td>
      <td>jacarezinho</td>
      <td>br</td>
    </tr>
    <tr>
      <th>499</th>
      <td>-20.43</td>
      <td>-28.59</td>
      <td>caravelas</td>
      <td>br</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 4 columns</p>
</div>




```python
row_count = 0

# Create blank columns for the necessary fields.

selected_cities["Temperature"] = ""
selected_cities["Humidity"] = ""
selected_cities["Cloudiness"] = ""
selected_cities["Wind Speed"] = ""

# Loop through and get the weather data using the Open Weather API.

for index, row in selected_cities.iterrows():
    #Sleep to help with the call limit
    
    time.sleep(1)
    
    #Open weather url.
    
    url = "https://api.openweathermap.org/data/2.5/weather?"
    units = "Imperial"
    
    query_url = url + "lat="+ str(row["Latitude"]) + "&lon=" + str(row["Longitude"])+ "&appid=" + api_key + "&units=" + units
    #Print log to ensure the loop is working correctly.
    
    print("Now retrieving city # " + str(row_count))
    print(query_url)
    row_count += 1
    
    city_weather = requests.get(query_url).json()
   
    #Append the weather data to the appropriate columns.
    #Use try/except to skip any cities with errors.
    #selected_cities.append(requests.get(query_url + str(city_weather)).json())
    try:
        temperature = city_weather["main"]["temp"]
        humidity = city_weather["main"]["humidity"]
        cloudiness = city_weather["clouds"]["all"]
        wind_speed = city_weather["wind"]["speed"]
        name = city_weather['name']
        selected_cities.set_value(index, "Temperature", temperature)
        selected_cities.set_value(index,"Humidity", humidity)
        selected_cities.set_value(index,"Cloudiness", cloudiness)
        selected_cities.set_value(index,"Wind Speed", wind_speed)

    except:
        print("Error with weather data, skipping.")
        #continue
selected_cities
```

    Now retrieving city # 0
    https://api.openweathermap.org/data/2.5/weather?lat=72.58000000000197&lon=-22.570000000000157&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:40: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:41: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:42: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:43: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    

    Now retrieving city # 1
    https://api.openweathermap.org/data/2.5/weather?lat=-88.77000000000024&lon=-172.32999999999606&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 2
    https://api.openweathermap.org/data/2.5/weather?lat=8.85&lon=-22.360000000000163&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 3
    https://api.openweathermap.org/data/2.5/weather?lat=-20.300000000000207&lon=103.92999999999722&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 4
    https://api.openweathermap.org/data/2.5/weather?lat=0.62&lon=7.79&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 5
    https://api.openweathermap.org/data/2.5/weather?lat=-76.05000000000277&lon=95.56999999999888&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 6
    https://api.openweathermap.org/data/2.5/weather?lat=67.0100000000011&lon=122.89999999999942&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 7
    https://api.openweathermap.org/data/2.5/weather?lat=-23.04000000000015&lon=69.48000000000148&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 8
    https://api.openweathermap.org/data/2.5/weather?lat=13.73&lon=-78.40000000000231&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 9
    https://api.openweathermap.org/data/2.5/weather?lat=66.77000000000106&lon=63.530000000000555&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 10
    https://api.openweathermap.org/data/2.5/weather?lat=-77.57000000000247&lon=-24.100000000000126&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 11
    https://api.openweathermap.org/data/2.5/weather?lat=71.04000000000173&lon=123.46999999999932&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 12
    https://api.openweathermap.org/data/2.5/weather?lat=36.81999999999985&lon=-22.310000000000166&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 13
    https://api.openweathermap.org/data/2.5/weather?lat=36.13999999999987&lon=-108.4400000000023&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 14
    https://api.openweathermap.org/data/2.5/weather?lat=74.33000000000224&lon=100.92999999999782&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 15
    https://api.openweathermap.org/data/2.5/weather?lat=-78.72000000000224&lon=-135.58999999999264&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 16
    https://api.openweathermap.org/data/2.5/weather?lat=70.47000000000163&lon=-27.910000000000046&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 17
    https://api.openweathermap.org/data/2.5/weather?lat=2.06&lon=156.01000000000306&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 18
    https://api.openweathermap.org/data/2.5/weather?lat=37.76999999999983&lon=-18.380000000000248&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 19
    https://api.openweathermap.org/data/2.5/weather?lat=-54.09999999999908&lon=72.63000000000197&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 20
    https://api.openweathermap.org/data/2.5/weather?lat=-52.25999999999879&lon=0.7000000000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 21
    https://api.openweathermap.org/data/2.5/weather?lat=11.42&lon=-143.01999999999643&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 22
    https://api.openweathermap.org/data/2.5/weather?lat=-21.240000000000187&lon=90.53999999999989&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 23
    https://api.openweathermap.org/data/2.5/weather?lat=-77.58000000000247&lon=-107.66000000000246&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 24
    https://api.openweathermap.org/data/2.5/weather?lat=65.91000000000092&lon=-136.6699999999932&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 25
    https://api.openweathermap.org/data/2.5/weather?lat=-19.72000000000022&lon=155.54000000000283&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 26
    https://api.openweathermap.org/data/2.5/weather?lat=-49.69999999999839&lon=11.8&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 27
    https://api.openweathermap.org/data/2.5/weather?lat=1.27&lon=122.0699999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 28
    https://api.openweathermap.org/data/2.5/weather?lat=10.93&lon=-106.49000000000268&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 29
    https://api.openweathermap.org/data/2.5/weather?lat=42.539999999999736&lon=-138.0699999999939&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 30
    https://api.openweathermap.org/data/2.5/weather?lat=-84.31000000000114&lon=151.950000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 31
    https://api.openweathermap.org/data/2.5/weather?lat=-46.36999999999787&lon=-141.25999999999553&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 32
    https://api.openweathermap.org/data/2.5/weather?lat=2.22&lon=-24.37000000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 33
    https://api.openweathermap.org/data/2.5/weather?lat=40.229999999999784&lon=-78.28000000000233&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 34
    https://api.openweathermap.org/data/2.5/weather?lat=-59.139999999999866&lon=101.0499999999978&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 35
    https://api.openweathermap.org/data/2.5/weather?lat=14.16&lon=93.93999999999922&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 36
    https://api.openweathermap.org/data/2.5/weather?lat=-76.58000000000267&lon=-143.7599999999968&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 37
    https://api.openweathermap.org/data/2.5/weather?lat=-83.39000000000132&lon=4.25&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 38
    https://api.openweathermap.org/data/2.5/weather?lat=34.7499999999999&lon=100.96999999999781&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 39
    https://api.openweathermap.org/data/2.5/weather?lat=10.33&lon=-81.84000000000162&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 40
    https://api.openweathermap.org/data/2.5/weather?lat=31.24999999999997&lon=100.82999999999785&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 41
    https://api.openweathermap.org/data/2.5/weather?lat=-47.679999999998074&lon=-149.1899999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 42
    https://api.openweathermap.org/data/2.5/weather?lat=-53.20999999999894&lon=-117.73000000000044&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 43
    https://api.openweathermap.org/data/2.5/weather?lat=-82.17000000000155&lon=-118.35000000000034&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 44
    https://api.openweathermap.org/data/2.5/weather?lat=-84.86000000000102&lon=151.52000000000078&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 45
    https://api.openweathermap.org/data/2.5/weather?lat=39.5099999999998&lon=33.50999999999993&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 46
    https://api.openweathermap.org/data/2.5/weather?lat=-85.83000000000084&lon=125.71999999999886&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 47
    https://api.openweathermap.org/data/2.5/weather?lat=-45.21999999999769&lon=121.5199999999997&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 48
    https://api.openweathermap.org/data/2.5/weather?lat=-81.18000000000175&lon=-57.14999999999955&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 49
    https://api.openweathermap.org/data/2.5/weather?lat=37.14999999999985&lon=33.08999999999993&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 50
    https://api.openweathermap.org/data/2.5/weather?lat=3.38&lon=-178.06999999999903&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 51
    https://api.openweathermap.org/data/2.5/weather?lat=31.36999999999997&lon=-51.48999999999867&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 52
    https://api.openweathermap.org/data/2.5/weather?lat=-76.4000000000027&lon=-114.77000000000103&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 53
    https://api.openweathermap.org/data/2.5/weather?lat=10.870000000000001&lon=158.84000000000452&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 54
    https://api.openweathermap.org/data/2.5/weather?lat=10.42&lon=6.91&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 55
    https://api.openweathermap.org/data/2.5/weather?lat=63.15000000000049&lon=14.36&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 56
    https://api.openweathermap.org/data/2.5/weather?lat=63.78000000000059&lon=104.20999999999717&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 57
    https://api.openweathermap.org/data/2.5/weather?lat=39.0599999999998&lon=91.29999999999974&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 58
    https://api.openweathermap.org/data/2.5/weather?lat=-16.130000000000294&lon=4.37&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 59
    https://api.openweathermap.org/data/2.5/weather?lat=-84.89000000000101&lon=-84.23000000000116&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 60
    https://api.openweathermap.org/data/2.5/weather?lat=-84.34000000000113&lon=-29.640000000000008&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 61
    https://api.openweathermap.org/data/2.5/weather?lat=34.809999999999896&lon=-28.49000000000003&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 62
    https://api.openweathermap.org/data/2.5/weather?lat=-18.900000000000237&lon=127.85999999999844&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 63
    https://api.openweathermap.org/data/2.5/weather?lat=-53.179999999998934&lon=14.36&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 64
    https://api.openweathermap.org/data/2.5/weather?lat=35.65999999999988&lon=-113.9800000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 65
    https://api.openweathermap.org/data/2.5/weather?lat=-26.74000000000007&lon=-23.48000000000014&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 66
    https://api.openweathermap.org/data/2.5/weather?lat=63.46000000000054&lon=41.369999999999756&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 67
    https://api.openweathermap.org/data/2.5/weather?lat=-59.65999999999995&lon=-28.290000000000035&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 68
    https://api.openweathermap.org/data/2.5/weather?lat=10.49&lon=159.87000000000506&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 69
    https://api.openweathermap.org/data/2.5/weather?lat=39.569999999999794&lon=-178.7899999999994&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 70
    https://api.openweathermap.org/data/2.5/weather?lat=-88.24000000000035&lon=-112.49000000000149&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 71
    https://api.openweathermap.org/data/2.5/weather?lat=-48.86999999999826&lon=-105.76000000000283&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 72
    https://api.openweathermap.org/data/2.5/weather?lat=-17.530000000000264&lon=33.659999999999926&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 73
    https://api.openweathermap.org/data/2.5/weather?lat=35.81999999999988&lon=-175.1299999999975&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 74
    https://api.openweathermap.org/data/2.5/weather?lat=5.14&lon=-81.66000000000166&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 75
    https://api.openweathermap.org/data/2.5/weather?lat=-51.049999999998604&lon=150.2100000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 76
    https://api.openweathermap.org/data/2.5/weather?lat=2.87&lon=-137.16999999999342&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 77
    https://api.openweathermap.org/data/2.5/weather?lat=-87.13000000000056&lon=-26.590000000000074&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 78
    https://api.openweathermap.org/data/2.5/weather?lat=-48.07999999999814&lon=-58.0799999999997&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 79
    https://api.openweathermap.org/data/2.5/weather?lat=-88.78000000000024&lon=92.11999999999958&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 80
    https://api.openweathermap.org/data/2.5/weather?lat=-84.13000000000116&lon=-80.77000000000183&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 81
    https://api.openweathermap.org/data/2.5/weather?lat=69.50000000000148&lon=123.40999999999931&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 82
    https://api.openweathermap.org/data/2.5/weather?lat=-15.48000000000031&lon=154.69000000000239&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 83
    https://api.openweathermap.org/data/2.5/weather?lat=-84.38000000000112&lon=68.83000000000138&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 84
    https://api.openweathermap.org/data/2.5/weather?lat=44.0699999999997&lon=-146.8899999999984&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 85
    https://api.openweathermap.org/data/2.5/weather?lat=-81.53000000000168&lon=69.50000000000148&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 86
    https://api.openweathermap.org/data/2.5/weather?lat=-16.210000000000292&lon=-50.62999999999853&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 87
    https://api.openweathermap.org/data/2.5/weather?lat=37.08999999999985&lon=1.59&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 88
    https://api.openweathermap.org/data/2.5/weather?lat=4.41&lon=-22.48000000000016&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 89
    https://api.openweathermap.org/data/2.5/weather?lat=64.06000000000063&lon=-118.08000000000038&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 90
    https://api.openweathermap.org/data/2.5/weather?lat=-24.580000000000116&lon=-80.4700000000019&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 91
    https://api.openweathermap.org/data/2.5/weather?lat=-81.9600000000016&lon=-48.06999999999813&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 92
    https://api.openweathermap.org/data/2.5/weather?lat=-23.750000000000135&lon=-172.209999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 93
    https://api.openweathermap.org/data/2.5/weather?lat=1.4000000000000001&lon=9.16&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 94
    https://api.openweathermap.org/data/2.5/weather?lat=-45.04999999999767&lon=-76.23000000000275&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 95
    https://api.openweathermap.org/data/2.5/weather?lat=-85.04000000000099&lon=-58.069999999999695&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 96
    https://api.openweathermap.org/data/2.5/weather?lat=3.63&lon=-175.1099999999975&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 97
    https://api.openweathermap.org/data/2.5/weather?lat=5.76&lon=-144.139999999997&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 98
    https://api.openweathermap.org/data/2.5/weather?lat=34.11999999999991&lon=2.9&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 99
    https://api.openweathermap.org/data/2.5/weather?lat=10.93&lon=-26.35000000000008&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 100
    https://api.openweathermap.org/data/2.5/weather?lat=61.16000000000018&lon=164.34000000000734&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 101
    https://api.openweathermap.org/data/2.5/weather?lat=2.54&lon=-146.7299999999983&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 102
    https://api.openweathermap.org/data/2.5/weather?lat=9.0&lon=159.64000000000493&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 103
    https://api.openweathermap.org/data/2.5/weather?lat=-24.100000000000126&lon=-18.230000000000253&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 104
    https://api.openweathermap.org/data/2.5/weather?lat=73.12000000000205&lon=-116.4600000000007&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 105
    https://api.openweathermap.org/data/2.5/weather?lat=-15.350000000000312&lon=-105.66000000000285&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 106
    https://api.openweathermap.org/data/2.5/weather?lat=1.09&lon=-83.53000000000128&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 107
    https://api.openweathermap.org/data/2.5/weather?lat=64.35000000000068&lon=-86.29000000000073&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 108
    https://api.openweathermap.org/data/2.5/weather?lat=-59.59999999999994&lon=160.3100000000053&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 109
    https://api.openweathermap.org/data/2.5/weather?lat=-78.72000000000224&lon=7.54&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 110
    https://api.openweathermap.org/data/2.5/weather?lat=0.22&lon=-118.19000000000037&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 111
    https://api.openweathermap.org/data/2.5/weather?lat=-54.81999999999919&lon=-47.57999999999806&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 112
    https://api.openweathermap.org/data/2.5/weather?lat=-89.04000000000019&lon=124.63999999999908&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 113
    https://api.openweathermap.org/data/2.5/weather?lat=-79.08000000000217&lon=-84.5000000000011&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 114
    https://api.openweathermap.org/data/2.5/weather?lat=-17.610000000000262&lon=-145.01999999999745&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 115
    https://api.openweathermap.org/data/2.5/weather?lat=-24.30000000000012&lon=-176.2599999999981&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 116
    https://api.openweathermap.org/data/2.5/weather?lat=64.82000000000076&lon=97.97999999999841&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 117
    https://api.openweathermap.org/data/2.5/weather?lat=4.73&lon=68.79000000000137&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 118
    https://api.openweathermap.org/data/2.5/weather?lat=-22.56000000000016&lon=13.24&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 119
    https://api.openweathermap.org/data/2.5/weather?lat=-77.71000000000245&lon=-108.70000000000225&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 120
    https://api.openweathermap.org/data/2.5/weather?lat=-49.769999999998404&lon=-49.63999999999838&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 121
    https://api.openweathermap.org/data/2.5/weather?lat=37.81999999999984&lon=-82.95000000000141&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 122
    https://api.openweathermap.org/data/2.5/weather?lat=-21.93000000000017&lon=-168.49999999999412&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 123
    https://api.openweathermap.org/data/2.5/weather?lat=-86.73000000000064&lon=-116.90000000000062&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 124
    https://api.openweathermap.org/data/2.5/weather?lat=30.869999999999983&lon=63.630000000000564&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 125
    https://api.openweathermap.org/data/2.5/weather?lat=6.5600000000000005&lon=30.319999999999993&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 126
    https://api.openweathermap.org/data/2.5/weather?lat=-22.690000000000154&lon=-111.25000000000173&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 127
    https://api.openweathermap.org/data/2.5/weather?lat=-83.9800000000012&lon=2.15&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 128
    https://api.openweathermap.org/data/2.5/weather?lat=-56.54999999999946&lon=162.09000000000617&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 129
    https://api.openweathermap.org/data/2.5/weather?lat=11.27&lon=-57.749999999999645&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 130
    https://api.openweathermap.org/data/2.5/weather?lat=-27.330000000000055&lon=97.88999999999842&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 131
    https://api.openweathermap.org/data/2.5/weather?lat=43.139999999999716&lon=-107.28000000000253&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 132
    https://api.openweathermap.org/data/2.5/weather?lat=-45.03999999999766&lon=100.079999999998&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 133
    https://api.openweathermap.org/data/2.5/weather?lat=70.16000000000159&lon=-82.15000000000157&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 134
    https://api.openweathermap.org/data/2.5/weather?lat=-45.8899999999978&lon=-179.88999999999993&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 135
    https://api.openweathermap.org/data/2.5/weather?lat=-50.57999999999853&lon=2.35&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 136
    https://api.openweathermap.org/data/2.5/weather?lat=73.09000000000205&lon=10.31&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 137
    https://api.openweathermap.org/data/2.5/weather?lat=33.45999999999992&lon=-175.36999999999762&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 138
    https://api.openweathermap.org/data/2.5/weather?lat=-76.82000000000262&lon=64.00000000000063&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 139
    https://api.openweathermap.org/data/2.5/weather?lat=-83.38000000000132&lon=-174.64999999999725&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 140
    https://api.openweathermap.org/data/2.5/weather?lat=-84.36000000000112&lon=9.8&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 141
    https://api.openweathermap.org/data/2.5/weather?lat=-76.52000000000268&lon=34.069999999999915&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 142
    https://api.openweathermap.org/data/2.5/weather?lat=72.790000000002&lon=38.13999999999983&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 143
    https://api.openweathermap.org/data/2.5/weather?lat=9.72&lon=132.60999999999748&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 144
    https://api.openweathermap.org/data/2.5/weather?lat=5.22&lon=40.49999999999977&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 145
    https://api.openweathermap.org/data/2.5/weather?lat=-18.34000000000025&lon=-45.959999999997805&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 146
    https://api.openweathermap.org/data/2.5/weather?lat=41.04999999999976&lon=67.6900000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 147
    https://api.openweathermap.org/data/2.5/weather?lat=-16.890000000000278&lon=-172.7699999999963&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 148
    https://api.openweathermap.org/data/2.5/weather?lat=36.769999999999854&lon=153.05000000000155&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 149
    https://api.openweathermap.org/data/2.5/weather?lat=36.539999999999864&lon=-137.20999999999344&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 150
    https://api.openweathermap.org/data/2.5/weather?lat=34.949999999999896&lon=151.2000000000006&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 151
    https://api.openweathermap.org/data/2.5/weather?lat=-18.460000000000246&lon=8.94&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 152
    https://api.openweathermap.org/data/2.5/weather?lat=32.06999999999996&lon=-86.06000000000078&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 153
    https://api.openweathermap.org/data/2.5/weather?lat=13.67&lon=-136.84999999999326&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 154
    https://api.openweathermap.org/data/2.5/weather?lat=-59.62999999999994&lon=35.29999999999988&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 155
    https://api.openweathermap.org/data/2.5/weather?lat=68.69000000000136&lon=42.12999999999974&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 156
    https://api.openweathermap.org/data/2.5/weather?lat=-15.160000000000316&lon=-49.17999999999831&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 157
    https://api.openweathermap.org/data/2.5/weather?lat=68.58000000000135&lon=68.9200000000014&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 158
    https://api.openweathermap.org/data/2.5/weather?lat=-80.55000000000189&lon=-88.53000000000029&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 159
    https://api.openweathermap.org/data/2.5/weather?lat=-26.630000000000074&lon=-147.2399999999986&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 160
    https://api.openweathermap.org/data/2.5/weather?lat=32.709999999999944&lon=162.5000000000064&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 161
    https://api.openweathermap.org/data/2.5/weather?lat=13.0&lon=61.13000000000018&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 162
    https://api.openweathermap.org/data/2.5/weather?lat=-58.57999999999978&lon=-142.43999999999613&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 163
    https://api.openweathermap.org/data/2.5/weather?lat=63.76000000000059&lon=134.90999999999704&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 164
    https://api.openweathermap.org/data/2.5/weather?lat=-49.61999999999838&lon=-27.900000000000045&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 165
    https://api.openweathermap.org/data/2.5/weather?lat=-77.10000000000257&lon=10.64&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 166
    https://api.openweathermap.org/data/2.5/weather?lat=-20.18000000000021&lon=154.3300000000022&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 167
    https://api.openweathermap.org/data/2.5/weather?lat=-46.34999999999786&lon=-141.68999999999573&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 168
    https://api.openweathermap.org/data/2.5/weather?lat=-20.5300000000002&lon=154.94000000000253&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 169
    https://api.openweathermap.org/data/2.5/weather?lat=35.099999999999895&lon=-48.40999999999819&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 170
    https://api.openweathermap.org/data/2.5/weather?lat=-19.66000000000022&lon=-21.660000000000178&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 171
    https://api.openweathermap.org/data/2.5/weather?lat=-58.1099999999997&lon=98.62999999999829&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 172
    https://api.openweathermap.org/data/2.5/weather?lat=73.4000000000021&lon=-118.26000000000035&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 173
    https://api.openweathermap.org/data/2.5/weather?lat=41.82999999999975&lon=-114.34000000000113&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 174
    https://api.openweathermap.org/data/2.5/weather?lat=11.35&lon=-166.7199999999932&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 175
    https://api.openweathermap.org/data/2.5/weather?lat=10.69&lon=-48.88999999999827&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 176
    https://api.openweathermap.org/data/2.5/weather?lat=73.94000000000219&lon=12.040000000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 177
    https://api.openweathermap.org/data/2.5/weather?lat=-46.82999999999794&lon=39.569999999999794&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 178
    https://api.openweathermap.org/data/2.5/weather?lat=6.98&lon=-78.61000000000226&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 179
    https://api.openweathermap.org/data/2.5/weather?lat=65.0800000000008&lon=-46.64999999999792&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 180
    https://api.openweathermap.org/data/2.5/weather?lat=43.949999999999704&lon=96.67999999999867&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 181
    https://api.openweathermap.org/data/2.5/weather?lat=-56.579999999999465&lon=-46.13999999999783&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 182
    https://api.openweathermap.org/data/2.5/weather?lat=-59.64999999999995&lon=35.38999999999989&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 183
    https://api.openweathermap.org/data/2.5/weather?lat=-26.950000000000067&lon=-76.4000000000027&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 184
    https://api.openweathermap.org/data/2.5/weather?lat=-77.57000000000247&lon=90.03999999999999&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 185
    https://api.openweathermap.org/data/2.5/weather?lat=-80.64000000000186&lon=95.08999999999898&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 186
    https://api.openweathermap.org/data/2.5/weather?lat=-59.82999999999997&lon=162.58000000000644&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 187
    https://api.openweathermap.org/data/2.5/weather?lat=-24.750000000000114&lon=164.47000000000742&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 188
    https://api.openweathermap.org/data/2.5/weather?lat=-23.930000000000128&lon=-51.08999999999861&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 189
    https://api.openweathermap.org/data/2.5/weather?lat=-50.119999999998456&lon=-149.7999999999999&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 190
    https://api.openweathermap.org/data/2.5/weather?lat=-82.91000000000142&lon=162.84000000000657&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 191
    https://api.openweathermap.org/data/2.5/weather?lat=44.58999999999969&lon=-21.98000000000017&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 192
    https://api.openweathermap.org/data/2.5/weather?lat=44.48999999999969&lon=-21.900000000000173&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 193
    https://api.openweathermap.org/data/2.5/weather?lat=-23.540000000000138&lon=-85.56000000000088&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 194
    https://api.openweathermap.org/data/2.5/weather?lat=-22.88000000000015&lon=-146.8599999999984&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 195
    https://api.openweathermap.org/data/2.5/weather?lat=-78.27000000000234&lon=68.58000000000135&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 196
    https://api.openweathermap.org/data/2.5/weather?lat=-84.90000000000101&lon=-109.4700000000021&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 197
    https://api.openweathermap.org/data/2.5/weather?lat=-76.39000000000271&lon=-84.37000000000111&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 198
    https://api.openweathermap.org/data/2.5/weather?lat=31.84999999999996&lon=74.80000000000231&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 199
    https://api.openweathermap.org/data/2.5/weather?lat=-58.12999999999971&lon=-87.58000000000048&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 200
    https://api.openweathermap.org/data/2.5/weather?lat=0.58&lon=-172.7699999999963&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 201
    https://api.openweathermap.org/data/2.5/weather?lat=40.60999999999977&lon=40.65999999999977&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 202
    https://api.openweathermap.org/data/2.5/weather?lat=30.02&lon=-58.039999999999694&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 203
    https://api.openweathermap.org/data/2.5/weather?lat=-79.54000000000208&lon=-168.34999999999405&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 204
    https://api.openweathermap.org/data/2.5/weather?lat=62.45000000000039&lon=73.95000000000218&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 205
    https://api.openweathermap.org/data/2.5/weather?lat=-57.719999999999644&lon=61.8900000000003&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 206
    https://api.openweathermap.org/data/2.5/weather?lat=40.819999999999766&lon=64.93000000000077&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 207
    https://api.openweathermap.org/data/2.5/weather?lat=-78.38000000000231&lon=-26.32000000000008&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 208
    https://api.openweathermap.org/data/2.5/weather?lat=7.91&lon=-113.0300000000014&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 209
    https://api.openweathermap.org/data/2.5/weather?lat=2.67&lon=-18.540000000000244&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 210
    https://api.openweathermap.org/data/2.5/weather?lat=-83.23000000000135&lon=65.65000000000089&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 211
    https://api.openweathermap.org/data/2.5/weather?lat=-18.180000000000252&lon=-168.76999999999424&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 212
    https://api.openweathermap.org/data/2.5/weather?lat=61.150000000000176&lon=-85.74000000000085&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 213
    https://api.openweathermap.org/data/2.5/weather?lat=-83.9400000000012&lon=163.9300000000071&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 214
    https://api.openweathermap.org/data/2.5/weather?lat=-23.710000000000136&lon=-79.19000000000216&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 215
    https://api.openweathermap.org/data/2.5/weather?lat=60.190000000000026&lon=-51.94999999999874&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 216
    https://api.openweathermap.org/data/2.5/weather?lat=-24.92000000000011&lon=37.80999999999983&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 217
    https://api.openweathermap.org/data/2.5/weather?lat=-79.990000000002&lon=-141.3999999999956&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 218
    https://api.openweathermap.org/data/2.5/weather?lat=32.86999999999994&lon=-169.59999999999468&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 219
    https://api.openweathermap.org/data/2.5/weather?lat=33.35999999999993&lon=5.6000000000000005&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 220
    https://api.openweathermap.org/data/2.5/weather?lat=2.81&lon=67.6200000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 221
    https://api.openweathermap.org/data/2.5/weather?lat=-84.39000000000112&lon=-46.5899999999979&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 222
    https://api.openweathermap.org/data/2.5/weather?lat=30.11&lon=122.42999999999952&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 223
    https://api.openweathermap.org/data/2.5/weather?lat=-58.7499999999998&lon=-20.12000000000021&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 224
    https://api.openweathermap.org/data/2.5/weather?lat=-48.989999999998275&lon=-105.27000000000294&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 225
    https://api.openweathermap.org/data/2.5/weather?lat=32.54999999999995&lon=-20.250000000000206&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 226
    https://api.openweathermap.org/data/2.5/weather?lat=-27.57000000000005&lon=133.20999999999736&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 227
    https://api.openweathermap.org/data/2.5/weather?lat=-56.839999999999506&lon=14.57&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 228
    https://api.openweathermap.org/data/2.5/weather?lat=8.83&lon=-75.05000000000297&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 229
    https://api.openweathermap.org/data/2.5/weather?lat=-29.85&lon=125.76999999999884&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 230
    https://api.openweathermap.org/data/2.5/weather?lat=60.24000000000004&lon=128.63999999999828&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 231
    https://api.openweathermap.org/data/2.5/weather?lat=-17.200000000000273&lon=32.82999999999994&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 232
    https://api.openweathermap.org/data/2.5/weather?lat=-29.330000000000013&lon=60.78000000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 233
    https://api.openweathermap.org/data/2.5/weather?lat=-54.979999999999215&lon=-169.59999999999468&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 234
    https://api.openweathermap.org/data/2.5/weather?lat=-46.89999999999795&lon=-166.24999999999295&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 235
    https://api.openweathermap.org/data/2.5/weather?lat=-59.139999999999866&lon=43.55999999999971&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 236
    https://api.openweathermap.org/data/2.5/weather?lat=-81.58000000000168&lon=30.179999999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 237
    https://api.openweathermap.org/data/2.5/weather?lat=4.96&lon=125.59999999999889&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 238
    https://api.openweathermap.org/data/2.5/weather?lat=-79.77000000000203&lon=-17.87000000000026&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 239
    https://api.openweathermap.org/data/2.5/weather?lat=-17.76000000000026&lon=69.29000000000146&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 240
    https://api.openweathermap.org/data/2.5/weather?lat=69.15000000000143&lon=154.0700000000021&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 241
    https://api.openweathermap.org/data/2.5/weather?lat=44.099999999999696&lon=40.68999999999977&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 242
    https://api.openweathermap.org/data/2.5/weather?lat=72.63000000000197&lon=-179.08999999999952&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 243
    https://api.openweathermap.org/data/2.5/weather?lat=-53.579999999998996&lon=-55.16999999999925&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 244
    https://api.openweathermap.org/data/2.5/weather?lat=33.05999999999993&lon=-83.73000000000124&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 245
    https://api.openweathermap.org/data/2.5/weather?lat=44.0199999999997&lon=-28.920000000000023&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 246
    https://api.openweathermap.org/data/2.5/weather?lat=-28.650000000000027&lon=-17.480000000000267&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 247
    https://api.openweathermap.org/data/2.5/weather?lat=64.5400000000007&lon=90.41999999999992&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 248
    https://api.openweathermap.org/data/2.5/weather?lat=71.35000000000177&lon=3.02&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 249
    https://api.openweathermap.org/data/2.5/weather?lat=-47.42999999999803&lon=-106.06000000000277&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 250
    https://api.openweathermap.org/data/2.5/weather?lat=-15.630000000000306&lon=10.43&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 251
    https://api.openweathermap.org/data/2.5/weather?lat=1.02&lon=126.61999999999868&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 252
    https://api.openweathermap.org/data/2.5/weather?lat=-16.82000000000028&lon=157.20000000000368&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 253
    https://api.openweathermap.org/data/2.5/weather?lat=-84.10000000000117&lon=131.73999999999768&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 254
    https://api.openweathermap.org/data/2.5/weather?lat=-47.32999999999802&lon=-118.64000000000027&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 255
    https://api.openweathermap.org/data/2.5/weather?lat=-20.6100000000002&lon=-147.18999999999858&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 256
    https://api.openweathermap.org/data/2.5/weather?lat=7.18&lon=-178.059999999999&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 257
    https://api.openweathermap.org/data/2.5/weather?lat=71.70000000000184&lon=5.3&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 258
    https://api.openweathermap.org/data/2.5/weather?lat=-15.49000000000031&lon=36.52999999999986&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 259
    https://api.openweathermap.org/data/2.5/weather?lat=74.60000000000228&lon=-135.80999999999273&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 260
    https://api.openweathermap.org/data/2.5/weather?lat=-50.819999999998565&lon=-89.61000000000007&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 261
    https://api.openweathermap.org/data/2.5/weather?lat=6.34&lon=90.76999999999984&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 262
    https://api.openweathermap.org/data/2.5/weather?lat=-20.360000000000205&lon=-136.8699999999933&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 263
    https://api.openweathermap.org/data/2.5/weather?lat=-59.81999999999997&lon=134.5999999999971&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 264
    https://api.openweathermap.org/data/2.5/weather?lat=-22.89000000000015&lon=-54.81999999999919&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 265
    https://api.openweathermap.org/data/2.5/weather?lat=69.43000000000147&lon=133.5599999999973&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 266
    https://api.openweathermap.org/data/2.5/weather?lat=3.67&lon=2.37&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 267
    https://api.openweathermap.org/data/2.5/weather?lat=38.379999999999825&lon=-19.900000000000215&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 268
    https://api.openweathermap.org/data/2.5/weather?lat=31.87999999999996&lon=-136.5099999999931&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 269
    https://api.openweathermap.org/data/2.5/weather?lat=66.29000000000099&lon=-57.69999999999964&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 270
    https://api.openweathermap.org/data/2.5/weather?lat=67.0700000000011&lon=68.82000000000139&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 271
    https://api.openweathermap.org/data/2.5/weather?lat=30.03&lon=163.15000000000674&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 272
    https://api.openweathermap.org/data/2.5/weather?lat=2.68&lon=-54.03999999999907&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 273
    https://api.openweathermap.org/data/2.5/weather?lat=41.409999999999755&lon=70.1800000000016&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 274
    https://api.openweathermap.org/data/2.5/weather?lat=33.009999999999934&lon=65.53000000000087&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 275
    https://api.openweathermap.org/data/2.5/weather?lat=-18.25000000000025&lon=67.12000000000111&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 276
    https://api.openweathermap.org/data/2.5/weather?lat=-54.8999999999992&lon=-177.29999999999862&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 277
    https://api.openweathermap.org/data/2.5/weather?lat=60.98000000000015&lon=-140.9999999999954&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 278
    https://api.openweathermap.org/data/2.5/weather?lat=1.94&lon=-20.6300000000002&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 279
    https://api.openweathermap.org/data/2.5/weather?lat=71.33000000000177&lon=-142.27999999999605&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 280
    https://api.openweathermap.org/data/2.5/weather?lat=71.91000000000186&lon=151.3600000000007&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 281
    https://api.openweathermap.org/data/2.5/weather?lat=-56.56999999999947&lon=130.29999999999797&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 282
    https://api.openweathermap.org/data/2.5/weather?lat=-55.10999999999923&lon=91.33999999999973&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 283
    https://api.openweathermap.org/data/2.5/weather?lat=-50.20999999999847&lon=94.59999999999908&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 284
    https://api.openweathermap.org/data/2.5/weather?lat=-84.01000000000118&lon=-138.74999999999426&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 285
    https://api.openweathermap.org/data/2.5/weather?lat=-26.430000000000078&lon=14.49&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 286
    https://api.openweathermap.org/data/2.5/weather?lat=32.35999999999995&lon=100.24999999999795&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 287
    https://api.openweathermap.org/data/2.5/weather?lat=10.91&lon=-107.91000000000241&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 288
    https://api.openweathermap.org/data/2.5/weather?lat=-84.90000000000101&lon=35.469999999999885&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 289
    https://api.openweathermap.org/data/2.5/weather?lat=32.629999999999946&lon=155.890000000003&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 290
    https://api.openweathermap.org/data/2.5/weather?lat=-28.280000000000037&lon=-17.240000000000272&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 291
    https://api.openweathermap.org/data/2.5/weather?lat=-47.71999999999808&lon=-169.0899999999944&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 292
    https://api.openweathermap.org/data/2.5/weather?lat=72.71000000000198&lon=-79.43000000000211&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 293
    https://api.openweathermap.org/data/2.5/weather?lat=42.71999999999973&lon=-167.25999999999348&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 294
    https://api.openweathermap.org/data/2.5/weather?lat=65.57000000000087&lon=74.79000000000231&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 295
    https://api.openweathermap.org/data/2.5/weather?lat=-80.23000000000195&lon=127.88999999999842&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 296
    https://api.openweathermap.org/data/2.5/weather?lat=-19.120000000000232&lon=64.4900000000007&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 297
    https://api.openweathermap.org/data/2.5/weather?lat=-23.48000000000014&lon=134.38999999999714&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 298
    https://api.openweathermap.org/data/2.5/weather?lat=-16.520000000000287&lon=-105.04000000000298&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 299
    https://api.openweathermap.org/data/2.5/weather?lat=-49.31999999999833&lon=65.61000000000088&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 300
    https://api.openweathermap.org/data/2.5/weather?lat=3.11&lon=31.659999999999965&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 301
    https://api.openweathermap.org/data/2.5/weather?lat=-55.95999999999937&lon=66.72000000000105&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 302
    https://api.openweathermap.org/data/2.5/weather?lat=-49.1399999999983&lon=-54.35999999999912&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 303
    https://api.openweathermap.org/data/2.5/weather?lat=-80.4100000000019&lon=103.78999999999726&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 304
    https://api.openweathermap.org/data/2.5/weather?lat=-58.85999999999982&lon=-172.9699999999964&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 305
    https://api.openweathermap.org/data/2.5/weather?lat=43.399999999999714&lon=-167.18999999999346&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 306
    https://api.openweathermap.org/data/2.5/weather?lat=-76.26000000000273&lon=32.899999999999935&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 307
    https://api.openweathermap.org/data/2.5/weather?lat=-81.87000000000162&lon=-142.85999999999635&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 308
    https://api.openweathermap.org/data/2.5/weather?lat=-19.010000000000232&lon=124.22999999999917&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 309
    https://api.openweathermap.org/data/2.5/weather?lat=67.27000000000113&lon=-172.18999999999602&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 310
    https://api.openweathermap.org/data/2.5/weather?lat=30.649999999999984&lon=73.27000000000207&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 311
    https://api.openweathermap.org/data/2.5/weather?lat=-26.990000000000066&lon=30.179999999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 312
    https://api.openweathermap.org/data/2.5/weather?lat=-49.1499999999983&lon=-75.4200000000029&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 313
    https://api.openweathermap.org/data/2.5/weather?lat=-89.0000000000002&lon=63.160000000000494&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 314
    https://api.openweathermap.org/data/2.5/weather?lat=61.120000000000175&lon=-119.08000000000018&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 315
    https://api.openweathermap.org/data/2.5/weather?lat=4.1&lon=-24.530000000000115&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 316
    https://api.openweathermap.org/data/2.5/weather?lat=-51.16999999999862&lon=-168.8899999999943&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 317
    https://api.openweathermap.org/data/2.5/weather?lat=70.2100000000016&lon=40.73999999999977&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 318
    https://api.openweathermap.org/data/2.5/weather?lat=9.65&lon=-46.5299999999979&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 319
    https://api.openweathermap.org/data/2.5/weather?lat=6.84&lon=41.16999999999976&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 320
    https://api.openweathermap.org/data/2.5/weather?lat=35.769999999999875&lon=30.129999999999995&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 321
    https://api.openweathermap.org/data/2.5/weather?lat=43.47999999999971&lon=-117.4600000000005&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 322
    https://api.openweathermap.org/data/2.5/weather?lat=-48.07999999999814&lon=127.57999999999849&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 323
    https://api.openweathermap.org/data/2.5/weather?lat=-88.34000000000033&lon=160.99000000000564&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 324
    https://api.openweathermap.org/data/2.5/weather?lat=13.77&lon=-166.59999999999314&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 325
    https://api.openweathermap.org/data/2.5/weather?lat=-81.15000000000177&lon=-25.120000000000104&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 326
    https://api.openweathermap.org/data/2.5/weather?lat=65.93000000000093&lon=-167.58999999999367&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 327
    https://api.openweathermap.org/data/2.5/weather?lat=-15.810000000000302&lon=-116.07000000000079&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 328
    https://api.openweathermap.org/data/2.5/weather?lat=13.790000000000001&lon=36.40999999999986&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 329
    https://api.openweathermap.org/data/2.5/weather?lat=33.53999999999992&lon=96.74999999999866&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 330
    https://api.openweathermap.org/data/2.5/weather?lat=-88.88000000000022&lon=-177.50999999999874&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 331
    https://api.openweathermap.org/data/2.5/weather?lat=-58.50999999999976&lon=-50.059999999998446&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 332
    https://api.openweathermap.org/data/2.5/weather?lat=2.22&lon=66.04000000000094&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 333
    https://api.openweathermap.org/data/2.5/weather?lat=4.91&lon=99.99999999999801&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 334
    https://api.openweathermap.org/data/2.5/weather?lat=-47.02999999999797&lon=6.61&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 335
    https://api.openweathermap.org/data/2.5/weather?lat=7.9&lon=-22.830000000000155&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 336
    https://api.openweathermap.org/data/2.5/weather?lat=-78.61000000000226&lon=122.62999999999948&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 337
    https://api.openweathermap.org/data/2.5/weather?lat=-89.5200000000001&lon=-165.09999999999238&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 338
    https://api.openweathermap.org/data/2.5/weather?lat=-17.39000000000027&lon=99.34999999999815&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 339
    https://api.openweathermap.org/data/2.5/weather?lat=-59.259999999999884&lon=-23.630000000000138&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 340
    https://api.openweathermap.org/data/2.5/weather?lat=61.610000000000255&lon=69.95000000000155&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 341
    https://api.openweathermap.org/data/2.5/weather?lat=-46.719999999997924&lon=-79.56000000000208&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 342
    https://api.openweathermap.org/data/2.5/weather?lat=-87.65000000000046&lon=-16.150000000000297&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 343
    https://api.openweathermap.org/data/2.5/weather?lat=13.290000000000001&lon=-54.43999999999913&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 344
    https://api.openweathermap.org/data/2.5/weather?lat=69.83000000000153&lon=9.03&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 345
    https://api.openweathermap.org/data/2.5/weather?lat=37.00999999999985&lon=-56.07999999999939&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 346
    https://api.openweathermap.org/data/2.5/weather?lat=41.299999999999756&lon=-144.08999999999696&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 347
    https://api.openweathermap.org/data/2.5/weather?lat=-54.78999999999918&lon=-172.7599999999963&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 348
    https://api.openweathermap.org/data/2.5/weather?lat=-79.65000000000205&lon=96.33999999999874&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 349
    https://api.openweathermap.org/data/2.5/weather?lat=-19.910000000000217&lon=-175.14999999999753&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 350
    https://api.openweathermap.org/data/2.5/weather?lat=3.02&lon=-171.08999999999543&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 351
    https://api.openweathermap.org/data/2.5/weather?lat=42.11999999999974&lon=39.14999999999981&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 352
    https://api.openweathermap.org/data/2.5/weather?lat=-20.000000000000213&lon=-114.980000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 353
    https://api.openweathermap.org/data/2.5/weather?lat=-83.70000000000125&lon=163.660000000007&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 354
    https://api.openweathermap.org/data/2.5/weather?lat=69.44000000000148&lon=-138.18999999999397&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 355
    https://api.openweathermap.org/data/2.5/weather?lat=8.01&lon=-115.4500000000009&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 356
    https://api.openweathermap.org/data/2.5/weather?lat=38.21999999999983&lon=-57.399999999999594&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 357
    https://api.openweathermap.org/data/2.5/weather?lat=39.4099999999998&lon=-169.19999999999447&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 358
    https://api.openweathermap.org/data/2.5/weather?lat=39.91999999999979&lon=130.4999999999979&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 359
    https://api.openweathermap.org/data/2.5/weather?lat=-83.91000000000122&lon=31.109999999999978&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 360
    https://api.openweathermap.org/data/2.5/weather?lat=-22.280000000000165&lon=120.21999999999996&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 361
    https://api.openweathermap.org/data/2.5/weather?lat=34.369999999999905&lon=128.34999999999835&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 362
    https://api.openweathermap.org/data/2.5/weather?lat=34.259999999999906&lon=-112.02000000000159&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 363
    https://api.openweathermap.org/data/2.5/weather?lat=-85.68000000000086&lon=0.54&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 364
    https://api.openweathermap.org/data/2.5/weather?lat=67.55000000000118&lon=90.24999999999994&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 365
    https://api.openweathermap.org/data/2.5/weather?lat=-27.97000000000004&lon=35.429999999999886&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 366
    https://api.openweathermap.org/data/2.5/weather?lat=38.499999999999815&lon=6.0200000000000005&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 367
    https://api.openweathermap.org/data/2.5/weather?lat=-21.48000000000018&lon=-81.61000000000166&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 368
    https://api.openweathermap.org/data/2.5/weather?lat=38.44999999999982&lon=11.55&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 369
    https://api.openweathermap.org/data/2.5/weather?lat=-86.4500000000007&lon=120.02&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 370
    https://api.openweathermap.org/data/2.5/weather?lat=36.49999999999986&lon=125.5399999999989&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 371
    https://api.openweathermap.org/data/2.5/weather?lat=44.3799999999997&lon=-84.67000000000107&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 372
    https://api.openweathermap.org/data/2.5/weather?lat=-53.66999999999901&lon=133.5199999999973&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 373
    https://api.openweathermap.org/data/2.5/weather?lat=-50.59999999999853&lon=-109.78000000000203&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 374
    https://api.openweathermap.org/data/2.5/weather?lat=63.55000000000055&lon=-87.39000000000053&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 375
    https://api.openweathermap.org/data/2.5/weather?lat=34.14999999999991&lon=162.29000000000627&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 376
    https://api.openweathermap.org/data/2.5/weather?lat=66.94000000000108&lon=43.16999999999972&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 377
    https://api.openweathermap.org/data/2.5/weather?lat=-22.81000000000015&lon=-178.90999999999946&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 378
    https://api.openweathermap.org/data/2.5/weather?lat=-83.52000000000129&lon=-138.4399999999941&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 379
    https://api.openweathermap.org/data/2.5/weather?lat=40.26999999999978&lon=-148.6199999999993&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 380
    https://api.openweathermap.org/data/2.5/weather?lat=-23.660000000000135&lon=6.92&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 381
    https://api.openweathermap.org/data/2.5/weather?lat=-45.489999999997735&lon=38.61999999999982&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 382
    https://api.openweathermap.org/data/2.5/weather?lat=-47.8699999999981&lon=-146.35999999999814&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 383
    https://api.openweathermap.org/data/2.5/weather?lat=35.07999999999989&lon=9.14&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 384
    https://api.openweathermap.org/data/2.5/weather?lat=-22.19000000000017&lon=160.3600000000053&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 385
    https://api.openweathermap.org/data/2.5/weather?lat=-59.49999999999992&lon=-116.39000000000073&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 386
    https://api.openweathermap.org/data/2.5/weather?lat=63.75000000000058&lon=-109.54000000000208&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 387
    https://api.openweathermap.org/data/2.5/weather?lat=-82.04000000000158&lon=44.659999999999684&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 388
    https://api.openweathermap.org/data/2.5/weather?lat=72.59000000000196&lon=-114.0300000000012&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 389
    https://api.openweathermap.org/data/2.5/weather?lat=71.5200000000018&lon=-45.68999999999777&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 390
    https://api.openweathermap.org/data/2.5/weather?lat=-57.479999999999606&lon=71.66000000000182&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 391
    https://api.openweathermap.org/data/2.5/weather?lat=1.48&lon=-146.30999999999813&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 392
    https://api.openweathermap.org/data/2.5/weather?lat=-18.480000000000246&lon=-46.63999999999791&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 393
    https://api.openweathermap.org/data/2.5/weather?lat=-76.4300000000027&lon=-110.88000000000181&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 394
    https://api.openweathermap.org/data/2.5/weather?lat=34.7799999999999&lon=126.09999999999879&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 395
    https://api.openweathermap.org/data/2.5/weather?lat=65.1600000000008&lon=99.84999999999803&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 396
    https://api.openweathermap.org/data/2.5/weather?lat=1.6400000000000001&lon=133.0799999999974&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 397
    https://api.openweathermap.org/data/2.5/weather?lat=69.80000000000153&lon=-147.17999999999856&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 398
    https://api.openweathermap.org/data/2.5/weather?lat=-45.72999999999777&lon=-88.63000000000028&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 399
    https://api.openweathermap.org/data/2.5/weather?lat=3.31&lon=71.33000000000177&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 400
    https://api.openweathermap.org/data/2.5/weather?lat=12.67&lon=-81.63000000000167&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 401
    https://api.openweathermap.org/data/2.5/weather?lat=63.8100000000006&lon=163.3000000000068&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 402
    https://api.openweathermap.org/data/2.5/weather?lat=34.89999999999989&lon=128.30999999999835&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 403
    https://api.openweathermap.org/data/2.5/weather?lat=-83.34000000000133&lon=-51.18999999999862&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 404
    https://api.openweathermap.org/data/2.5/weather?lat=9.540000000000001&lon=-135.74999999999272&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 405
    https://api.openweathermap.org/data/2.5/weather?lat=-88.31000000000034&lon=40.84999999999977&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 406
    https://api.openweathermap.org/data/2.5/weather?lat=-29.160000000000018&lon=37.37999999999984&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 407
    https://api.openweathermap.org/data/2.5/weather?lat=-46.77999999999793&lon=-112.4100000000015&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 408
    https://api.openweathermap.org/data/2.5/weather?lat=67.55000000000118&lon=-78.12000000000236&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 409
    https://api.openweathermap.org/data/2.5/weather?lat=36.769999999999854&lon=-53.609999999999005&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 410
    https://api.openweathermap.org/data/2.5/weather?lat=39.87999999999979&lon=-136.57999999999313&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 411
    https://api.openweathermap.org/data/2.5/weather?lat=14.44&lon=-45.18999999999768&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 412
    https://api.openweathermap.org/data/2.5/weather?lat=-84.01000000000118&lon=-24.260000000000122&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 413
    https://api.openweathermap.org/data/2.5/weather?lat=2.27&lon=126.5299999999987&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 414
    https://api.openweathermap.org/data/2.5/weather?lat=-47.089999999997985&lon=63.15000000000049&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 415
    https://api.openweathermap.org/data/2.5/weather?lat=74.25000000000223&lon=164.73000000000752&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 416
    https://api.openweathermap.org/data/2.5/weather?lat=44.84999999999968&lon=14.950000000000001&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 417
    https://api.openweathermap.org/data/2.5/weather?lat=-16.520000000000287&lon=41.28999999999976&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 418
    https://api.openweathermap.org/data/2.5/weather?lat=7.71&lon=39.4999999999998&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 419
    https://api.openweathermap.org/data/2.5/weather?lat=10.55&lon=-77.52000000000248&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 420
    https://api.openweathermap.org/data/2.5/weather?lat=63.35000000000052&lon=132.83999999999745&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 421
    https://api.openweathermap.org/data/2.5/weather?lat=-56.689999999999486&lon=151.26000000000064&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 422
    https://api.openweathermap.org/data/2.5/weather?lat=-19.20000000000023&lon=65.30000000000084&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 423
    https://api.openweathermap.org/data/2.5/weather?lat=74.51000000000226&lon=-173.48999999999666&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 424
    https://api.openweathermap.org/data/2.5/weather?lat=72.55000000000196&lon=-110.45000000000189&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 425
    https://api.openweathermap.org/data/2.5/weather?lat=-49.429999999998344&lon=-175.9299999999979&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 426
    https://api.openweathermap.org/data/2.5/weather?lat=3.99&lon=-149.29999999999964&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 427
    https://api.openweathermap.org/data/2.5/weather?lat=-79.53000000000208&lon=128.33999999999833&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 428
    https://api.openweathermap.org/data/2.5/weather?lat=44.059999999999704&lon=-118.54000000000029&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 429
    https://api.openweathermap.org/data/2.5/weather?lat=7.98&lon=-55.559999999999306&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 430
    https://api.openweathermap.org/data/2.5/weather?lat=66.95000000000108&lon=-179.6099999999998&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 431
    https://api.openweathermap.org/data/2.5/weather?lat=65.02000000000078&lon=60.280000000000044&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 432
    https://api.openweathermap.org/data/2.5/weather?lat=13.06&lon=-172.49999999999616&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 433
    https://api.openweathermap.org/data/2.5/weather?lat=73.71000000000214&lon=-105.4200000000029&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 434
    https://api.openweathermap.org/data/2.5/weather?lat=60.06000000000001&lon=-115.37000000000091&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 435
    https://api.openweathermap.org/data/2.5/weather?lat=4.4&lon=159.25000000000472&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 436
    https://api.openweathermap.org/data/2.5/weather?lat=-24.95000000000011&lon=11.65&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 437
    https://api.openweathermap.org/data/2.5/weather?lat=-76.65000000000265&lon=156.13000000000312&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 438
    https://api.openweathermap.org/data/2.5/weather?lat=-88.18000000000036&lon=38.979999999999805&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 439
    https://api.openweathermap.org/data/2.5/weather?lat=-56.58999999999946&lon=-137.30999999999352&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 440
    https://api.openweathermap.org/data/2.5/weather?lat=10.99&lon=73.26000000000207&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 441
    https://api.openweathermap.org/data/2.5/weather?lat=-46.99999999999797&lon=0.14&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 442
    https://api.openweathermap.org/data/2.5/weather?lat=-49.219999999998315&lon=-22.600000000000158&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 443
    https://api.openweathermap.org/data/2.5/weather?lat=12.75&lon=-82.59000000000148&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 444
    https://api.openweathermap.org/data/2.5/weather?lat=74.96000000000234&lon=13.42&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 445
    https://api.openweathermap.org/data/2.5/weather?lat=44.46999999999969&lon=-58.7499999999998&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 446
    https://api.openweathermap.org/data/2.5/weather?lat=-59.489999999999924&lon=-167.2999999999935&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 447
    https://api.openweathermap.org/data/2.5/weather?lat=-23.150000000000148&lon=126.15999999999877&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 448
    https://api.openweathermap.org/data/2.5/weather?lat=-56.7999999999995&lon=132.82999999999745&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 449
    https://api.openweathermap.org/data/2.5/weather?lat=14.49&lon=101.42999999999773&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 450
    https://api.openweathermap.org/data/2.5/weather?lat=-82.49000000000149&lon=65.25000000000082&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 451
    https://api.openweathermap.org/data/2.5/weather?lat=-88.62000000000027&lon=39.199999999999804&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 452
    https://api.openweathermap.org/data/2.5/weather?lat=4.24&lon=60.920000000000144&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 453
    https://api.openweathermap.org/data/2.5/weather?lat=68.9500000000014&lon=35.05999999999989&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 454
    https://api.openweathermap.org/data/2.5/weather?lat=-15.300000000000313&lon=-108.4400000000023&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 455
    https://api.openweathermap.org/data/2.5/weather?lat=-80.56000000000188&lon=-169.69999999999473&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 456
    https://api.openweathermap.org/data/2.5/weather?lat=1.1500000000000001&lon=-148.55999999999926&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 457
    https://api.openweathermap.org/data/2.5/weather?lat=65.86000000000092&lon=93.70999999999927&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 458
    https://api.openweathermap.org/data/2.5/weather?lat=-89.4900000000001&lon=103.69999999999727&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 459
    https://api.openweathermap.org/data/2.5/weather?lat=-78.57000000000227&lon=-45.96999999999781&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 460
    https://api.openweathermap.org/data/2.5/weather?lat=40.60999999999977&lon=-106.00000000000279&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 461
    https://api.openweathermap.org/data/2.5/weather?lat=-26.110000000000085&lon=164.12000000000722&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 462
    https://api.openweathermap.org/data/2.5/weather?lat=13.75&lon=-22.46000000000016&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 463
    https://api.openweathermap.org/data/2.5/weather?lat=-59.3799999999999&lon=160.1400000000052&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 464
    https://api.openweathermap.org/data/2.5/weather?lat=-46.20999999999785&lon=39.599999999999795&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 465
    https://api.openweathermap.org/data/2.5/weather?lat=-46.40999999999788&lon=-143.08999999999645&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 466
    https://api.openweathermap.org/data/2.5/weather?lat=-16.120000000000296&lon=-171.82999999999583&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 467
    https://api.openweathermap.org/data/2.5/weather?lat=-86.72000000000065&lon=6.03&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 468
    https://api.openweathermap.org/data/2.5/weather?lat=-29.700000000000006&lon=-24.720000000000113&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 469
    https://api.openweathermap.org/data/2.5/weather?lat=-15.200000000000315&lon=40.219999999999786&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 470
    https://api.openweathermap.org/data/2.5/weather?lat=13.59&lon=-114.04000000000119&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 471
    https://api.openweathermap.org/data/2.5/weather?lat=-16.390000000000292&lon=92.55999999999949&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 472
    https://api.openweathermap.org/data/2.5/weather?lat=-17.000000000000277&lon=-55.85999999999935&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 473
    https://api.openweathermap.org/data/2.5/weather?lat=-17.73000000000026&lon=73.4700000000021&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 474
    https://api.openweathermap.org/data/2.5/weather?lat=-81.92000000000161&lon=-24.310000000000123&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 475
    https://api.openweathermap.org/data/2.5/weather?lat=-48.459999999998196&lon=8.43&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 476
    https://api.openweathermap.org/data/2.5/weather?lat=66.72000000000105&lon=11.14&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 477
    https://api.openweathermap.org/data/2.5/weather?lat=2.3000000000000003&lon=4.05&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 478
    https://api.openweathermap.org/data/2.5/weather?lat=-56.49999999999945&lon=131.78999999999766&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 479
    https://api.openweathermap.org/data/2.5/weather?lat=41.849999999999746&lon=123.91999999999922&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 480
    https://api.openweathermap.org/data/2.5/weather?lat=1.76&lon=162.8700000000066&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 481
    https://api.openweathermap.org/data/2.5/weather?lat=-21.640000000000178&lon=101.73999999999766&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 482
    https://api.openweathermap.org/data/2.5/weather?lat=-76.20000000000275&lon=-146.2599999999981&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 483
    https://api.openweathermap.org/data/2.5/weather?lat=-22.870000000000154&lon=121.87999999999963&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 484
    https://api.openweathermap.org/data/2.5/weather?lat=-47.71999999999808&lon=98.76999999999825&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 485
    https://api.openweathermap.org/data/2.5/weather?lat=71.57000000000181&lon=95.5899999999989&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 486
    https://api.openweathermap.org/data/2.5/weather?lat=66.20000000000097&lon=-137.98999999999387&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 487
    https://api.openweathermap.org/data/2.5/weather?lat=-47.14999999999799&lon=-51.78999999999871&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 488
    https://api.openweathermap.org/data/2.5/weather?lat=63.480000000000544&lon=96.98999999999862&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 489
    https://api.openweathermap.org/data/2.5/weather?lat=14.31&lon=38.13999999999983&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 490
    https://api.openweathermap.org/data/2.5/weather?lat=63.25000000000051&lon=-26.050000000000082&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 491
    https://api.openweathermap.org/data/2.5/weather?lat=38.53999999999982&lon=125.78999999999886&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 492
    https://api.openweathermap.org/data/2.5/weather?lat=-46.16999999999784&lon=-28.240000000000038&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 493
    https://api.openweathermap.org/data/2.5/weather?lat=-49.219999999998315&lon=62.76000000000043&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 494
    https://api.openweathermap.org/data/2.5/weather?lat=43.05999999999972&lon=-140.94999999999538&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 495
    https://api.openweathermap.org/data/2.5/weather?lat=3.24&lon=129.71999999999807&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 496
    https://api.openweathermap.org/data/2.5/weather?lat=-24.630000000000116&lon=162.8800000000066&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 497
    https://api.openweathermap.org/data/2.5/weather?lat=63.44000000000054&lon=-55.24999999999926&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 498
    https://api.openweathermap.org/data/2.5/weather?lat=-23.36000000000014&lon=-49.82999999999841&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    Now retrieving city # 499
    https://api.openweathermap.org/data/2.5/weather?lat=-20.430000000000206&lon=-28.590000000000032&appid=f1d6a2a44804f4e2b3b88ae1b2b2cae6&units=Imperial
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Closest City Name</th>
      <th>Closest Country Code</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>72.58</td>
      <td>-22.57</td>
      <td>illoqqortoormiut</td>
      <td>gl</td>
      <td>31.03</td>
      <td>89</td>
      <td>0</td>
      <td>5.73</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-88.77</td>
      <td>-172.33</td>
      <td>vaini</td>
      <td>to</td>
      <td>-59.33</td>
      <td>0</td>
      <td>24</td>
      <td>13.44</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8.85</td>
      <td>-22.36</td>
      <td>praia</td>
      <td>cv</td>
      <td>79.18</td>
      <td>100</td>
      <td>80</td>
      <td>5.06</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-20.30</td>
      <td>103.93</td>
      <td>carnarvon</td>
      <td>au</td>
      <td>73.24</td>
      <td>100</td>
      <td>0</td>
      <td>1.48</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.62</td>
      <td>7.79</td>
      <td>port-gentil</td>
      <td>ga</td>
      <td>75.22</td>
      <td>100</td>
      <td>76</td>
      <td>14.79</td>
    </tr>
    <tr>
      <th>5</th>
      <td>-76.05</td>
      <td>95.57</td>
      <td>albany</td>
      <td>au</td>
      <td>-56.36</td>
      <td>0</td>
      <td>12</td>
      <td>14.56</td>
    </tr>
    <tr>
      <th>6</th>
      <td>67.01</td>
      <td>122.90</td>
      <td>zhigansk</td>
      <td>ru</td>
      <td>42.28</td>
      <td>85</td>
      <td>76</td>
      <td>10.65</td>
    </tr>
    <tr>
      <th>7</th>
      <td>-23.04</td>
      <td>69.48</td>
      <td>grand river south east</td>
      <td>mu</td>
      <td>69.55</td>
      <td>100</td>
      <td>88</td>
      <td>23.73</td>
    </tr>
    <tr>
      <th>8</th>
      <td>13.73</td>
      <td>-78.40</td>
      <td>san andres</td>
      <td>co</td>
      <td>82.87</td>
      <td>95</td>
      <td>24</td>
      <td>22.62</td>
    </tr>
    <tr>
      <th>9</th>
      <td>66.77</td>
      <td>63.53</td>
      <td>yeletskiy</td>
      <td>ru</td>
      <td>48.76</td>
      <td>98</td>
      <td>0</td>
      <td>2.71</td>
    </tr>
    <tr>
      <th>10</th>
      <td>-77.57</td>
      <td>-24.10</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-51.41</td>
      <td>0</td>
      <td>0</td>
      <td>4.72</td>
    </tr>
    <tr>
      <th>11</th>
      <td>71.04</td>
      <td>123.47</td>
      <td>zhigansk</td>
      <td>ru</td>
      <td>38.68</td>
      <td>89</td>
      <td>32</td>
      <td>11.77</td>
    </tr>
    <tr>
      <th>12</th>
      <td>36.82</td>
      <td>-22.31</td>
      <td>vila franca do campo</td>
      <td>pt</td>
      <td>69.91</td>
      <td>100</td>
      <td>48</td>
      <td>18.48</td>
    </tr>
    <tr>
      <th>13</th>
      <td>36.14</td>
      <td>-108.44</td>
      <td>farmington</td>
      <td>us</td>
      <td>88.18</td>
      <td>29</td>
      <td>64</td>
      <td>4.72</td>
    </tr>
    <tr>
      <th>14</th>
      <td>74.33</td>
      <td>100.93</td>
      <td>khatanga</td>
      <td>ru</td>
      <td>35.53</td>
      <td>86</td>
      <td>48</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>15</th>
      <td>-78.72</td>
      <td>-135.59</td>
      <td>rikitea</td>
      <td>pf</td>
      <td>-26.48</td>
      <td>34</td>
      <td>0</td>
      <td>14.56</td>
    </tr>
    <tr>
      <th>16</th>
      <td>70.47</td>
      <td>-27.91</td>
      <td>illoqqortoormiut</td>
      <td>gl</td>
      <td>28.87</td>
      <td>87</td>
      <td>12</td>
      <td>0.92</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2.06</td>
      <td>156.01</td>
      <td>namatanai</td>
      <td>pg</td>
      <td>79.9</td>
      <td>100</td>
      <td>48</td>
      <td>7.96</td>
    </tr>
    <tr>
      <th>18</th>
      <td>37.77</td>
      <td>-18.38</td>
      <td>camacha</td>
      <td>pt</td>
      <td>67.93</td>
      <td>100</td>
      <td>8</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>19</th>
      <td>-54.10</td>
      <td>72.63</td>
      <td>souillac</td>
      <td>mu</td>
      <td>33.01</td>
      <td>100</td>
      <td>36</td>
      <td>48.34</td>
    </tr>
    <tr>
      <th>20</th>
      <td>-52.26</td>
      <td>0.70</td>
      <td>cape town</td>
      <td>za</td>
      <td>32.56</td>
      <td>100</td>
      <td>76</td>
      <td>23.73</td>
    </tr>
    <tr>
      <th>21</th>
      <td>11.42</td>
      <td>-143.02</td>
      <td>hilo</td>
      <td>us</td>
      <td>80.26</td>
      <td>100</td>
      <td>92</td>
      <td>17.81</td>
    </tr>
    <tr>
      <th>22</th>
      <td>-21.24</td>
      <td>90.54</td>
      <td>bengkulu</td>
      <td>id</td>
      <td>70.45</td>
      <td>100</td>
      <td>92</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>23</th>
      <td>-77.58</td>
      <td>-107.66</td>
      <td>punta arenas</td>
      <td>cl</td>
      <td>-4.52</td>
      <td>65</td>
      <td>64</td>
      <td>18.7</td>
    </tr>
    <tr>
      <th>24</th>
      <td>65.91</td>
      <td>-136.67</td>
      <td>mayo</td>
      <td>ca</td>
      <td>54.7</td>
      <td>98</td>
      <td>48</td>
      <td>3.38</td>
    </tr>
    <tr>
      <th>25</th>
      <td>-19.72</td>
      <td>155.54</td>
      <td>yeppoon</td>
      <td>au</td>
      <td>74.23</td>
      <td>99</td>
      <td>88</td>
      <td>9.19</td>
    </tr>
    <tr>
      <th>26</th>
      <td>-49.70</td>
      <td>11.80</td>
      <td>hermanus</td>
      <td>za</td>
      <td>35.08</td>
      <td>100</td>
      <td>20</td>
      <td>39.06</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1.27</td>
      <td>122.07</td>
      <td>gorontalo</td>
      <td>id</td>
      <td>81.07</td>
      <td>100</td>
      <td>12</td>
      <td>6.06</td>
    </tr>
    <tr>
      <th>28</th>
      <td>10.93</td>
      <td>-106.49</td>
      <td>lazaro cardenas</td>
      <td>mx</td>
      <td>81.25</td>
      <td>100</td>
      <td>68</td>
      <td>12.44</td>
    </tr>
    <tr>
      <th>29</th>
      <td>42.54</td>
      <td>-138.07</td>
      <td>port hardy</td>
      <td>ca</td>
      <td>59.47</td>
      <td>100</td>
      <td>0</td>
      <td>10.76</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>470</th>
      <td>13.59</td>
      <td>-114.04</td>
      <td>cabo san lucas</td>
      <td>mx</td>
      <td>83.32</td>
      <td>99</td>
      <td>12</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>471</th>
      <td>-16.39</td>
      <td>92.56</td>
      <td>bengkulu</td>
      <td>id</td>
      <td>74.77</td>
      <td>100</td>
      <td>44</td>
      <td>17.47</td>
    </tr>
    <tr>
      <th>472</th>
      <td>-17.00</td>
      <td>-55.86</td>
      <td>barao de melgaco</td>
      <td>br</td>
      <td>89.17</td>
      <td>34</td>
      <td>0</td>
      <td>2.59</td>
    </tr>
    <tr>
      <th>473</th>
      <td>-17.73</td>
      <td>73.47</td>
      <td>grand river south east</td>
      <td>mu</td>
      <td>72.7</td>
      <td>100</td>
      <td>64</td>
      <td>21.61</td>
    </tr>
    <tr>
      <th>474</th>
      <td>-81.92</td>
      <td>-24.31</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-46.37</td>
      <td>0</td>
      <td>0</td>
      <td>19.6</td>
    </tr>
    <tr>
      <th>475</th>
      <td>-48.46</td>
      <td>8.43</td>
      <td>cape town</td>
      <td>za</td>
      <td>37.06</td>
      <td>100</td>
      <td>64</td>
      <td>20.71</td>
    </tr>
    <tr>
      <th>476</th>
      <td>66.72</td>
      <td>11.14</td>
      <td>sandnessjoen</td>
      <td>no</td>
      <td>57.13</td>
      <td>97</td>
      <td>44</td>
      <td>17.25</td>
    </tr>
    <tr>
      <th>477</th>
      <td>2.30</td>
      <td>4.05</td>
      <td>yenagoa</td>
      <td>ng</td>
      <td>77.65</td>
      <td>100</td>
      <td>88</td>
      <td>13.67</td>
    </tr>
    <tr>
      <th>478</th>
      <td>-56.50</td>
      <td>131.79</td>
      <td>new norfolk</td>
      <td>au</td>
      <td>39.31</td>
      <td>90</td>
      <td>92</td>
      <td>33.58</td>
    </tr>
    <tr>
      <th>479</th>
      <td>41.85</td>
      <td>123.92</td>
      <td>fushun</td>
      <td>cn</td>
      <td>73.4</td>
      <td>88</td>
      <td>75</td>
      <td>4.47</td>
    </tr>
    <tr>
      <th>480</th>
      <td>1.76</td>
      <td>162.87</td>
      <td>butaritari</td>
      <td>ki</td>
      <td>82.24</td>
      <td>100</td>
      <td>0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>481</th>
      <td>-21.64</td>
      <td>101.74</td>
      <td>carnarvon</td>
      <td>au</td>
      <td>74.41</td>
      <td>100</td>
      <td>88</td>
      <td>7.18</td>
    </tr>
    <tr>
      <th>482</th>
      <td>-76.20</td>
      <td>-146.26</td>
      <td>mataura</td>
      <td>pf</td>
      <td>-24.95</td>
      <td>71</td>
      <td>8</td>
      <td>5.06</td>
    </tr>
    <tr>
      <th>483</th>
      <td>-22.87</td>
      <td>121.88</td>
      <td>port hedland</td>
      <td>au</td>
      <td>51.82</td>
      <td>51</td>
      <td>0</td>
      <td>6.06</td>
    </tr>
    <tr>
      <th>484</th>
      <td>-47.72</td>
      <td>98.77</td>
      <td>busselton</td>
      <td>au</td>
      <td>40.21</td>
      <td>100</td>
      <td>76</td>
      <td>34.69</td>
    </tr>
    <tr>
      <th>485</th>
      <td>71.57</td>
      <td>95.59</td>
      <td>khatanga</td>
      <td>ru</td>
      <td>42.55</td>
      <td>62</td>
      <td>0</td>
      <td>7.18</td>
    </tr>
    <tr>
      <th>486</th>
      <td>66.20</td>
      <td>-137.99</td>
      <td>mayo</td>
      <td>ca</td>
      <td>56.32</td>
      <td>93</td>
      <td>64</td>
      <td>4.38</td>
    </tr>
    <tr>
      <th>487</th>
      <td>-47.15</td>
      <td>-51.79</td>
      <td>mar del plata</td>
      <td>ar</td>
      <td>48.13</td>
      <td>100</td>
      <td>92</td>
      <td>32.91</td>
    </tr>
    <tr>
      <th>488</th>
      <td>63.48</td>
      <td>96.99</td>
      <td>baykit</td>
      <td>ru</td>
      <td>41.83</td>
      <td>61</td>
      <td>0</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>489</th>
      <td>14.31</td>
      <td>38.14</td>
      <td>aksum</td>
      <td>et</td>
      <td>60.73</td>
      <td>99</td>
      <td>80</td>
      <td>1.92</td>
    </tr>
    <tr>
      <th>490</th>
      <td>63.25</td>
      <td>-26.05</td>
      <td>olafsvik</td>
      <td>is</td>
      <td>49.75</td>
      <td>97</td>
      <td>92</td>
      <td>17.02</td>
    </tr>
    <tr>
      <th>491</th>
      <td>38.54</td>
      <td>125.79</td>
      <td>seoul</td>
      <td>kr</td>
      <td>68.11</td>
      <td>92</td>
      <td>0</td>
      <td>2.59</td>
    </tr>
    <tr>
      <th>492</th>
      <td>-46.17</td>
      <td>-28.24</td>
      <td>arraial do cabo</td>
      <td>br</td>
      <td>41.02</td>
      <td>100</td>
      <td>20</td>
      <td>31.56</td>
    </tr>
    <tr>
      <th>493</th>
      <td>-49.22</td>
      <td>62.76</td>
      <td>saint-philippe</td>
      <td>re</td>
      <td>40.48</td>
      <td>86</td>
      <td>92</td>
      <td>33.8</td>
    </tr>
    <tr>
      <th>494</th>
      <td>43.06</td>
      <td>-140.95</td>
      <td>sitka</td>
      <td>us</td>
      <td>59.83</td>
      <td>100</td>
      <td>56</td>
      <td>7.4</td>
    </tr>
    <tr>
      <th>495</th>
      <td>3.24</td>
      <td>129.72</td>
      <td>ternate</td>
      <td>id</td>
      <td>84.22</td>
      <td>100</td>
      <td>92</td>
      <td>9.53</td>
    </tr>
    <tr>
      <th>496</th>
      <td>-24.63</td>
      <td>162.88</td>
      <td>poya</td>
      <td>nc</td>
      <td>65.32</td>
      <td>100</td>
      <td>76</td>
      <td>3.49</td>
    </tr>
    <tr>
      <th>497</th>
      <td>63.44</td>
      <td>-55.25</td>
      <td>maniitsoq</td>
      <td>gl</td>
      <td>41.11</td>
      <td>96</td>
      <td>88</td>
      <td>13</td>
    </tr>
    <tr>
      <th>498</th>
      <td>-23.36</td>
      <td>-49.83</td>
      <td>jacarezinho</td>
      <td>br</td>
      <td>76.48</td>
      <td>45</td>
      <td>0</td>
      <td>7.4</td>
    </tr>
    <tr>
      <th>499</th>
      <td>-20.43</td>
      <td>-28.59</td>
      <td>caravelas</td>
      <td>br</td>
      <td>72.61</td>
      <td>100</td>
      <td>24</td>
      <td>8.41</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 8 columns</p>
</div>




```python
selected_cities.to_csv("WeatherData.csv", index=False)
```


```python
plt.scatter(selected_cities['Latitude'], selected_cities["Temperature"], c='b', alpha = .75)
plt.title("Latitude vs Temperature")
plt.xlabel("Latitude")
plt.ylabel("Temperature")
plt.savefig("Temperature.png")
plt.show()
```


![png](output_8_0.png)



```python
plt.scatter(selected_cities['Latitude'], selected_cities["Humidity"], c='r', alpha = .75)
plt.title("Latitude vs Humidity")
plt.xlabel("Latitude")
plt.ylabel("Humidity")
plt.savefig("Humidity.png")
plt.show()
```


![png](output_9_0.png)



```python
plt.scatter(selected_cities['Latitude'], selected_cities["Cloudiness"], c='g', alpha = .75)
plt.title("Latitude vs Cloudiness")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness")
plt.savefig("Cloudiness.png")
plt.show()
```


![png](output_10_0.png)



```python
plt.scatter(selected_cities['Latitude'], selected_cities["Wind Speed"], c='g', alpha = .75)
plt.title("Latitude vs Wind Speed")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed")
plt.savefig("Wind Speed.png")
plt.show()
```


![png](output_11_0.png)

# WeatherPy
