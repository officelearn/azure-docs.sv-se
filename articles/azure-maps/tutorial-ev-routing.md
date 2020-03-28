---
title: 'Självstudiekurs: Dirigera elfordon med hjälp av Azure Notebooks (Python) | Microsoft Azure Maps'
description: Dirigera elfordon med hjälp av Microsoft Azure Maps routning API:er och Azure Notebooks.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333826"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Självstudiekurs: Dirigera elfordon med hjälp av Azure Notebooks (Python)

Azure Maps är en portfölj av geospatiala tjänst-API:er som är inbyggda i Azure. Dessa API:er gör det möjligt för utvecklare, företag och ISV:er att utveckla platsmedvetna appar, IoT, mobilitet, logistik och lösningar för spårning av tillgångar. 

Azure Maps REST API:er kan anropas från språk som Python och R för att aktivera geospatial dataanalys och machine learning-scenarier. Azure Maps erbjuder en robust uppsättning [routnings-API:er](https://docs.microsoft.com/rest/api/maps/route) som tillåter användare att beräkna vägar mellan flera datapunkter. Beräkningarna baseras på olika förhållanden, till exempel fordonstyp eller nåbart område. 

I den här guiden går du hjälpa en förare vars elfordon batteri är låg. Föraren måste hitta närmaste möjliga laddningsstation från fordonets plats.

I den här kursen ska du:

> [!div class="checklist"]
> * Skapa och kör en Jupyter-anteckningsbok på [Azure-anteckningsböcker](https://docs.microsoft.com/azure/notebooks) i molnet.
> * Anropa AZURE Maps REST API:er i Python.
> * Sök efter ett räckviddsintervall baserat på elbilens förbrukningsmodell.
> * Sök efter laddstationer för elfordon inom räckhåll, eller isochrone.
> * Återge den räckviddsbara räckviddsgränsen och laddningsstationerna på en karta.
> * Hitta och visualisera en rutt till närmaste laddningsstation för elfordon baserat på körtid.


## <a name="prerequisites"></a>Krav 

För att slutföra den här självstudien måste du först skapa ett Azure Maps-konto och få din primära nyckel (prenumerationsnyckel). 

Om du vill skapa en Azure Maps-kontoprenumeration följer du instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps). Du behöver en Azure Maps-kontoprenumeration med prisnivån S1. 

Om du vill hämta den primära prenumerationsnyckeln för ditt konto följer du instruktionerna för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebook"></a>Skapa en Azure-anteckningsbok

Om du vill följa med den här självstudien måste du skapa ett Azure notebook-projekt och hämta och köra jupyter-anteckningsboksfilen. Anteckningsboksfilen innehåller Python-kod, som implementerar scenariot i den här självstudien. Så här skapar du ett Azure notebook-projekt och överför jupyter-anteckningsboksdokumentet till det:

1. Gå till [Azure-anteckningsböcker](https://notebooks.azure.com) och logga in. Mer information finns [i Snabbstart: Logga in och ange ett användar-ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. Högst upp på din offentliga profilsida väljer du **Mina projekt**.

    ![Knappen Mina projekt](./media/tutorial-ev-routing/myproject.png)

1. Välj **Nytt projekt**på sidan **Mina projekt** .
 
   ![Knappen Nytt projekt](./media/tutorial-ev-routing/create-project.png)

1. Ange ett projektnamn och projekt-ID i fönstret **Skapa nytt projekt.**
 
    ![Fönstret Skapa nytt projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Välj **Skapa**.

1. När projektet har skapats hämtar du [dokumentfilen för Jupyter-anteckningsboken](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) från [Azure Maps Jupyters anteckningsboksdatabas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Välj projektet i projektlistan på sidan **Mina projekt** och välj sedan **Ladda upp** för att ladda upp dokumentfilen för Jupyter-anteckningsboken. 

    ![ladda upp anteckningsbok](./media/tutorial-ev-routing/upload-notebook.png)

1. Ladda upp filen från datorn och välj sedan **Klar**.

1. När överföringen har slutförts visas filen på projektsidan. Dubbelklicka på filen för att öppna den som en Jupyter-anteckningsbok.

Försök att förstå de funktioner som implementeras i anteckningsboksfilen. Kör koden i anteckningsboksfilen en cell i taget. Du kan köra koden i varje cell genom att välja knappen **Kör** högst upp i anteckningsboksappen.

  ![Knappen Kör](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installera projektnivåpaket

Om du vill köra koden i anteckningsboken installerar du paket på projektnivå genom att göra följande:

1. Hämta [*filen requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) från [Azure Maps Jupyter-databasen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)för anteckningsboken och ladda sedan upp den till projektet.
1. Välj Projektinställningar på **instrumentpanelen**för projektet . 
1. Välj fliken **Miljö** i fönstret **Projektinställningar** och välj sedan **Lägg till**.
1. Gör följande under **Steg för miljöinställningar:**   
    a. I den första listrutan väljer du **Requirements.txt**.  
    b. Välj filen *requirements.txt* i den andra listrutan.  
    c. I den tredje listrutan väljer du **Python Version 3.6** som version.
1. Välj **Spara**.

    ![Installera paket](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Ladda de moduler och ramverk som krävs

Om du vill läsa in alla nödvändiga moduler och ramverk kör du följande skript.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Begär den räckviddsbara områdesgränsen

Ett paketleveransföretag har några elfordon i sin flotta. Under dagen måste elbilar laddas utan att behöva återvända till lagret. Varje gång den återstående laddningen sjunker till mindre än en timme söker du efter en uppsättning laddstationer som ligger inom ett nåbart intervall. I huvudsak söker du efter en laddningsstation när batteriet är svagt vid laddning. Och du får gränsinformationen för det utbudet av laddstationer. 

Eftersom företaget föredrar att använda rutter som kräver en balans mellan ekonomi och hastighet, är den begärda routeType *eco*. Följande skript anropar [Api:et hämta ruttområde](https://docs.microsoft.com/rest/api/maps/route/getrouterange) för routningstjänsten Azure Maps. Den använder parametrar för fordonets förbrukningsmodell. Skriptet tolkar sedan svaret för att skapa ett polygonobjekt i geojsonformatet, som representerar bilens maximala räckviddsområde.

Om du vill bestämma gränserna för elbilens räckvidd kör du skriptet i följande cell:

```python
subscriptionKey = "Your Azure Maps key"
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


# Get boundaries for the electric vehicle's reachable range.
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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Sök efter laddstationer för elfordon inom det räckhåll som kan nås

När du har fastställt räckviddsområdet (isochrone) för elfordonet kan du söka efter laddstationer inom det området. 

Följande skript anropar Azure Maps [Post Search Inside Geometry API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry). Den söker efter laddstationer för elfordon, inom gränserna för bilens maximala räckvidd. Sedan tolkar skriptet svaret på en rad nåbara platser.

Om du vill söka efter laddningsstationer för elfordon inom det räckhåll som kan nås kör du följande skript:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Ladda upp det räckviddsbara intervallet och laddningspunkterna till Azure Maps Data Service

På en karta vill du visualisera laddstationerna och gränsen för elbilens maximala räckvidd. Det gör du genom att överföra gränsdata och laddningsstationer data som geojson-objekt till Azure Maps Data Service. Använd [API:et för dataöverföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Om du vill överföra gräns- och laddningspunktsdata till Azure Maps Data Service kör du följande två celler:

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

# Upload the range data to Azure Maps Data Service.
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

# Upload the electric vehicle charging station data to Azure Maps Data Service.
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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Rendera laddstationerna och räckviddsområdet på en karta

När du har laddat upp data till datatjänsten ringer du tjänsten Azure Maps [Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage). Den här tjänsten används för att återge laddningspunkterna och den maximala räckviddsbara gränsen på den statiska kartbilden genom att köra följande skript:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
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

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![En karta som visar platsområdet](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Hitta den optimala laddningsstationen

Först vill du bestämma alla potentiella laddstationer inom det räckviddsbara området. Sedan vill du veta vilken av dem som kan nås på en minimal tid. 

Följande skript anropar Azure Maps [Matrix Routning API](https://docs.microsoft.com/rest/api/maps/route/postroutematrix). Den returnerar den angivna fordonsplatsen, restiden och avståndet till varje laddningsstation. Skriptet i nästa cell tolkar svaret för att hitta den närmaste laddningsstationen som kan nås med avseende på tid.

Om du vill hitta den närmaste laddningsstationen som kan nås på minst tid kör du skriptet i följande cell:

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

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Beräkna rutten till närmaste laddningsstation

Nu när du har hittat närmaste laddningsstation kan du ringa [API:et Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) för att begära den detaljerade vägen från elbilens aktuella plats till laddningsstationen.

Om du vill hämta vägen till laddningsstationen och tolka svaret för att skapa ett geojson-objekt som representerar rutten kör du skriptet i följande cell:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
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

## <a name="visualize-the-route"></a>Visualisera rutten

För att visualisera rutten överför du först vägdata som ett geojson-objekt till Azure Maps Data Service. Det gör du genom att använda [API:et](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)för dataöverföring av Azure Maps. Anropa sedan renderingstjänsten, [Hämta api för kartavbildning](https://docs.microsoft.com/rest/api/maps/render/getmapimage)för att återge rutten på kartan och visualisera den.

Om du vill hämta en bild för den renderade rutten på kartan kör du följande skript:

```python
# Upload the route data to Azure Maps Data Service.
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


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![En karta som visar rutten](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att anropa Azure Maps REST API:er direkt och visualisera Azure Maps-data med hjälp av Python.

Information om hur du utforskar Api:erna för Azure Maps som används i den här självstudien finns i:

* [Hämta ruttområde](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post Sök inuti geometri](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Data ladda upp](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render - Hämta kartbild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Matris för inläggsrutt](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Hämta vägbeskrivningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

En fullständig lista över AZURE Maps REST-API:er finns i [Azure Maps REST API:er](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Mer information om Azure-anteckningsböcker finns i [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
