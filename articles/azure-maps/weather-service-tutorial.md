---
title: 'Självstudie: koppla sensor data med väder prognos data med hjälp av Azure Notebooks (python) | Microsoft Docs'
description: 'Självstudie: den här själv studie kursen visar hur du kopplar sensor data med väder prognos data från Azure Maps väder tjänsten med Azure Notebooks (python).'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 809c188dc37aba64de27e89e38acd8692c7de032
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613575"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Självstudie: koppla sensor data med väder prognos data med hjälp av Azure Notebooks (python)

Vind styrkan är en alternativ energi källa för fossila bränslen för att bekämpa klimat förändringar. Eftersom själva lindningen inte är konsekvent måste du bygga ML-modeller (maskin inlärning) för att förutsäga energi kapaciteten för att möta El efter frågan och se till att rutnätet blir stabilt. I den här självstudien går vi igenom hur Azure Maps information om väder prognoser kan kombineras med demo data uppsättningar med väder läsningar. Väder prognos data begärs genom att anropa Azure Maps väder tjänsten.

I den här kursen ska du:

> [!div class="checklist"]
> * Arbeta med datafiler i [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) i molnet.
> * Läs in demo data från fil.
> * Anropa Azure Maps REST-API: er i python.
> * Återge plats data på kartan.
> * Förbättra demonstrations data med Azure Maps [dagliga data för dagliga prognoser](https://aka.ms/AzureMapsWeatherDailyForecast) .
> * Rita prognos data i grafer.


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du först:

1. Skapa en Azure Maps konto prenumeration på S0-pris nivån genom att följa instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Hämta den primära prenumerations nyckeln för ditt konto genom att följa anvisningarna i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

Om du vill bekanta dig med Azures antecknings böcker och vet hur du kommer igång, följer du anvisningarna [skapa en Azure-anteckningsbok](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Jupyter Notebook-filen för det här projektet kan laddas ned från [väder Kartornas Jupyter Notebook-lagringsplats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Läs in de nödvändiga modulerna och ramverken

Kör följande skript för att läsa in alla nödvändiga moduler och ramverk:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Importera väder data

För den här självstudien kommer vi att använda väder data läsningar från sensorer som är installerade på fyra olika lindnings turbiner. Exempel data består av 30 dagars väder läsningar som samlats in från väder Data Center nära varje turbin-plats. Demonstrations data innehåller data avläsningar för temperatur, vridnings hastighet och riktning. Du kan ladda ned demonstrations data [härifrån](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skriptet nedan importerar demonstrations data till Azure-anteckningsboken.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Begär dagliga prognos data

I vårt exempel scenario skulle vi vilja begära dagliga prognoser för varje sensor plats. Följande skript anropar [API: et för dagliga prognoser](https://aka.ms/AzureMapsWeatherDailyForecast) i Azure Maps väderleks tjänsten för att få dagliga väder prognoser för varje lindnings turbin, under de närmaste 15 dagarna från dagens datum.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Skriptet nedan återger turbin-platserna på kartan genom att anropa Azure Maps [Hämta avbildnings tjänsten](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbin-platser](./media/weather-service-tutorial/location-map.png)


För att utöka demonstrations data med prognos data kommer vi att gruppera prognos data med demonstrations data baserat på Stations-ID: t för väder data centret.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

I följande tabell visas de kombinerade historiska och prognos data för en av turbin-platserna.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![grupperade data](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Rita prognos data

För att se hur vridnings hastigheten och riktningen ändras under de närmaste 15 dagarna kommer vi att rita upp de prognostiserade värdena mot de dagar som de är prognostiserade.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Diagrammen nedan visualiserar prognos data för att ändra vridnings hastigheten (till vänster graf) och riktning (höger graf) under de närmaste 15 dagarna från den dag då data begärs.

<center>

![vridnings hastighet Rita](./media/weather-service-tutorial/speed-date-plot.png) ![vridnings riktnings ritytan](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig att anropa Azure Maps REST-API: er för att få information om väder prognoser och visualisera data i grafer.

Om du vill veta mer om hur du anropar Azure Maps REST-API: er i Azure Notebooks, se [EV-routning med Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Om du vill utforska de Azure Maps-API: er som används i den här självstudien, se:

* [Daglig prognos](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Rendera-Hämta kart bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

En fullständig lista över Azure Maps REST API: er finns i [Azure Maps REST API: er](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Mer information om Azure Notebooks finns i [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
