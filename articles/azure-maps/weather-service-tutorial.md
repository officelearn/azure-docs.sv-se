---
title: 'Självstudiekurs: Gå med sensordata med väderprognosdata med hjälp av Azure Notebooks(Python) | Microsoft Azure Maps'
description: Den här självstudien visar hur du ansluter sensordata med väderprognosdata från Microsoft Azure Maps Weather Service med Azure Notebooks(Python).
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333485"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Självstudiekurs: Gå med sensordata med väderprognosdata med hjälp av Azure Notebooks (Python)

Vindkraft är en alternativ energikälla för fossila bränslen för att bekämpa klimatförändringarna. Eftersom vinden inte är konsekvent av naturen, vindkraft operatörer måste bygga maskininlärning (ML) modeller för att förutsäga vindkraftkapacitet. Denna förutsägelse är nödvändig för att möta efterfrågan på el och säkerställa nätets stabilitet. I den här självstudien går vi igenom hur Azure Maps väderprognosdata kombineras med demodata för väderavläsningar. Väderprognosdata begärs genom att anropa Azure Maps Weather Service.

I den här kursen ska du:

> [!div class="checklist"]
> * Arbeta med datafiler i [Azure-anteckningsböcker](https://docs.microsoft.com/azure/notebooks) i molnet.
> * Läs in demodata från filen.
> * Anropa AZURE Maps REST API:er i Python.
> * Återge platsdata på kartan.
> * Berika demodata med azure maps [dagliga prognosväderdata.](https://aka.ms/AzureMapsWeatherDailyForecast)
> * Rita prognosdata i diagram.


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du först:

1. Skapa en Azure Maps-kontoprenumeration på prisnivån S0 genom att följa instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Hämta den primära prenumerationsnyckeln för ditt konto, följ instruktionerna för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

Om du vill bekanta dig med Azure-anteckningsböcker och veta hur du kommer igång följer du instruktionerna [Skapa en Azure Notebook](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Den Jupyter anteckningsboken arkivera för den här projekt kanna bli dataöverföring från [weather maps Jupyter anteckningsboken arkivet](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Ladda de moduler och ramverk som krävs

Om du vill läsa in alla nödvändiga moduler och ramverk kör du följande skript:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importera väderdata

Av hänsyn till den här guiden använder vi väderdataavläsningar från sensorer installerade på fyra olika vindkraftverk. Exempeldata består av 30 dagars väderavläsningar. Dessa avläsningar samlas in från väderdatacentra nära varje turbinplats. Demodata innehåller dataavläsningar för temperatur, vindhastighet och riktning. Du kan ladda ner demodata [härifrån](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skriptet nedan importerar demodata till Azure Notebook.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Begär dagliga prognosdata

I vårt scenario vill vi begära daglig prognos för varje sensorplats. Följande skript anropar [daily forecast-API:et](https://aka.ms/AzureMapsWeatherDailyForecast) för vädertjänsten Azure Maps. Detta API returnerar väderprognos för varje vindkraftverk, för de kommande 15 dagarna från det aktuella datumet.


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

Skriptet nedan återger turbinplatserna på kartan genom att anropa tjänsten Azure Maps [Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

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

![Turbinplatser](./media/weather-service-tutorial/location-map.png)


Vi grupperar prognosdata med demodata baserat på stations-ID. Stations-ID:t är till för väderdatacentret. Den här gruppningen utökar demodata med prognosdata.

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

I följande tabell visas kombinerade historiska data och prognosdata för en av turbinplatserna.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Grupperade data](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Prognosdata för diagram

Vi ritar de prognostiserade värdena mot de dagar som de prognostiseras för. Denna tomt gör det möjligt för oss att se hastighet och riktning förändringar av vinden för de kommande 15 dagarna.

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

Diagrammen nedan visualiserar prognosdata. För byte av vindhastighet, se den vänstra grafen. För förändring i vindriktning, se rätt diagram. Dessa data är förutsägelse för nästa 15 dagar från den dag då data begärs.

<center>

![Vindhastighet](./media/weather-service-tutorial/speed-date-plot.png) ![tomt Vindriktning tomt](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Nästa steg

I den här självstudien som du lärde dig, hur du anropar Azure Maps REST API:er för att hämta väderprognosdata. Du har också lärt dig hur du visualiserar data i diagram.

Mer information om hur du anropar AZURE Maps REST-API:er i Azure Notebooks finns i [EV-routning med Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Information om hur du utforskar Api:erna för Azure Maps som används i den här självstudien finns i:

* [Daglig prognos](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Hämta kartbild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

En fullständig lista över AZURE Maps REST-API:er finns i [Azure Maps REST API:er](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Mer information om Azure-anteckningsböcker finns i [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
