
Observation #1: There is much more land mass (and subsequently more cities) in the northern hemisphere compared to the southern and thus clusters data points on the right hand side of the plots.  

Obervation #2: Cloudiness/Wind Speed/Humidity don't seem to be affected by the distance from the equator. A larger and evenly dispersed dataset may return different conclusions though.

Obervation #3: Temperature seems to be the only clear trend in the plots that were done but was a little surprising to see less of a drop in temp as cities go south vs. north. I suppose this has something to do with less land mass and more cities that are located near oceans which would moderate temps.


```python
import csv
import matplotlib.pyplot as plt
import requests
import pandas as pd
import openweathermapy.core as ow
from citipy import citipy
import json
import datetime as dt

```


```python
file_name = "../twitter bot module/api_keys.json"
data = json.load(open(file_name))
wkey = data['openweathermapy_api_key']
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

```


```python
cities_csv = "../twitter bot module/worldcities.csv"
cities_df = pd.read_csv(cities_csv)
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
      <th>Country</th>
      <th>City</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ad</td>
      <td>andorra la vella</td>
      <td>42.500000</td>
      <td>1.516667</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ad</td>
      <td>canillo</td>
      <td>42.566667</td>
      <td>1.600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ad</td>
      <td>encamp</td>
      <td>42.533333</td>
      <td>1.583333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ad</td>
      <td>la massana</td>
      <td>42.550000</td>
      <td>1.516667</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ad</td>
      <td>les escaldes</td>
      <td>42.500000</td>
      <td>1.533333</td>
    </tr>
  </tbody>
</table>
</div>




```python
selected_cities = cities_df.sample(n=500, random_state=1)
```


```python
selected_cities["Temperature"] = ""
selected_cities["Humidity"] = ""
selected_cities["Cloudiness"] = ""
selected_cities["Wind Speed"] = ""
#selected_cities = selected_cities.reset_index()
decimals = pd.Series([2,2],index=['Latitude', 'Longitude'])
selected_cities = selected_cities.round(decimals)

row_count = 0

```


```python
for index, row in selected_cities.iterrows():
    target_url = url +"appid=" + wkey + "&lat=" + str(row["Latitude"]) + "&lon=" + str(row["Longitude"]) + "&units=" + units
    
    row_count += 1
    
    city_wx_data = requests.get(target_url).json()
    print("City Name: " + city_wx_data["name"] + ", Count:" + str(row_count) )
    print(target_url)
    
    try: 
        
        city_temp = city_wx_data["main"]["temp"]
        city_humid = city_wx_data["main"]["humidity"]
        city_clouds = city_wx_data["clouds"]["all"]
        city_wind = city_wx_data["wind"]["speed"]
        
        selected_cities.set_value(index, "Temperature", city_temp)
        selected_cities.set_value(index, "Humidity", city_humid)
        selected_cities.set_value(index, "Cloudiness", city_clouds)
        selected_cities.set_value(index, "Wind Speed", city_wind)
        
    except:
        print("Error with city data. Skipping")
        continue
        
selected_cities.to_csv("City_Weather.csv", sep=',', encoding='utf-8')
selected_cities.head()
```

    City Name: Saint-Felicien, Count:1
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.65&lon=-72.45&units=imperial
    

    C:\Users\Jeff\Anaconda3\envs\py36\lib\site-packages\ipykernel\__main__.py:17: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Jeff\Anaconda3\envs\py36\lib\site-packages\ipykernel\__main__.py:18: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Jeff\Anaconda3\envs\py36\lib\site-packages\ipykernel\__main__.py:19: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Jeff\Anaconda3\envs\py36\lib\site-packages\ipykernel\__main__.py:20: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    

    City Name: São Félix, Count:2
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-12.6&lon=-38.97&units=imperial
    City Name: Tagbubungang Diot, Count:3
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=11.13&lon=124.4&units=imperial
    City Name: El Plan, Count:4
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.28&lon=-87.97&units=imperial
    City Name: Lydenburg, Count:5
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-25.1&lon=30.46&units=imperial
    City Name: Schwandorf in Bayern, Count:6
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.33&lon=12.11&units=imperial
    City Name: Tarashcha, Count:7
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.69&lon=30.47&units=imperial
    City Name: Poiana Lacului, Count:8
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.82&lon=24.73&units=imperial
    City Name: Marche-en-Famenne, Count:9
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.2&lon=5.33&units=imperial
    City Name: Bala, Count:10
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.86&lon=22.91&units=imperial
    City Name: Nyborg, Count:11
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.31&lon=10.8&units=imperial
    City Name: Pinawa, Count:12
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.15&lon=-95.88&units=imperial
    City Name: Frunzivka, Count:13
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.33&lon=29.76&units=imperial
    City Name: Athens, Count:14
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.2&lon=-95.86&units=imperial
    City Name: Jovellar, Count:15
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.07&lon=123.6&units=imperial
    City Name: Medfield, Count:16
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.19&lon=-71.31&units=imperial
    City Name: Provita de Sus, Count:17
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.13&lon=25.63&units=imperial
    City Name: Puerto Baquerizo Moreno, Count:18
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-0.9&lon=-89.6&units=imperial
    City Name: Tsqneti, Count:19
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.69&lon=44.7&units=imperial
    City Name: Sharya, Count:20
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=58.37&lon=45.52&units=imperial
    City Name: Rebrikha, Count:21
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.01&lon=82.29&units=imperial
    City Name: Nuevo Progreso, Count:22
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=23.17&lon=-97.95&units=imperial
    City Name: West Saint Paul, Count:23
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.92&lon=-93.1&units=imperial
    City Name: Veglie, Count:24
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.33&lon=17.97&units=imperial
    City Name: Caldono, Count:25
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=2.8&lon=-76.48&units=imperial
    City Name: Arroio do Meio, Count:26
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-29.4&lon=-51.95&units=imperial
    City Name: Sandanski, Count:27
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.57&lon=23.28&units=imperial
    City Name: Ghotki, Count:28
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=28.01&lon=69.32&units=imperial
    City Name: Virginia, Count:29
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-28.1&lon=26.87&units=imperial
    City Name: Borough of Tameside, Count:30
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.5&lon=-2.1&units=imperial
    City Name: Paradip Garh, Count:31
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.32&lon=86.62&units=imperial
    City Name: Puerto Limon, Count:32
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.99&lon=-83.03&units=imperial
    City Name: Dublyany, Count:33
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.5&lon=23.39&units=imperial
    City Name: Zhigalovo, Count:34
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.81&lon=105.16&units=imperial
    City Name: Zarritap, Count:35
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.64&lon=45.51&units=imperial
    City Name: Bozova, Count:36
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=37.36&lon=38.53&units=imperial
    City Name: Hofu, Count:37
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.05&lon=131.57&units=imperial
    City Name: Sochaczew, Count:38
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.23&lon=20.25&units=imperial
    City Name: Semut, Count:39
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.66&lon=122.22&units=imperial
    City Name: Ngemplak, Count:40
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.3&lon=110.75&units=imperial
    City Name: Beauvais, Count:41
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.43&lon=2.08&units=imperial
    City Name: Tocantins, Count:42
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-21.17&lon=-43.03&units=imperial
    City Name: Lago, Count:43
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.27&lon=-12.15&units=imperial
    City Name: Pallappatti, Count:44
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.7&lon=77.88&units=imperial
    City Name: Kurchatov, Count:45
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.66&lon=35.65&units=imperial
    City Name: Negomiru, Count:46
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.83&lon=23.17&units=imperial
    City Name: Nagyrede, Count:47
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.77&lon=19.85&units=imperial
    City Name: Rio Gatun, Count:48
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.29&lon=-79.77&units=imperial
    City Name: Csaszartoltes, Count:49
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.42&lon=19.18&units=imperial
    City Name: Canta, Count:50
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.05&lon=28.04&units=imperial
    City Name: Acazulco, Count:51
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=19.27&lon=-99.43&units=imperial
    City Name: Payuhan, Count:52
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.94&lon=120.9&units=imperial
    City Name: Manjo, Count:53
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=4.85&lon=9.82&units=imperial
    City Name: Landen, Count:54
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.75&lon=5.08&units=imperial
    City Name: Ebersberg, Count:55
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.08&lon=11.97&units=imperial
    City Name: Kiskunmajsa, Count:56
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.49&lon=19.74&units=imperial
    City Name: Islaz, Count:57
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.73&lon=24.75&units=imperial
    City Name: Rhenen, Count:58
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.96&lon=5.57&units=imperial
    City Name: Harlau, Count:59
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.43&lon=26.9&units=imperial
    City Name: Weverstraat, Count:60
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.0&lon=4.13&units=imperial
    City Name: Mitake, Count:61
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.4&lon=137.06&units=imperial
    City Name: Kollam, Count:62
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.88&lon=76.59&units=imperial
    City Name: Oer-Erkenschwick, Count:63
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.63&lon=7.25&units=imperial
    City Name: Xixiang, Count:64
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.16&lon=112.86&units=imperial
    City Name: Santo Rosario, Count:65
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.43&lon=120.89&units=imperial
    City Name: Lukovo, Count:66
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.35&lon=20.6&units=imperial
    City Name: Ishimbay, Count:67
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.45&lon=56.04&units=imperial
    City Name: Ijuin, Count:68
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=31.62&lon=130.4&units=imperial
    City Name: Letavertes, Count:69
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.38&lon=21.9&units=imperial
    City Name: Marassi, Count:70
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.42&lon=8.95&units=imperial
    City Name: Novaya Malykla, Count:71
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.2&lon=49.95&units=imperial
    City Name: Creemore, Count:72
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.32&lon=-80.1&units=imperial
    City Name: Arrondissement de Dole, Count:73
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.09&lon=5.49&units=imperial
    City Name: Aanislag, Count:74
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.07&lon=123.7&units=imperial
    City Name: Khvastovichi, Count:75
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.47&lon=35.09&units=imperial
    City Name: Mankono, Count:76
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.06&lon=-6.19&units=imperial
    City Name: Guinticgan, Count:77
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=11.56&lon=123.12&units=imperial
    City Name: Carlentini, Count:78
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=37.27&lon=15.02&units=imperial
    City Name: Maliq, Count:79
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.71&lon=20.7&units=imperial
    City Name: Anulid, Count:80
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.84&lon=120.5&units=imperial
    City Name: Aksdal, Count:81
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=59.52&lon=5.58&units=imperial
    City Name: Mbuyapey, Count:82
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-26.2&lon=-56.75&units=imperial
    City Name: Lebowakgomo, Count:83
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-24.2&lon=29.5&units=imperial
    City Name: Uzhur, Count:84
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.99&lon=89.82&units=imperial
    City Name: Dukhovnitskoye, Count:85
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.48&lon=48.21&units=imperial
    City Name: Kuala Kangsar, Count:86
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=4.77&lon=100.93&units=imperial
    City Name: Marigot, Count:87
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.53&lon=-61.3&units=imperial
    City Name: Peerstalle, Count:88
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.07&lon=3.27&units=imperial
    City Name: San Dionisio Ocotepec, Count:89
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=16.78&lon=-96.33&units=imperial
    City Name: Elmina, Count:90
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.08&lon=-1.35&units=imperial
    City Name: Hasselt, Count:91
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.93&lon=5.33&units=imperial
    City Name: Mali Igos, Count:92
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.71&lon=19.67&units=imperial
    City Name: Santa Paz, Count:93
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.27&lon=124.78&units=imperial
    City Name: Cuxhaven, Count:94
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.88&lon=8.7&units=imperial
    City Name: Klimovsk, Count:95
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.36&lon=37.53&units=imperial
    City Name: Ejby, Count:96
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.43&lon=9.93&units=imperial
    City Name: Coronel Fabriciano, Count:97
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-19.52&lon=-42.63&units=imperial
    City Name: Coari, Count:98
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-4.11&lon=-63.14&units=imperial
    City Name: El Guamo, Count:99
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.03&lon=-74.98&units=imperial
    City Name: Sibulao, Count:100
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=7.32&lon=122.24&units=imperial
    City Name: Xiaoweizhai, Count:101
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=26.19&lon=107.51&units=imperial
    City Name: Wuhan, Count:102
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=30.58&lon=114.27&units=imperial
    City Name: Nidderau, Count:103
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.25&lon=8.9&units=imperial
    City Name: Commune de Brazzaville, Count:104
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-4.26&lon=15.28&units=imperial
    City Name: Yartsevo, Count:105
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.07&lon=32.7&units=imperial
    City Name: Tolga, Count:106
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=62.42&lon=11.0&units=imperial
    City Name: Elancourt, Count:107
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.78&lon=1.96&units=imperial
    City Name: Shushtar, Count:108
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.05&lon=48.86&units=imperial
    City Name: Videira, Count:109
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-27.0&lon=-51.13&units=imperial
    City Name: Victor Harbor, Count:110
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-35.55&lon=138.62&units=imperial
    City Name: Youghal, Count:111
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.95&lon=-7.85&units=imperial
    City Name: Itau de Minas, Count:112
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-20.72&lon=-46.75&units=imperial
    City Name: San Juan Nepomuceno, Count:113
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.95&lon=-75.08&units=imperial
    City Name: Buray, Count:114
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.72&lon=122.46&units=imperial
    City Name: Obshtina Elin Pelin, Count:115
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.67&lon=23.6&units=imperial
    City Name: Sumalig, Count:116
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.0&lon=123.66&units=imperial
    City Name: Kosti, Count:117
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.17&lon=32.67&units=imperial
    City Name: Paulba, Count:118
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.71&lon=123.25&units=imperial
    City Name: Roma, Count:119
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.83&lon=26.6&units=imperial
    City Name: Montalvo, Count:120
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-1.8&lon=-79.33&units=imperial
    City Name: Selfoss, Count:121
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=63.87&lon=-21.15&units=imperial
    City Name: San Pablo, Count:122
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=37.96&lon=-122.34&units=imperial
    City Name: Atami, Count:123
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.09&lon=139.07&units=imperial
    City Name: Mingshui, Count:124
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.17&lon=125.92&units=imperial
    City Name: Giulianova, Count:125
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.75&lon=13.95&units=imperial
    City Name: Lesu, Count:126
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.32&lon=24.75&units=imperial
    City Name: Pszow, Count:127
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.04&lon=18.4&units=imperial
    City Name: Murun-kuren, Count:128
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.65&lon=100.15&units=imperial
    City Name: Podolsk, Count:129
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.42&lon=37.55&units=imperial
    City Name: Pinukpuk, Count:130
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.58&lon=121.37&units=imperial
    City Name: Bodegraven, Count:131
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.09&lon=4.76&units=imperial
    City Name: Torre del Greco, Count:132
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.78&lon=14.37&units=imperial
    City Name: Velden am Worthersee, Count:133
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.62&lon=14.05&units=imperial
    City Name: Utuloa, Count:134
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-13.5&lon=-172.8&units=imperial
    City Name: Lumbayan, Count:135
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.95&lon=122.14&units=imperial
    City Name: Mangai, Count:136
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-4.05&lon=19.53&units=imperial
    City Name: Chiri-Yurt, Count:137
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.09&lon=45.72&units=imperial
    City Name: Ikon-Khalk, Count:138
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.31&lon=41.91&units=imperial
    City Name: Baki, Count:139
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.61&lon=110.78&units=imperial
    City Name: Purushottampur, Count:140
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=19.53&lon=84.88&units=imperial
    City Name: Wijnegem, Count:141
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.23&lon=4.53&units=imperial
    City Name: Darmanesti, Count:142
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.92&lon=25.78&units=imperial
    City Name: Tuka, Count:143
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.52&lon=124.58&units=imperial
    City Name: Shouguang, Count:144
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.88&lon=118.74&units=imperial
    City Name: Sandpoint, Count:145
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.28&lon=-116.55&units=imperial
    City Name: Tuscaloosa, Count:146
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=33.21&lon=-87.57&units=imperial
    City Name: South San Jose Hills, Count:147
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.01&lon=-117.9&units=imperial
    City Name: Madhugiri, Count:148
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.66&lon=77.21&units=imperial
    City Name: Rizal, Count:149
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.71&lon=122.37&units=imperial
    City Name: Manta, Count:150
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.01&lon=-73.54&units=imperial
    City Name: Rostusha, Count:151
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.61&lon=20.6&units=imperial
    City Name: Taktaharkany, Count:152
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.08&lon=21.13&units=imperial
    City Name: Alnashi, Count:153
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.19&lon=52.48&units=imperial
    City Name: Lagos de Moreno, Count:154
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=21.35&lon=-101.92&units=imperial
    City Name: Purna, Count:155
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=19.18&lon=77.05&units=imperial
    City Name: Chitila, Count:156
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.51&lon=25.98&units=imperial
    City Name: Narasapur, Count:157
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=16.45&lon=81.67&units=imperial
    City Name: Mikulovice, Count:158
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.3&lon=17.32&units=imperial
    City Name: Perőcsitanya, Count:159
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.6&lon=19.36&units=imperial
    City Name: Tenno, Count:160
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.9&lon=139.97&units=imperial
    City Name: Bustuchin, Count:161
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.97&lon=23.73&units=imperial
    City Name: Clarksburg, Count:162
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.28&lon=-80.34&units=imperial
    City Name: Tasco, Count:163
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.91&lon=-72.78&units=imperial
    City Name: Pirapo, Count:164
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-26.85&lon=-55.53&units=imperial
    City Name: Montecchio Maggiore, Count:165
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.5&lon=11.4&units=imperial
    City Name: Nizhniy Bestyakh, Count:166
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=61.96&lon=129.91&units=imperial
    City Name: Paluan, Count:167
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.42&lon=120.46&units=imperial
    City Name: San Juan de Dios, Count:168
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.88&lon=-84.08&units=imperial
    City Name: Poblacion, San Felipe, Count:169
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.06&lon=120.07&units=imperial
    City Name: Gaspar, Count:170
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-26.93&lon=-48.97&units=imperial
    City Name: Seia, Count:171
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.42&lon=-7.71&units=imperial
    City Name: Boyany, Count:172
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.27&lon=26.13&units=imperial
    City Name: Kibre Mengist, Count:173
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.88&lon=38.98&units=imperial
    City Name: Gbongan, Count:174
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=7.47&lon=4.35&units=imperial
    City Name: Farrukhnagar, Count:175
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=28.45&lon=76.82&units=imperial
    City Name: Csokmo, Count:176
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.03&lon=21.3&units=imperial
    City Name: Andreapol, Count:177
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.65&lon=32.27&units=imperial
    City Name: Albemarle, Count:178
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.35&lon=-80.2&units=imperial
    City Name: Motril, Count:179
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.75&lon=-3.52&units=imperial
    City Name: Troy, Count:180
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.61&lon=-83.15&units=imperial
    City Name: Morrinhos, Count:181
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-10.44&lon=-45.13&units=imperial
    City Name: Carrick-on-Suir, Count:182
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.35&lon=-7.41&units=imperial
    City Name: Herning, Count:183
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.13&lon=8.98&units=imperial
    City Name: Deseronto, Count:184
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.2&lon=-77.03&units=imperial
    City Name: Udaipur, Count:185
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=23.53&lon=91.48&units=imperial
    City Name: Ruatoki North, Count:186
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-38.07&lon=177.02&units=imperial
    City Name: Kilyazi, Count:187
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.87&lon=49.34&units=imperial
    City Name: Fratesti, Count:188
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.97&lon=25.97&units=imperial
    City Name: Thymiana, Count:189
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.32&lon=26.13&units=imperial
    City Name: Al ‘Atabah, Count:190
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=30.05&lon=31.25&units=imperial
    City Name: Krasnokholm, Count:191
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.6&lon=54.16&units=imperial
    City Name: Palpa, Count:192
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-14.53&lon=-75.19&units=imperial
    City Name: Verkhnechusovskiye Gorodki, Count:193
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=58.17&lon=57.11&units=imperial
    City Name: Machacamarca, Count:194
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-18.17&lon=-67.03&units=imperial
    City Name: Nikolayevka, Count:195
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.15&lon=45.14&units=imperial
    City Name: Poienesti, Count:196
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.65&lon=27.45&units=imperial
    City Name: Santa Rosa, Count:197
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-6.77&lon=-79.83&units=imperial
    City Name: Dukuhturi, Count:198
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-6.97&lon=109.06&units=imperial
    City Name: Aldergrove, Count:199
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.06&lon=-122.46&units=imperial
    City Name: Komsomolskoye, Count:200
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.26&lon=47.54&units=imperial
    City Name: Ramhormoz, Count:201
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=31.28&lon=49.6&units=imperial
    City Name: Mbutuy, Count:202
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-24.97&lon=-56.3&units=imperial
    City Name: Huichapan, Count:203
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.38&lon=-99.65&units=imperial
    City Name: Leova, Count:204
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.48&lon=28.26&units=imperial
    City Name: South Charleston, Count:205
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.37&lon=-81.7&units=imperial
    City Name: Sarkeresztur, Count:206
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.01&lon=18.54&units=imperial
    City Name: Santo Tomas Chautla, Count:207
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.95&lon=-98.15&units=imperial
    City Name: Stupari, Count:208
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.31&lon=18.68&units=imperial
    City Name: Sasykoli, Count:209
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.55&lon=47.0&units=imperial
    City Name: Ulmeni, Count:210
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.07&lon=26.65&units=imperial
    City Name: Collalabo, Count:211
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.1&lon=121.7&units=imperial
    City Name: Beausoleil, Count:212
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.75&lon=7.42&units=imperial
    City Name: Khorramshahr, Count:213
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=30.44&lon=48.17&units=imperial
    City Name: Bad Lippspringe, Count:214
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.78&lon=8.82&units=imperial
    City Name: Policarpa, Count:215
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=1.63&lon=-77.46&units=imperial
    City Name: Baku City, Count:216
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.4&lon=49.9&units=imperial
    City Name: Gurranebraher, Count:217
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.9&lon=-8.5&units=imperial
    City Name: Arco, Count:218
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.92&lon=10.88&units=imperial
    City Name: Mil’kovo, Count:219
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.72&lon=158.62&units=imperial
    City Name: Santoshpur, Count:220
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=22.53&lon=88.26&units=imperial
    City Name: Dolhasca, Count:221
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.43&lon=26.6&units=imperial
    City Name: Nospelt, Count:222
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.68&lon=6.01&units=imperial
    City Name: Watsonville, Count:223
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.91&lon=-121.76&units=imperial
    City Name: Karyotissa, Count:224
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.77&lon=22.31&units=imperial
    City Name: Gualdo Tadino, Count:225
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.23&lon=12.78&units=imperial
    City Name: Boueni, Count:226
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-12.9&lon=45.08&units=imperial
    City Name: Bad Doberan, Count:227
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.1&lon=11.9&units=imperial
    City Name: Lola, Count:228
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=7.8&lon=-8.53&units=imperial
    City Name: Reutte, Count:229
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.48&lon=10.72&units=imperial
    City Name: Kumanovo, Count:230
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.13&lon=21.71&units=imperial
    City Name: San Eduardo, Count:231
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=12.17&lon=125.45&units=imperial
    City Name: Bayanga, Count:232
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=7.54&lon=124.24&units=imperial
    City Name: Valega, Count:233
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.84&lon=-8.58&units=imperial
    City Name: Alcains, Count:234
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.92&lon=-7.46&units=imperial
    City Name: Den Chai, Count:235
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.98&lon=100.05&units=imperial
    City Name: Bhum, Count:236
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.47&lon=75.67&units=imperial
    City Name: Dunmore Town, Count:237
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=25.5&lon=-76.65&units=imperial
    City Name: Kennebunk, Count:238
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.38&lon=-70.55&units=imperial
    City Name: Sveio, Count:239
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=59.54&lon=5.35&units=imperial
    City Name: Dimos Sintiki, Count:240
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.28&lon=23.23&units=imperial
    City Name: Ngasin, Count:241
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.27&lon=112.56&units=imperial
    City Name: Teyateyaneng, Count:242
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-29.15&lon=27.73&units=imperial
    City Name: Tisul, Count:243
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.53&lon=88.14&units=imperial
    City Name: Sindon, Count:244
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.06&lon=122.02&units=imperial
    City Name: Cocoa Beach, Count:245
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=28.32&lon=-80.61&units=imperial
    City Name: Jhusi, Count:246
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=25.43&lon=81.93&units=imperial
    City Name: Vinh Yen, Count:247
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=21.31&lon=105.6&units=imperial
    City Name: Jáltipan de Morelos, Count:248
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.97&lon=-94.7&units=imperial
    City Name: Rio Hato, Count:249
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.38&lon=-80.17&units=imperial
    City Name: Colomi, Count:250
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-17.35&lon=-65.87&units=imperial
    City Name: Saint-Andre-Avellin, Count:251
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.72&lon=-75.05&units=imperial
    City Name: Lerici, Count:252
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.07&lon=9.92&units=imperial
    City Name: Shahr-e Kord, Count:253
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.33&lon=50.86&units=imperial
    City Name: Winter Springs, Count:254
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=28.7&lon=-81.31&units=imperial
    City Name: Sinfra, Count:255
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.62&lon=-5.91&units=imperial
    City Name: Kara-Tyube, Count:256
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.63&lon=45.44&units=imperial
    City Name: Syumsi, Count:257
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.11&lon=51.61&units=imperial
    City Name: Forbach, Count:258
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.19&lon=6.89&units=imperial
    City Name: Crivina, Count:259
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.58&lon=25.98&units=imperial
    City Name: Zugdidi, Count:260
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.5&lon=41.86&units=imperial
    City Name: Rosenlund, Count:261
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.78&lon=14.18&units=imperial
    City Name: Proba, Count:262
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=60.09&lon=30.84&units=imperial
    City Name: Novyye Lapsary, Count:263
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.07&lon=47.21&units=imperial
    City Name: Kstovo, Count:264
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.15&lon=44.2&units=imperial
    City Name: Mindyak, Count:265
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.02&lon=58.79&units=imperial
    City Name: Cabanatuan, Count:266
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.49&lon=120.97&units=imperial
    City Name: Oral, Count:267
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.18&lon=51.34&units=imperial
    City Name: Tenno, Count:268
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.87&lon=139.85&units=imperial
    City Name: Kiliya, Count:269
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.46&lon=29.26&units=imperial
    City Name: Gaillard, Count:270
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.19&lon=6.22&units=imperial
    City Name: Haltom City, Count:271
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.8&lon=-97.27&units=imperial
    City Name: Kibangay, Count:272
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.05&lon=124.89&units=imperial
    City Name: Monticello, Count:273
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.31&lon=-93.79&units=imperial
    City Name: Madrid, Count:274
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=4.73&lon=-74.26&units=imperial
    City Name: Ural’skiy, Count:275
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.9&lon=55.48&units=imperial
    City Name: El Rancho, Count:276
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.68&lon=-87.5&units=imperial
    City Name: Soskut, Count:277
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.41&lon=18.82&units=imperial
    City Name: Akouda, Count:278
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.87&lon=10.57&units=imperial
    City Name: Baca, Count:279
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=21.1&lon=-89.42&units=imperial
    City Name: Atsipopoulon, Count:280
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.35&lon=24.4&units=imperial
    City Name: Mulbagal, Count:281
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.17&lon=78.4&units=imperial
    City Name: Farakka, Count:282
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=24.82&lon=87.9&units=imperial
    City Name: Coalburn, Count:283
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.58&lon=-3.9&units=imperial
    City Name: Szoboszlaitanya, Count:284
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.98&lon=22.52&units=imperial
    City Name: City of Derry, Count:285
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.0&lon=-7.25&units=imperial
    City Name: San Carlos Park, Count:286
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=26.47&lon=-81.8&units=imperial
    City Name: Gorbatovka, Count:287
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.26&lon=43.75&units=imperial
    City Name: Jevicko, Count:288
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.63&lon=16.71&units=imperial
    City Name: Elista, Count:289
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.31&lon=44.26&units=imperial
    City Name: San Pedro, Count:290
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.46&lon=-75.56&units=imperial
    City Name: Narovchat, Count:291
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.88&lon=43.69&units=imperial
    City Name: Zlynka, Count:292
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.49&lon=31.54&units=imperial
    City Name: Belyy Yar, Count:293
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=58.44&lon=85.04&units=imperial
    City Name: Yenakiyeve, Count:294
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.25&lon=38.2&units=imperial
    City Name: Cuenca, Count:295
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.07&lon=-2.13&units=imperial
    City Name: Lumajang, Count:296
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-8.13&lon=113.22&units=imperial
    City Name: Xinxiang, Count:297
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.31&lon=113.87&units=imperial
    City Name: Kadaka, Count:298
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-6.53&lon=106.58&units=imperial
    City Name: Prochoma, Count:299
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.8&lon=22.66&units=imperial
    City Name: Antri, Count:300
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=26.06&lon=78.21&units=imperial
    City Name: Udarnyy, Count:301
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.34&lon=42.86&units=imperial
    City Name: Devonport, Count:302
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-41.18&lon=146.35&units=imperial
    City Name: Merville, Count:303
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.64&lon=2.64&units=imperial
    City Name: Diawara, Count:304
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.9&lon=-12.45&units=imperial
    City Name: Tila, Count:305
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.4&lon=-92.48&units=imperial
    City Name: Qiryat Yam, Count:306
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.85&lon=35.07&units=imperial
    City Name: Sharhorod, Count:307
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.68&lon=28.29&units=imperial
    City Name: Balingcanaway, Count:308
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.48&lon=120.69&units=imperial
    City Name: Nagireddipalli, Count:309
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.27&lon=79.1&units=imperial
    City Name: Zorlentu Mare, Count:310
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.45&lon=21.96&units=imperial
    City Name: Cigand, Count:311
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.26&lon=21.89&units=imperial
    City Name: Englewood, Count:312
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.88&lon=-84.3&units=imperial
    City Name: Sis, Count:313
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.03&lon=44.37&units=imperial
    City Name: Zavety Ilicha, Count:314
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.03&lon=140.25&units=imperial
    City Name: Mundwa, Count:315
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=27.07&lon=73.82&units=imperial
    City Name: Itapeva, Count:316
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-23.97&lon=-48.87&units=imperial
    City Name: Ekibastuz, Count:317
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.67&lon=75.37&units=imperial
    City Name: Cansilayan, Count:318
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.56&lon=123.02&units=imperial
    City Name: Clemmons, Count:319
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.02&lon=-80.38&units=imperial
    City Name: David, Count:320
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.43&lon=-82.43&units=imperial
    City Name: Solina, Count:321
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.56&lon=18.69&units=imperial
    City Name: Bacon, Count:322
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.04&lon=124.04&units=imperial
    City Name: Manubul, Count:323
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.47&lon=120.8&units=imperial
    City Name: Macayug, Count:324
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=16.11&lon=120.44&units=imperial
    City Name: Nea Makri, Count:325
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.08&lon=23.98&units=imperial
    City Name: Itabira, Count:326
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-19.62&lon=-43.22&units=imperial
    City Name: L’Agha, Count:327
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.76&lon=3.05&units=imperial
    City Name: Dedovichi, Count:328
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.55&lon=29.95&units=imperial
    City Name: Marion, Count:329
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.59&lon=-83.13&units=imperial
    City Name: Rosiesti, Count:330
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.43&lon=27.88&units=imperial
    City Name: Vrutky, Count:331
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.12&lon=18.92&units=imperial
    City Name: Botoroaga, Count:332
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.15&lon=25.54&units=imperial
    City Name: Ufimskiy, Count:333
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.71&lon=58.05&units=imperial
    City Name: Bereldange, Count:334
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.66&lon=6.13&units=imperial
    City Name: Logrono, Count:335
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.47&lon=-2.45&units=imperial
    City Name: Calongbuyan, Count:336
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.18&lon=120.47&units=imperial
    City Name: Viloco, Count:337
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-16.95&lon=-67.55&units=imperial
    City Name: Otanche, Count:338
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.66&lon=-74.18&units=imperial
    City Name: Pidhorodna, Count:339
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.11&lon=30.89&units=imperial
    City Name: Plyussa, Count:340
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=58.42&lon=29.42&units=imperial
    City Name: Ôlho d’Água de Igreja, Count:341
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.28&lon=-38.95&units=imperial
    City Name: Bellview, Count:342
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=30.46&lon=-87.32&units=imperial
    City Name: Guachucal, Count:343
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=0.96&lon=-77.73&units=imperial
    City Name: Markaz-e Woluswali-ye Achin, Count:344
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.08&lon=70.67&units=imperial
    City Name: Wallis et Futuna, Count:345
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-13.35&lon=-176.18&units=imperial
    City Name: Santa Catarina Pinula, Count:346
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.57&lon=-90.5&units=imperial
    City Name: Cerna, Count:347
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.08&lon=28.32&units=imperial
    City Name: Khoni, Count:348
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.32&lon=42.43&units=imperial
    City Name: Pirot, Count:349
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.15&lon=22.59&units=imperial
    City Name: Kalanchak, Count:350
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.26&lon=33.29&units=imperial
    City Name: Yukon, Count:351
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.51&lon=-97.76&units=imperial
    City Name: Itajuipe, Count:352
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-14.68&lon=-39.37&units=imperial
    City Name: Sammyō, Count:353
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=32.03&lon=131.43&units=imperial
    City Name: Volcan, Count:354
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.77&lon=-82.63&units=imperial
    City Name: Chitose, Count:355
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.82&lon=141.65&units=imperial
    City Name: Itaugua, Count:356
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-25.38&lon=-57.33&units=imperial
    City Name: San Casimiro, Count:357
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.55&lon=120.78&units=imperial
    City Name: Timon, Count:358
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-5.1&lon=-42.82&units=imperial
    City Name: Sujiatun, Count:359
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.66&lon=123.34&units=imperial
    City Name: Gilleleje, Count:360
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.12&lon=12.31&units=imperial
    City Name: Marl, Count:361
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.65&lon=7.08&units=imperial
    City Name: Sidoarjo, Count:362
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.45&lon=112.72&units=imperial
    City Name: Melendugno, Count:363
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.27&lon=18.33&units=imperial
    City Name: Jasper, Count:364
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.39&lon=-86.93&units=imperial
    City Name: Westview, Count:365
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=25.88&lon=-80.24&units=imperial
    City Name: Fairview, Count:366
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.07&lon=-118.4&units=imperial
    City Name: Selamo, Count:367
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.52&lon=16.28&units=imperial
    City Name: Namanyere, Count:368
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.52&lon=31.05&units=imperial
    City Name: Ciocanesti, Count:369
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.6&lon=25.85&units=imperial
    City Name: Katyuzhanka, Count:370
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.81&lon=30.14&units=imperial
    City Name: Crewe, Count:371
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.1&lon=-2.43&units=imperial
    City Name: Barcelos, Count:372
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.54&lon=-8.62&units=imperial
    City Name: Racoasa, Count:373
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.0&lon=26.88&units=imperial
    City Name: Preili, Count:374
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.3&lon=26.72&units=imperial
    City Name: Piracuruca, Count:375
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-3.93&lon=-41.7&units=imperial
    City Name: Nishiwaki, Count:376
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.98&lon=134.97&units=imperial
    City Name: Lethbridge, Count:377
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.7&lon=-112.83&units=imperial
    City Name: Guantanamo, Count:378
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.14&lon=-75.21&units=imperial
    City Name: Zarzal, Count:379
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=4.39&lon=-76.07&units=imperial
    City Name: Hailey, Count:380
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.52&lon=-114.31&units=imperial
    City Name: Orange Cove, Count:381
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.62&lon=-119.31&units=imperial
    City Name: Stefanavikion, Count:382
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.47&lon=22.73&units=imperial
    City Name: Oberkirch, Count:383
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.53&lon=8.08&units=imperial
    City Name: Vysoke Myto, Count:384
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.95&lon=16.15&units=imperial
    City Name: Huayucachi, Count:385
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-12.13&lon=-75.23&units=imperial
    City Name: White Center, Count:386
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.52&lon=-122.35&units=imperial
    City Name: Ibiracu, Count:387
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-19.83&lon=-40.37&units=imperial
    City Name: Timmins, Count:388
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=48.47&lon=-81.33&units=imperial
    City Name: Acri, Count:389
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.48&lon=16.38&units=imperial
    City Name: Claremorris, Count:390
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.72&lon=-9.0&units=imperial
    City Name: Rosehearty, Count:391
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.68&lon=-2.12&units=imperial
    City Name: Thung Song, Count:392
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.16&lon=99.68&units=imperial
    City Name: Kamareddi, Count:393
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.32&lon=78.35&units=imperial
    City Name: Sotik, Count:394
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-0.68&lon=35.12&units=imperial
    City Name: Provideniya, Count:395
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=64.38&lon=-173.3&units=imperial
    City Name: Duekoue, Count:396
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.74&lon=-7.35&units=imperial
    City Name: Subiaco, Count:397
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.92&lon=13.1&units=imperial
    City Name: Lino Lakes, Count:398
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.16&lon=-93.09&units=imperial
    City Name: Durham, Count:399
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.77&lon=-1.57&units=imperial
    City Name: Tacoma, Count:400
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.25&lon=-122.44&units=imperial
    City Name: Mettlach, Count:401
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.5&lon=6.6&units=imperial
    City Name: Canelli, Count:402
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.72&lon=8.28&units=imperial
    City Name: Menzel Heurr, Count:403
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=36.78&lon=10.99&units=imperial
    City Name: Obo, Count:404
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=5.4&lon=26.5&units=imperial
    City Name: Nirgua, Count:405
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.15&lon=-68.56&units=imperial
    City Name: Lyndhurst, Count:406
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.52&lon=-81.49&units=imperial
    City Name: Ihlow, Count:407
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.88&lon=13.33&units=imperial
    City Name: Arskiy Rayon, Count:408
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.25&lon=49.45&units=imperial
    City Name: Wolfsberg, Count:409
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.84&lon=14.84&units=imperial
    City Name: Tangkak, Count:410
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=2.27&lon=102.55&units=imperial
    City Name: Teapa, Count:411
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=17.55&lon=-92.95&units=imperial
    City Name: San Lorenzo, Count:412
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.48&lon=-91.52&units=imperial
    City Name: Lyubinskiy, Count:413
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.15&lon=72.69&units=imperial
    City Name: Mardin, Count:414
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=37.31&lon=40.74&units=imperial
    City Name: Blue Ash, Count:415
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=39.23&lon=-84.38&units=imperial
    City Name: West Richland, Count:416
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.3&lon=-119.36&units=imperial
    City Name: Badarganj, Count:417
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=25.67&lon=89.05&units=imperial
    City Name: Salgado de Sao Felix, Count:418
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-7.35&lon=-35.43&units=imperial
    City Name: Dubki, Count:419
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.02&lon=46.84&units=imperial
    City Name: Sandolot, Count:420
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.1&lon=123.02&units=imperial
    City Name: Sanmenxia, Count:421
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.77&lon=111.18&units=imperial
    City Name: Vadakku Valliyur, Count:422
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=8.38&lon=77.65&units=imperial
    City Name: Tileagd, Count:423
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.07&lon=22.2&units=imperial
    City Name: Enniscorthy, Count:424
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.5&lon=-6.56&units=imperial
    City Name: Brunico, Count:425
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.8&lon=11.93&units=imperial
    City Name: Rio de Mouro, Count:426
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.77&lon=-9.33&units=imperial
    City Name: Chiche, Count:427
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.01&lon=-91.06&units=imperial
    City Name: Santa Rosa, Count:428
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-14.17&lon=-66.88&units=imperial
    City Name: Warrenton, Count:429
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-28.11&lon=24.85&units=imperial
    City Name: San Pietro Vernotico, Count:430
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.49&lon=18.0&units=imperial
    City Name: Novocheboksarsk, Count:431
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=56.11&lon=47.48&units=imperial
    City Name: Capitanejo, Count:432
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=6.53&lon=-72.7&units=imperial
    City Name: Dalaba, Count:433
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.7&lon=-12.25&units=imperial
    City Name: Dornesti, Count:434
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.87&lon=26.02&units=imperial
    City Name: Delfzijl, Count:435
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=53.33&lon=6.92&units=imperial
    City Name: Kemnay, Count:436
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.23&lon=-2.45&units=imperial
    City Name: Teonthar, Count:437
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=24.98&lon=81.65&units=imperial
    City Name: Windsor, Count:438
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=38.55&lon=-122.82&units=imperial
    City Name: Capelle aan den IJssel, Count:439
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=51.93&lon=4.58&units=imperial
    City Name: Puracic, Count:440
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.55&lon=18.48&units=imperial
    City Name: Talen, Count:441
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=23.57&lon=76.72&units=imperial
    City Name: Cercany, Count:442
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.85&lon=14.71&units=imperial
    City Name: Concepcion del Sur, Count:443
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=14.8&lon=-88.17&units=imperial
    City Name: Bochalema, Count:444
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=7.61&lon=-72.65&units=imperial
    City Name: Lakeville, Count:445
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.65&lon=-93.24&units=imperial
    City Name: Bulod, Count:446
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.68&lon=123.15&units=imperial
    City Name: Dhenkanal, Count:447
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.67&lon=85.6&units=imperial
    City Name: Comlosu Mare, Count:448
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.89&lon=20.63&units=imperial
    City Name: Zogno, Count:449
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.8&lon=9.67&units=imperial
    City Name: Omdurman, Count:450
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=15.64&lon=32.44&units=imperial
    City Name: Caledonia, Count:451
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=42.81&lon=-87.92&units=imperial
    City Name: Castelnau-le-Lez, Count:452
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.63&lon=3.9&units=imperial
    City Name: Turbiv, Count:453
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.35&lon=28.72&units=imperial
    City Name: Lipova, Count:454
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.72&lon=27.23&units=imperial
    City Name: San Andres Tuxtla, Count:455
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.45&lon=-95.22&units=imperial
    City Name: Valea Mare, Count:456
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.78&lon=25.23&units=imperial
    City Name: Micasasa, Count:457
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.08&lon=24.12&units=imperial
    City Name: Lumphat, Count:458
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=13.5&lon=106.98&units=imperial
    City Name: Ustrzyki Dolne, Count:459
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.43&lon=22.58&units=imperial
    City Name: Mims, Count:460
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=28.66&lon=-80.84&units=imperial
    City Name: Cateasca, Count:461
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.77&lon=25.08&units=imperial
    City Name: Vladyslavivka, Count:462
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.17&lon=35.38&units=imperial
    City Name: Orel-Izumrud, Count:463
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.46&lon=39.92&units=imperial
    City Name: Richmond Heights, Count:464
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=25.63&lon=-80.37&units=imperial
    City Name: South Elgin, Count:465
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=41.99&lon=-88.29&units=imperial
    City Name: Sulangan, Count:466
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.94&lon=125.83&units=imperial
    City Name: Kaluga, Count:467
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=54.53&lon=36.28&units=imperial
    City Name: Lermontovka, Count:468
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=47.15&lon=134.34&units=imperial
    City Name: Pogranichnyy, Count:469
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=44.41&lon=131.38&units=imperial
    City Name: Burgos, Count:470
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=16.07&lon=121.05&units=imperial
    City Name: Levikha, Count:471
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=57.58&lon=59.9&units=imperial
    City Name: Medgyesegyhaza, Count:472
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.5&lon=21.03&units=imperial
    City Name: Geiro, Count:473
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-6.15&lon=36.87&units=imperial
    City Name: Klecany, Count:474
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=50.18&lon=14.41&units=imperial
    City Name: Catayuan, Count:475
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.16&lon=121.65&units=imperial
    City Name: Hrasno, Count:476
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.85&lon=18.38&units=imperial
    City Name: Khalabat, Count:477
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=34.06&lon=72.89&units=imperial
    City Name: Lysva, Count:478
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=58.11&lon=57.81&units=imperial
    City Name: Huiloapan, Count:479
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=18.75&lon=-97.32&units=imperial
    City Name: Ruzhyn, Count:480
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.72&lon=29.2&units=imperial
    City Name: Bang Saphan, Count:481
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=11.21&lon=99.51&units=imperial
    City Name: Strzelce Krajenskie, Count:482
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=52.88&lon=15.53&units=imperial
    City Name: Hendersonville, Count:483
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.32&lon=-82.46&units=imperial
    City Name: Amdos, Count:484
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=9.88&lon=123.1&units=imperial
    City Name: Jouy-le-Moutier, Count:485
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=49.01&lon=2.04&units=imperial
    City Name: Bracebridge, Count:486
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=45.05&lon=-79.4&units=imperial
    City Name: Moskovskiy, Count:487
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=55.6&lon=37.35&units=imperial
    City Name: Oi, Count:488
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=35.87&lon=139.56&units=imperial
    City Name: Alejandro Korn, Count:489
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-34.98&lon=-58.38&units=imperial
    City Name: Figueiredo, Count:490
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=40.84&lon=-8.48&units=imperial
    City Name: Himaya, Count:491
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=10.45&lon=122.83&units=imperial
    City Name: Hermanus, Count:492
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-34.42&lon=19.23&units=imperial
    City Name: Chilchota, Count:493
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=19.85&lon=-102.13&units=imperial
    City Name: San Juan de Arama, Count:494
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=3.35&lon=-73.89&units=imperial
    City Name: Cardonal, Count:495
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.62&lon=-99.12&units=imperial
    City Name: Belaya Glina, Count:496
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=46.08&lon=40.87&units=imperial
    City Name: Kefamenanu, Count:497
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-9.45&lon=124.48&units=imperial
    City Name: Santa Cruz del Valle, Count:498
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=20.53&lon=-103.43&units=imperial
    City Name: Conway, Count:499
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=43.98&lon=-71.12&units=imperial
    City Name: Tamasane, Count:500
    http://api.openweathermap.org/data/2.5/weather?appid=10c4f684ad8218433ee6f20b577f106c&lat=-22.32&lon=27.37&units=imperial
    




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
      <th>Country</th>
      <th>City</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4943</th>
      <td>ca</td>
      <td>saint-felicien</td>
      <td>48.65</td>
      <td>-72.45</td>
      <td>-11.21</td>
      <td>76</td>
      <td>5</td>
      <td>4.27</td>
    </tr>
    <tr>
      <th>2566</th>
      <td>br</td>
      <td>cachoeira</td>
      <td>-12.60</td>
      <td>-38.97</td>
      <td>77.38</td>
      <td>80</td>
      <td>44</td>
      <td>5.95</td>
    </tr>
    <tr>
      <th>30331</th>
      <td>ph</td>
      <td>tagbubunga</td>
      <td>11.13</td>
      <td>124.40</td>
      <td>81.2</td>
      <td>97</td>
      <td>8</td>
      <td>9.42</td>
    </tr>
    <tr>
      <th>15071</th>
      <td>hn</td>
      <td>pimienta</td>
      <td>15.28</td>
      <td>-87.97</td>
      <td>75.2</td>
      <td>100</td>
      <td>20</td>
      <td>9.17</td>
    </tr>
    <tr>
      <th>46631</th>
      <td>za</td>
      <td>lydenburg</td>
      <td>-25.10</td>
      <td>30.46</td>
      <td>59.33</td>
      <td>100</td>
      <td>64</td>
      <td>1.81</td>
    </tr>
  </tbody>
</table>
</div>




```python
date_today = dt.datetime.today().strftime("%m/%d/%Y")
```


```python
with plt.style.context("ggplot"):
    plt.scatter(selected_cities["Latitude"], selected_cities["Temperature"])
    plt.title("Latitude vs. Temperature" + " (" + date_today + ")"  )
    plt.ylabel("Temperature in F")
    plt.xlabel("Latitude")
    plt.savefig("Lat_vs_Temp.png")
```


![png](output_8_0.png)



```python
with plt.style.context("seaborn-darkgrid"):
    plt.scatter(selected_cities["Latitude"], selected_cities["Humidity"])
    plt.title("Latitude vs. Humidity (%)" + " (" + date_today + ")")
    plt.xlabel("Latitude")
    plt.ylabel("Humidity (%)")
    plt.savefig("Lat_vs_Humidity.png")
```


![png](output_9_0.png)



```python
with plt.style.context("Solarize_Light2"):
    plt.scatter(selected_cities["Latitude"], selected_cities["Cloudiness"])
    plt.title("Latitude vs. Cloudiness (%)" + " (" + date_today + ")")
    plt.xlabel("Latitude")
    plt.ylabel("Cloudiness (%)")
    plt.savefig("Lat_vs_Clouds.png")
```


![png](output_10_0.png)



```python
with plt.style.context("ggplot"):
    plt.scatter(selected_cities["Latitude"], selected_cities["Wind Speed"])
    plt.title("Latitude vs. Wind Speed (mph)" + " (" + date_today + ")")
    plt.ylabel("Wind Speed (mph)")
    plt.xlabel("Latitude")
    plt.savefig("Lat_vs_Wind.png")
```


![png](output_11_0.png)

