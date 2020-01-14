# Geography
Analyzing data from different countries around the world.

## Data

The data are two json files that were both downloaded from the internet directly in the Jupyter notebook using the `download` function listed below. The countries dataset was downloaded as a json file while the capitals dataset was scaped from an html file using Beautiful Soup. As for the datasets themselves, Countries consists of 174 countries with data such as Area, Birth Rate, Coastline, Continent, Country, Death Rate, GDP Per Capita, Infant Mortality, Literacy, Net Migration, Phones, Population, and Region. Capitals has 247 countries in the dataset but I will only be looking at the countries listed in the countries dataset, and it contains data such as Country Name, Capital Name, Capital Longitude, and Capital Latitude. 

The first five entries in the countries dataset are:

Area | Birth Rate	| Coastline	| Continent | Country | Death Rate | GDP Per Capita | Infant Mortality | Literacy | Net Migration | Phones | Population | Region
---- | ---------- | --------- | --------- | ------- | ---------- | -------------- | ---------------- | -------- | ------------- | ------ | ---------- | ------
756,950 | 15.23 | 0.85	| South America	| Chile	| 5.81 | 9,900	| 8.80 | 96,2	| 0.00 | 213,0 | 16,134,219 | LATIN AMER. & CARIB
665 | 17.80	| 24.21	| Asia | Bahrain | 4.14 | 16,900	| 17.27	| 89,1 | 1.05	| 281,3	| 698,585 | NEAR EAST
22,966 | 28.84	| 1.68 | North America | Belize | 5.72 | 4,900 | 25.69 | 94,1 | 0.00 | 115,7 | 287,730 | LATIN AMER. & CARIB
582,650 | 39.72 | 0.09 | Africa | Kenya | 14.02 | 1,000 | 61.47 | 85,1 | -0.10 | 8,1 | 34,707,817 | SUB-SAHARAN AFRICA
11,854 | 33.05 | 3.12 | Australia | Marshall Islands | 4.78 | 1,600 | 29.45 | 93,7 | -6.04 | 91,2 | 60,422 | OCEANIA

The first five entries in the capitals dataset are:

Country Name | Capital Name | Capital Latitude | Capital Longitude
------------ | ------------ | ---------------- | -----------------
Afghanistan | Kabul | 34.516667 | 69.183333
Albania | Tirana | 41.316667 | 19.816667
Algeria | Algiers | 36.750000 | 3.050000
Anguilla | The Valley | 18.216667 | -63.050000
Argentina | Buenos Aires | -34.583333 | -58.666667

## Functions

Downloads file from url if file isn't already created:

```python
def download(filename, url):
    # We do not download again if the file already exists
    if os.path.exists(filename):
        return (str(filename) + " already exists!")

    else:
        r = requests.get(url)
        data = r.text
        
        f = open(filename, "w", encoding="utf-8")
        f.write(data)
        f.close()
    

    return (str(filename) + " created!")
```

Uses haversine formula to calculate the distance between two countries:
```python
def haversine(lon1, lat1, lon2, lat2):
    """
    Calculate the great circle distance between two points 
    on the earth (specified in decimal degrees)
    """
    # convert decimal degrees to radians 
    lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])

    # haversine formula 
    dlon = lon2 - lon1 
    dlat = lat2 - lat1 
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a)) 
    r = 3956
    return c * r
```

## Data Analysis

The total area of the countries in the dataset is 127,170,843 miles

The capital of Cuba and country with the capital of Tbilisi can be found with similar methods:

```python
cuba_series = capitals[capitals['Country Name'] == 'Cuba']
next(iter(cuba_series['Capital Name']))
```

The program works by first returning the series of the country where the condition is true and then iterating through the series to return the data I need.

### Countries with the Southern and Northern Most Capitals

In order to find the southern and northern most capitals, I sort the capitals by longitude. For northern most I just changed the sort so that reverse is true. Taking the seven most southern capitals and five most northern.

```python
southern_most = capitals.sort_values(by='Capital Latitude')[0:7]
```

Southern:
* New Zealand
* Australia
* Uruguay
* Argentina
* Chile
* Lesotho
* Swaziland

Northern:
* Iceland
* Finland
* Norway
* Estonia
* Sweden

### Birth and Death Rates

Using the describe function from Pandas we can get statistics info for the birth and death rates for the entire dataset.

Stats | Birth Rate | Death Rate
-----| ---------- | ----------
count | 174.000000 | 174.000000
mean | 22.463851 | 9.625172
std | 11.278992 | 5.187143
min | 8.250000 | 2.410000
25% | 12.597500 | 6.027500
50% | 20.010000 | 8.230000
75% | 29.860000 | 11.715000
max | 50.730000 | 29.740000

The average birth rate among all the countries in the dataset is 22.463851 and the average death rate is 9.625172. Showing the gap that causes the rising global population.

### Phone and Literacy Statistics

Looking at the number of phones per capita and the literacy rates for the dataset. These two categories in the dataset come in a format with commas in the place of periods for some countries. In order for Pandas to work with this data, it has to be converted into a float. Thus, the first part of getting the statistics for these two categories is replacing the commas with periods and setting the type as float. Then I can use the describe method in Pandas to get the statistics data I need.

Stats | Literacy | Phones
----- | -------- | ------
count | 174.000000 | 174.000000
mean | 82.362069 | 215.770690
std | 20.003166 | 218.713811
min | 17.600000 | 1.300000
25% | 70.100000 | 32.000000
50% | 92.100000 | 140.350000
75% | 98.000000 | 331.750000
max | 100.000000 | 1035.600000

The average literacy rate is 82.362069 which is much lower than the 50th percentile in the dataset at 92.100000. Conversely, the average number of phones per capita is higher than the 50th percentile of 140.350000 at 215.770690.

### Landlocked Countries

To find landlocked countries I found countries whose `coastline` was equal to zero:

```python
landlocked = countries[countries['coastline'] == 0]
```

I wanted to find the largest landlocked country in both Europe and Asia. This was done by finding where `continent` is equal to either Asia or Europe. From this database of landlocked countries in a certain continent, I can find the largest country based on area using the `pd.max()` function from Pandas.

The largest landlocked country from Europe is Belarus. The largest landlocked country from Asia is Kazakhstan. However, looking at the most populated landlocked country in Asia is Afghanistan. 

### Distances

The next few analysises will being completed using the haversine formula and the `haversine` function listed above.

Finding the distance from Japan to France can be done using the latitude and longitude from both locations as so:

```python
japan_series = capitals[capitals['Country Name'] == 'Japan']
japan_lat = next(iter(japan_series['Capital Latitude']))
japan_lon = next(iter(japan_series['Capital Longitude']))
```
France is found the same way just replacing the condition with 'France'. Then the distance can be found inputing those two points into the haversine function. The distance from Japan to France is then 6,032.33 miles

Then I wanted to compile data for the distances between each country in Oceania using the code below:
```python
ocea_list = []
aust_series = countries[countries['continent'] == 'Australia']
for c in aust_series['country']:
    ocea_list.append(c)
    
df_list = []
dfs_list = []

for c in ocea_list:
    df_list.append(capitals[capitals['Country Name'] == c])
for c in df_list:
    ocea_dict = {}
    lat = next(iter(c['Capital Latitude']))
    lon = next(iter(c['Capital Longitude']))
    name = next(iter(c['Country Name']))
    ocea_dict['Country Name'] = name
    ocea_dict['Longitude'] = lon
    ocea_dict['Latitude'] = lat
    dfs_list.append(ocea_dict)

ocea_df = DataFrame(dfs_list)
ocea_dist = {}
for country in ocea_df['Country Name']:
    ocea_dist[country] = []
    country_series = ocea_df[ocea_df['Country Name'] == country]
    country_lat = next(iter(country_series['Latitude']))
    country_lon = next(iter(country_series['Longitude']))
    for c in ocea_df['Country Name']:
        if c == country:
            ocea_dist[country].append(None)
        else:
            c_series = ocea_df[ocea_df['Country Name'] == c]
            c_lat = next(iter(c_series['Latitude']))
            c_lon = next(iter(c_series['Longitude']))
            ocea_dist[country].append(haversine(country_lon, country_lat, c_lon, c_lat))

australia_df = DataFrame.from_dict(ocea_dist, orient = 'index', columns = ocea_list)
australia_df
```

The code starts by identifying each country in the database in the 'Australia' continent and creating a database of those countries and also adding each country name from those series to a list to be used as keys. Then the program iterates through the countries in the df_list to get the name, latitude, and longitude of each country and creating a dict mapping the categories to their values. These dicts are then appended to the dfs_list which can be converted into a dataframe, ocea_df. Then the code goes about calculating the distance from the country being iterated to every other country in oceania and appending it to a list of distances for each country, if the country being iterated is the same as the country the distance is being calculated to then the program appends None to the list. The last two lines of the code are just formatting the dataframe and outputing it in Jupyter Notebook.

The dataframe is:

Country | Marshall Islands | French Polynesia | New Zealand | Vanuatu | Palau | Papua New Guinea | Guam | Samoa | New Caledonia | Australia | Fiji | Tonga
------- | ---------------- | ---------------- | ----------- | ------- | ----- | ---------------- | ---- | ----- | ------------- | --------- | ---- | -----
Marshall Islands | NaN | 3,159.856137 | 3,348.725378 | 1,727.328621 | 2,516.301728 | 2,019.144119 | 1,861.584428 | 1.849.150692 | 2,054.905160 | 3,263.901948 | 1,806.972599 | 2,151.129410
French Polynesia | 3,159.856137 | NaN | 2,668.807328 | 2,765.339382 | 5,447.018761 | 4,265.757856 | 4,961.131713 | 1,496.953816 | 2,864.497308 | 3,920.570216 | 2,102.135722 | 1,686.539411
New Zealand | 3,348.725378 | 2,668.807328 | NaN | 1,671.641167 | 4,218.188412 | 2,767.607095 | 4,239.314912 | 2,062.548020 | 1,400.483623 | 1,445.147895 | 1,613.916992 | 1,510.219450
Vanuatu | 1,727.328621 | 2,765.339382 | 1,671.641167 | NaN | 2,882.041892 | 1,527.495923 | 2,687.395771 | 1,349.904435 | 335.602971 | 1,687.810709 | 663.967196 | 1,098.016273
Palau | 2,516.301728 | 5,447.018761 | 4,218.188412 | 2,882.041892 | NaN | 1,453.306058 | 800.243081 | 3,956.305436 | 2,974.892838 | 3,099.023227 | 3,468.037102 | 3,931.843110
Papua New Guinea | 2,019.144119 | 4,265.757856 | 2,767.607095 | 1,527.495923 | 1,453.306058 | NaN | 1,591.171595 | 2,789.123270 | 1,552.708901 | 1,786.749660 | 2,174.712829 | 2,623.742354
Guam | 1,861.584428 | 4,961.131713 | 4,239.314912 | 2,687.395771 | 800.243081 | 1,591.171595 | NaN | 3,520.883355 | 2,871.868601 | 3,377.103870 | 3,165.570014 | 3,617.512808
Samoa | 1,849.150692 | 1,496.953816 | 2,062.548020 | 1,349.904435 | 3,956.305436 | 2,789.123270 | 3,520.883355 | NaN | 1,542.152644 | 2,840.091065 | 716.316054 | 553.393188
New Caledonia | 2,054.905160 | 2,864.497308 | 1,400.483623 | 335.602971 | 2,974.892838 | 1,552.708901 | 2,871.868601 | 1,542.152644 | NaN | 1,375.924712 | 825.890876 | 1,179.069567
Australia | 3,263.901948 | 3,920.570216 | 1,445.147895 | 1,687.810709 | 3,099.023227 | 1,786.749660 | 3,377.103870 | 2,840.091065 | 1,375.924712 | NaN | 2,145.768971 | 2,362.766922
Fiji | 1,806.972599 | 2,102.135722 | 1,613.916992 | 663.967196 | 3,468.037102 | 2,174.712829 | 3,165.570014 | 716.316054 | 825.890876 | 2,145.768971 | NaN | 463.843872
Tonga | 2,151.129410 | 1,686.539411 | 1,510.219450 | 1,098.016273 | 3,931.843110 | 2,623.742354 | 3,617.512808 | 553.393188 | 1,179.069567 | 2,362.766922 | 463.843872 | NaN

Each of the distances in the dataframe are calculated in miles

Data based on the the dataframe:
* Most central country in Oceania: Vanuatu
* Least central country in Oceania: French Polynesia

Each countries nearest neighbor:

Country | Nearest | Distance
------- | ------- | --------
Marshall Islands | Vanuatu | 1,727.328621
French Polynesia | Samoa | 1,496.953816
New Zealand | New Caledonia | 1,400.483623
Vanuatu | New Caledonia | 335.602971
Palau | Guam | 800.243081
Papua New Guinea | Palau | 1,453.306058
Guam | Palau | 800.243081
Samoa | Tonga | 553.393188
New Caledonia | Vanuatu | 335.602971
Australia | New Caledonia | 1,375.924712
Fiji | Tonga | 463.843872
Tonga | Fiji | 463.843872

Each countries furthest neighbor:

Country | Furthest | Distance
------- | -------- | --------
Marshall Islands | New Zealand | 3,348.725378
French Polynesia | Palau | 5,447.018761
New Zealand | Guam | 4,239.314912
Vanuatu | Palau | 2,882.041892
Palau | French Polynesia | 5,447.018761
Papua New Guinea | French Polynesia | 4,265.757856
Guam | French Polynesia | 4,961.131713
Samoa | Palau | 3,956.305436
New Caledonia | Palau | 2,974.892838
Australia | French Polynesia | 3,920.570216
Fiji | Palau | 3,468.037102
Tonga | Palau | 3,931.843110

### SQL Analysis

Pandas allows me to run SQL Queries on the Dataframes using `pd.read_sql`

I found the number of countries in each continent using the query: ```"SELECT continent, COUNT(country) AS `number of countries` FROM countries GROUP BY continent ORDER BY COUNT(country) ASC"``` which lists them in ascending order. 

Giving me the result:
Continent | Number of Countries
--------- | -------------------
Australia | 12
South America | 12
North America | 24
Asia | 38
Europe | 41
Africa | 47

As shown in the data, Africa is the continent with the most countries at 47 and Australia and South America both have the least countries at 12.

This chart can be shown graphically as:

<img src="Graph 1.png" width="400">

The total populations each continent can be found using the query ```"SELECT continent, CAST(SUM(population) AS float)/1000000 AS `total population (million)` FROM countries GROUP BY continent ORDER BY SUM(population) DESC"``` showing the results in descending order.

The results are: 

Continent | Total Population (Millions)
--------- | ---------------------------
Asia | 3,739.902863
Africa | 807.299195
Europe | 792.053486
North America | 515.041558
South America | 375.441666
Australia | 32.163025

Shown graphically as: 

<img src="Graph 2.png" width="400">

Asia has by far the most people living there with most continents not even coming close to its total population. This can be attributed to China and India who both have populations of over one billion people.

Looking at the GDP data for all the countries in the dataset, the top ten countries in terms of GDP are:

The data can be organized using the query: 
``` "SELECT country, CAST(population*`gdp-per-capita` AS float)/1000000000 AS `real GDP (billion)` FROM countries ORDER BY `real GDP (billion)` DESC LIMIT 10"```

Country | Real GDP (Billions)
------- | -------------------
United States | 11,281.191327
China | 6,569.868565
Japan | 3,594.473830
India | 3,176.520785
Germany | 2,274.855452
France | 1,680.181354
United Kingdom | 1,678.873538
Italy | 1,552.164690
Brazil | 1,429.394525
Russia | 1,271.752506

<img src="Graph 3.png" width="400">

The data shows that the United States has a significantly higher GDP than most of the other countries in the top ten, and looking at the graph we see that the United States nearly has an equal GDP to all the countries not in the top ten combined.

Analyzing birth and death rates for each country. The 15 countries with the largest gap between their birth and death rates are:

Country | Birth Rate | Death Rate
------- | ---------- | ----------
Uganda | 47.35 | 12.24
Yemen | 42.89 | 8.30
Mali | 49.82 | 16.89
Oman | 36.24 | 3.81
Madagascar | 41.41 | 11.11
Burkina Faso | 45.62 | 15.60
Niger | 50.73 | 20.91
Chad | 45.73 | 16.38
Mauritania | 40.99 | 12.16
Burundi | 42.22 | 13.46
Comoros | 36.93 | 8.20
Somalia | 45.13 | 16.63
Marshall Islands | 33.05 | 4.78
Maldives | 34.81 | 7.06
Togo | 37.01 | 9.83

<img src="Graph 4.png" width="400">

Uganda has the highest gap between their birth and death rates with a difference of 35.11.


I also looked at graph data for the relationship between birth and death rate. The largest density of countries in the graph are found at the bottom of the graph with a death rate between 5 and 15 and a birth rate between 10 and 15. A large number of countries have a death rate that is larger than its birth rate. 

<img src="Graph 5.png" width="400">

In the graph there are 6 outliers that don't fit the curve of data. These countries are:

Country | Birth Rate | Death Rate
------- | ---------- | ----------
Namibia | 24.32 | 18.86
Zimbabwe | 28.01 | 21.84
South Africa | 18.20 | 22.00
Lesotho | 24.75 | 28.71
Botswana | 23.08 | 29.50
Swaziland | 27.41 | 29.74

The correlation between birth and death rate is then: 0.39650861177525115

Comparing GDP to Birth and Death rates shows fairly similar graphs:

Relationship between GDP per Capita and Death Rates:

<img src="Graph 6.png" width="400">

Relationship between GDP per Capita and Birth Rates:

<img src="Graph 7.png" width="400">

Finally looking at the Inverse Birth Rate vs GDP per Capita:

<img src="Graph 8.png" width="400">

with the red line representing the linear line of best fit.
