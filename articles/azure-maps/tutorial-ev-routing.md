---
title: Routning av elektriskt fordon med Azure Notebooks (python) | Microsoft Docs
description: 'EV-routning med Azure Maps routnings-API: er och Azure Notebooks.'
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 30751bebd397b378924453987462c9e2b3b55ebf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803931"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Routning av elektriskt fordon med Azure Notebooks (python)

Azure Maps är en portfölj med geospatiala tjänst-API: er inbyggt i Azure som gör det möjligt för utvecklare, företag och ISV att skapa plats medvetna appar och IoT, mobilitet, logistik och till gångs spårnings lösningar. Azure Maps REST-API: er kan anropas från språk som python och R för att aktivera geospatiala data analyser och maskin inlärnings scenarier. Azure Maps erbjuder en robust uppsättning [routnings-API: er]([https://docs.microsoft.com/rest/api/maps/route) som gör att användarna kan beräkna vägar mellan flera data punkter baserat på olika villkor, till exempel fordons typ eller nåbart utrymme. I den här självstudien får vi gå igenom ett scenario för att hjälpa en elektrisk fordons driv rutin, vars fordon har låg batteri laddning, för att hitta den närmaste möjliga laddnings stationen med avseende på enhets tid.

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa och kör en Jupyter Notebook på [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) i molnet
> * Anropa Azure Maps REST-API: er i python
> * Sök efter ett nåbart intervall baserat på den elektriska fordons förbruknings modellen.
> * Sök efter elektriska fordons uttags stationer inom det nåbara intervallet (eller isochrone).
> * Återge gränsen för det nåbara intervallet och debitera stationerna på en karta.
> * Hitta och visualisera väg till närmaste elektriska fordons laddnings Station baserat på tid.


## <a name="prerequisites"></a>Förutsättningar 

För att slutföra stegen i den här självstudien måste du först skapa ett Azure Maps konto och hämta din primär nyckel (prenumerations nyckel). Följ anvisningarna i [Hantera konto](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) om du vill skapa en Azure Maps konto prenumeration med pris nivån S1 och följ stegen i [Hämta primär nyckel](./tutorial-search-location.md#getkey) för att hämta den primära prenumerations nyckeln för ditt konto.

## <a name="create-an-azure-notebook"></a>Skapa en Azure-anteckningsbok

För att följa med i den här själv studie kursen måste du skapa ett Azure Notebook-projekt och hämta och köra Jupyter Notebook-filen. Anteckningsbok-filen innehåller python-kod som implementerar scenariot i den här självstudien. Följ stegen nedan för att skapa ett Azure Notebook-projekt och ladda upp anteckningsbok-dokumentet för Jupyter till det.

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Välj **Mina projekt** överst på sidan från din offentliga profil sida.

    ![mitt projekt](./media/tutorial-ev-routing/myproject.png)

3. På sidan **Mina projekt** väljer du **nytt projekt**.
 
   ![Nytt projekt](./media/tutorial-ev-routing/create-project.png)

4. Ange följande information i popup-fönstret **Skapa nytt projekt** som visas och klicka på **skapa**:
    * Projektnamn
    * Projekt-ID
 
    ![Skapa projekt](./media/tutorial-ev-routing/create-project-window.png)

5. När projektet har skapats laddar du ned [dokument filen Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) från [Azure Maps Jupyter Notebook-lagringsplatsen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Välj ditt projekt från listan projekt på sidan **Mina projekt** och klicka på **överför** för att ladda upp anteckningsbok-filen för Jupyter Notebook. Ladda upp filen från datorn och klicka på **färdig**.

    ![Ladda upp antecknings bok](./media/tutorial-ev-routing/upload-notebook.png)

7. När överföringen är klar visas filen på projekt sidan. Klicka på anteckningsbok-filen för att öppna den som en Jupyter Notebook.

För att bättre förstå funktionerna som implementeras i Notebook-filen rekommenderar vi att du kör koden i antecknings boken en cell i taget. Du kan köra koden i varje cell genom att klicka på knappen **Kör** överst i appen Notebook.

  ![Kör](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installera på paket i projektet

Om du ska kunna köra koden i antecknings boken måste du installera paket på projekt nivå. Följ stegen nedan för att installera de nödvändiga paketen:

1. Hämta filen ["krav. txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) från [Azure Maps Jupyter Notebook-lagringsplatsen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) och ladda upp den till projektet.
2. På instrumentpanelen för projektet väljer **Projektinställningar**. 
3. I popup-fönstret som visas väljer du **fliken miljö**och väljer sedan **Lägg till**.
4. Under **miljö konfigurations steg**, 
    * I den första List rutan väljer du **krav. txt**.
    * I den andra List rutan väljer du filen "krav. txt".
    * I den tredje List kontrollen väljer du python version 3,6 som python-version.
7. Välj **Spara**.

    ![Installera paket](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Läs in nödvändiga moduler och ramverk

Kör följande skript för att läsa in alla nödvändiga moduler och ramverk.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Begäran om nåbar intervalls gränser

I vårt scenario har ett paket leverans företag några elektriska fordon i flottan. Under dagen måste elektriska fordon debiteras igen utan att behöva gå tillbaka till lagret. Varje gång som den aktuella återstående avgiften för det elektriska fordonet får mindre än en timme (ett elektriskt fordon har låg belastning) måste vi söka efter en uppsättning debiterings stationer som ligger inom det intervall som kan nås och hämtar gräns informationen för intervallet. Eftersom företaget föredrar att använda vägar som är balanserade efter ekonomi och hastighet, är det begärda routeType "eko". Följande skript anropar [API: t för att hämta väg intervall](https://docs.microsoft.com/rest/api/maps/route/getrouterange) för Azure Maps-routningstjänsten med parametrar för fordonets förbruknings modell och tolkar svaret för att skapa ett polygon-objekt av det interjson-format som representerar Car: s maximalt nåbara intervall .

Kör skriptet i cellen nedan för att få gränser för det tillgängliga intervallet för det elektriska fordonet.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Sök efter elektriska fordons laddnings stationer inom räckhåll

När vi har det tillgängliga intervallet (isochrone) för det elektriska fordonet kan vi söka efter debitering av stationer i det intervallet. Följande skript anropar Azure Maps [post-sökning i Geometry-API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) för att söka efter elektriska fordons uttags stationer inom gränserna för Car: s maximalt nåbara intervall och parsar sedan svaret till en matris med platser som kan nås.

Kör följande skript för att söka efter elektriska fordons uttags stationer inom räckhåll.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Överför nåbart intervall och debiterar punkter för Azure Maps data tjänst

För att visualisera debiterings stationerna och gränsen för det högsta räckvidds intervallet för det elektriska fordonet på kartan, måste vi ladda upp gräns data och debitera Stations data som JSON-objekt till Azure Maps data tjänsten med hjälp av [API: et för data uppladdning ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Kör följande två celler för att överföra avgränsnings-och avgifts plats data till Azure Maps data service.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Rendera debiterings stationer och nåbart intervall på kartan

När vi har överfört data till data tjänsten kommer vi nu att köra följande skript för att anropa tjänsten Azure Maps [Hämta avbildnings tjänst](https://docs.microsoft.com/rest/api/maps/render/getmapimage) för att återge debiterings punkterna och den maximalt tillgängliga gränsen på den statiska kart bilden.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![plats intervall](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Hitta den optimala laddnings stationen att stoppa

När vi har alla potentiella avgifts stationer inom det nåbara intervallet vill vi veta vilken av stationerna som kan nås under den minsta tiden. Följande skript anropar Azure Maps [mat ris routnings-API:](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) t för den givna fordons platsen res tiden och avståndet till varje avgift Stations Stations plats. Skriptet i nästa cell, parsar svaret för att få plats för den närmaste tillgängliga avgifts stationen med avseende på tid.

Kör följande cell för att hitta den närmaste tillgängliga avgifts station som kan nås inom kort tid.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Beräkna väg till närmaste avgifts Station

Nu när vi har hittat den närmaste laddnings stationen kommer vi att anropa [API: et för att hämta väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) för att begära den detaljerade vägen från det elektriska fordonets aktuella plats till laddnings stationen.

Kör skriptet i följande cell för att hämta vägen och parsa svaret för att skapa ett interjson-objekt som representerar vägen.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualisera vägen

För att visualisera vägen laddar vi först upp flödes data som ett JSON-objekt i Azure Maps data tjänsten med hjälp av Azure Maps [data överförings-API: et](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Anropa sedan rendering-tjänsten, [Hämta bild-API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) för att rendera vägen på kartan och visualisera den.

Kör följande skript för att hämta en avbildning för den återgivna vägen på kartan.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![route](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du anropar Azure Maps REST-API: er direkt och visualiserar Azure Maps data med python.

Information om Azure Maps-API: er som används i den här självstudien finns i:

* [Hämta flödes intervall](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Publicera sökning i geometri](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Data uppladdning](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Rendera-Hämta kart bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Publicera väg mat ris](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Hämta väg riktningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

En fullständig lista över Azure Maps REST API: er finns i:

* [Azure Maps REST-API: er](https://docs.microsoft.com/azure/azure-maps/#reference)

Mer information om Azure Notebooks finns i:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)