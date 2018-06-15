---
title: Söka med Azure Maps | Microsoft Docs
description: Söka efter orienteringspunkter i närheten med hjälp av Azure Maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4a414b5b865d31dd99b54ef9693abafb5490a50f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601793"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Söka efter orienteringspunkter i närheten med hjälp av Azure Maps

Den här självstudiekursen visar hur du skapar ett konto med Azure Maps och sedan använder API:er för Maps för att söka efter en orienteringspunkt. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Azure Maps-konto
> * Hämta primärnyckeln för ditt Maps-konto
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Maps-söktjänsten för att hitta orienteringspunkter i närheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen
Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Skapa ett konto med Azure Maps

Skapa ett nytt Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **Maps** i rutan *Sök på Marketplace*.
3. Bland *resultaten* väljer du **Maps**. Klicka på knappen **Skapa** som visas nedanför kartan. 
4. Ange följande värden på sidan **Skapa Maps-konto**:
    - *Namn* för ditt nya konto. 
    - Den *Prenumeration* som ska användas för det här kontot.
    - Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    - Välj en *Resursgruppsplats*.
    - Läs *licensen* och *sekretesspolicyn* och markera kryssrutan för att godkänna villkoren. 
    - Klicka på knappen **Skapa**.
   
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

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>

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
    
4. Spara dina ändringar i filen och öppna HTML-sidan i en webbläsare. Det här är den mest grundläggande mappningen du kan göra genom att anropa **atlas.map** och anger din kontonyckel. 

   ![Visa kartan](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Lägga till sökfunktioner

Det här avsnittet visar hur du API:et Maps Search för att hitta en orienteringspunkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. 

1. Lägg till ett nytt lager på kartan för att visa sökresultaten. Lägg till följande Javascript-kod till *skriptblocket* efter koden som initierar kartan. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Skapa en [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägg till en händelsehanterare för att parsa JSON-svaret som skickas av Maps-söktjänsten. Det här kodfragmentet skapar händelsehanteraren för att samla in adress, namn och information om latitud och longitud för varje plats som returneras i variabeln `searchPins`. Slutligen lägger det till den här samlingen av platser i kontrollen `map` som kartnålar. 

    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```

3. Lägg till följande kod till *skriptblocket* för att skapa frågan och skicka XMLHttpRequest till Maps-söktjänsten:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Det här kodfragmentet använder det enkla söknings-API:et i Search Service som kallas **Fuzzy Search**. Det hanterar de mest otydliga indata, däribland alla kombinationer av adresser eller orienteringspunkter. Det söker efter **bensinstationer** i närheten inom en angiven radie för de angivna koordinaterna för latituden och longituden. Det använder ditt kontos primärnyckel som angavs tidigare i exempelfilen för att anropa Maps. Det returnerar resultatet som par av latitud/longitud för de funna platserna. 
    
4. Spara filen **MapSearch.html** och uppdatera webbläsaren. Du bör nu se att kartan är centrerad i Seattle och att blå kartnålar markerar områdets bensinstationer. 

   ![Visa kartan med sökresultat](./media/tutorial-search-location/pins-map.png)

5. Du kan se i rådata att kartan återger genom att ta XMLHTTPRequest som du skapade i filen och lägger in den i webbläsaren. Ersätt \<din kontonyckel\> med primärnyckeln. 

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
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API:et **atlas. Popup** ger ett informationsfönster som är fäst vid motsvarande position på kartan. Det här kodfragmentet anger innehåll och position för popup-fönstret samt lägger till en händelselyssnare i kontrollen `map` som väntar på att _musen_ ska rullas över popup-fönstret. 

4. Spara filen och uppdatera webbläsaren. Nu visar kartan i webbläsaren när du hovrar över någon av sökningskartnålarna. 

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure Maps
> * Hitta primärnyckeln för ditt konto
> * Skapa ny webbsida med API:et Kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

Nästa självstudie demonstrerar hur du visar en väg mellan två platser. 

> [!div class="nextstepaction"]
> [Dirigera mot ett mål](./tutorial-route-location.md)
