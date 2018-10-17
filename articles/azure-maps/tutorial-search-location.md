---
title: Söka med Azure Maps | Microsoft Docs
description: Söka efter orienteringspunkter i närheten med hjälp av Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 761674c5839f0513532355116db07604f9e9d9dc
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816828"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Söka efter orienteringspunkter i närheten med hjälp av Azure Maps

Den här självstudiekursen visar hur du skapar ett konto med Azure Maps och sedan använder API:er för Maps för att söka efter en orienteringspunkt. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Maps-konto
> * Hämta primärnyckeln för ditt Maps-konto
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Maps-söktjänsten för att hitta orienteringspunkter i närheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Skapa ett konto med Azure Maps

Skapa ett nytt Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **Maps** i rutan *Sök på Marketplace*.
3. Bland *resultaten* väljer du **Maps**. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * *Namn* för ditt nya konto.
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * Välj en *Resursgruppsplats*.
    * Läs *licensen* och *sekretesspolicyn* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

    ![Skapa Maps-konto i portalen](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du nyckeln som gör att du kan fråga API:et Kartkontroll.

1. Öppna ditt Maps-konto i portalen.
2. I fönstret Inställningar, väljer du **Nycklar**.
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

    ![Hämta primärnyckel i portalen](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta

API:et Kartkontroll är ett praktiskt klientbiblioteket som hjälper dig att enkelt integrera Maps i ditt webbprogram. Det döljer komplexiteten i de rena REST-tjänstanropen och ökar produktiviteten med formateringsbara och anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapSearch.html**.
2. Lägg till följande HTML-komponenter i filen:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera segmentet *script* som lagts till i HTML-filens *brödtext*. Det här segmentet innehåller infogad JavaScript-kod för att komma åt API:et i Azure Maps.

3. Lägg till följande JavaScript-kod i HTML-filens *script*-block. Ersätt strängen **\<din kontonyckel\>** med primärnyckeln som du kopierade från Maps-kontot.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Det här segmentet initierar API:et Kartkontroll för din Azure Maps-kontonyckel. **Atlas** är det namnområde som innehåller API:et och relaterade visuella komponenter. **Atlas.Map** ger kontroll över en visuell och interaktiv webbkarta.

4. Spara dina ändringar i filen och öppna HTML-sidan i en webbläsare. Det här är den mest grundläggande mappningen du kan göra genom att anropa **atlas.map** med hjälp av din kontonyckel.

   ![Visa kartan](./media/tutorial-search-location/basic-map.png)

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Lägga till sökfunktioner

Det här avsnittet visar hur du API:et Maps Search för att hitta en orienteringspunkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. **Tjänstemodulen** som beskrivs nedan kan användas till att söka efter en plats med hjälp av API:et för kartsökning.

### <a name="service-module"></a>Tjänstmodul

1. Lägg till ett nytt lager på kartan för att visa sökresultaten. Lägg till följande Javascript-kod till skriptblocket efter koden som initierar kartan.

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    ```

2. Lägg till följande Javascript-kod till skriptblocket efter koden som initierar kartan, för att initiera klienttjänsten.

    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. Alla funktioner på kartan ska läsas in efter att kartan har lästs in. Du kan se till att det sker genom att lägga till alla kartfunktioner i kartans eventListener-block. Lägg till följande kodrader för att lägga till en [eventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) på kartan för att säkerställa att kartan läses in helt innan du lägger till funktioner.
    
    ```JavaScript
         map.addEventListener("load", function() {
         });
    ```

4. Lägg till följande skriptblock **i kartinläsningsfunktionen eventListener** för att skapa frågan. Den använder det enkla söknings-API:et i Search Service, som kallas Fuzzy Search. Fuzzy Search-tjänsten hanterar de flesta diffusa indata, exempelvis olika kombinationer av adress och orienteringspunkt. Du söker efter närliggande bensinstationer inom angiven radie. Svaret tolkas sedan till GeoJSON-format och konverteras till punktfunktioner. De läggs till på kartan som knappnålar. Den sista delen av skriptet lägger till kameragränser för kartan med hjälp av kartans [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest)-egenskap.

    ```JavaScript

            // Execute a POI search query then add pins to the map for each result once a response is received
            client.search.getSearchFuzzy("gasoline station", {
            lat: 47.6292,
            lon: -122.2337,
            radius: 100000
            }).then(response => {
       
            // Parse the response into GeoJSON 
            var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);

            // Create the point features that will be added to the map as pins
            var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => {
               var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat];
               return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                name: poiResult.properties.poi.name,
                address: poiResult.properties.address.freeformAddress,
                position: poiPosition[1] + ", " + poiPosition[0]
               });
            });

            // Add pins to the map for each POI
            map.addPins(searchPins, {
               name: searchLayerName,
               cluster: false, 
               icon: "pin-round-darkblue" 
            });

            // Set the camera bounds
            map.setCameraBounds({
               bounds: geojsonResponse.getGeoJsonResults().bbox,
               padding: 50
            );
        });
    ```
5. Spara filen **MapSearch.html** och uppdatera webbläsaren. Du bör nu se att kartan är centrerad i Seattle med blå kartnålar som markerar platserna för områdets bensinstationer.

   ![Visa kartan med sökresultat](./media/tutorial-search-location/pins-map.png)

6. Du kan visa rådata som kartan återger genom att ange följande HTTP-förfrågan i webbläsaren. Ersätt \<din kontonyckel\> med primärnyckeln.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

I det här läget kan MapSearch-sidan visa orienteringspunkterna som returneras från en fuzzy-sökfråga. Lägg till vissa interaktiva funktioner och mer information om platser.

## <a name="add-interactive-data"></a>Lägga till interaktiva data

Karta som har vi gjort tittar hittills bara på latitud-/longituddata för sökresultaten. Om du tittar på den oformaterade JSON som Maps-söktjänsten returnerar ser du att den innehåller ytterligare information om varje bensinstation, inklusive namn och adress. Du kan införliva dessa data i kartan med interaktiva popup-rutor.

1. Lägg till följande rader i *script*-blocket för att skapa popup-fönster för de orienteringspunkter som returneras av Search Service:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API:et **atlas. Popup** ger ett informationsfönster som är fäst vid motsvarande position på kartan. Det här kodavsnittet anger innehåll och plats för popup-fönstret. Det ger även en händelselyssnare i `map`-kontrollen som väntar på att _musen_ ska rullas över popup-fönstret.

2. Spara filen och uppdatera webbläsaren. Nu visar kartan i webbläsaren när du hovrar över någon av sökningskartnålarna.

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure Maps
> * Hitta primärnyckeln för ditt konto
> * Skapa ny webbsida med API:et Kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

Du kan komma åt kodexemplet för den här självstudien här:

> [Sök efter plats med Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/search.html)

Nästa självstudie demonstrerar hur du visar en väg mellan två platser.

> [!div class="nextstepaction"]
> [Dirigera mot ett mål](./tutorial-route-location.md)
