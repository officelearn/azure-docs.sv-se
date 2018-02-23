---
title: "Söka med Azure Location Based Services | Microsoft Docs"
description: "Söka efter orienteringspunkter i närheten med hjälp av Azure Location Based Services"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 791992028d11633fc20f55ae1a34e7fcd442bf3a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="search-nearby-points-of-interest-using-azure-location-based-services"></a>Söka efter orienteringspunkter i närheten med hjälp av Azure Location Based Services

Den här självstudiekursen visar hur du skapar ett konto med Azure Location Based Services och sedan använder tillhandahållna API:er för att söka efter en orienteringspunkt. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett konto med Azure Location Based Services
> * Hitta primärnyckeln för ditt konto i Azure Location Based Services
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Skapa ett konto med Azure Location Based Services

Följ dessa steg för att skapa ett nytt konto i Location Based Services.

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **location based services** i rutan *Search the Marketplace* (sök på marketplace).
3. Klicka på **Location Based Services (förhandsversion)** i *Resultat*. Klicka på knappen **Skapa** som visas nedanför kartan. 
4. Ange följande värden på sidan **Skapa Location Based Services-konto**:
    - *Namn* för ditt nya konto. 
    - Den *Prenumeration* som ska användas för det här kontot.
    - Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    - Välj en *Resursgruppsplats*.
    - Läs *villkoren för förhandsversionen* och markera kryssrutan för att acceptera villkoren. 
    - Klicka slutligen på knappen **Skapa**.
   
    ![Skapa Location Based Services-konto i portalen](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt konto för Location Based Services har skapats följer du stegen för att länka det till kartsöknings-API:er:

1. Öppna ditt Location Based Services-konto i portalen.
2. Navigera till kontots **INSTÄLLNINGAR** och välj sedan **Nycklar**.
3. Kopiera **Primärnyckel** till Urklipp. Spara lokalt för att använda i senare steg. 

    ![Hämta primärnyckel i portalen](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Skapa ny webbsida med API:et Azure Kartkontroll
API:et Azure Kartkontroll är ett praktiskt klientbiblioteket som hjälper dig att enkelt integrera Azure Location Based Services i ditt webbprogram. Det döljer komplexiteten i de rena REST-tjänstanropen och ökar produktiviteten med formateringsbara och anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll i Location Based Services. 

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
    Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera segmentet *script* som lagts till i HTML-filens *brödtext*. Det här segmentet innehåller infogad JavaScript-kod för att komma åt API:et i Azure Location Based Services.
 
3.  Lägg till följande JavaScript-kod i HTML-filens *script*-block. Använd primärnyckeln från ditt Location Based Services-konto i skriptet. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Det här segmentet initierar API:et Kartkontroll för din Azure Location Based Services-kontonyckel. **Atlas** är det namnområde som innehåller API:et Azure Kartkontroll och relaterade visuella komponenter. **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta. Du kan se hur kartan ser ut som genom att öppna HTML-sidan i webbläsaren. 

4. Lägg till följande JavaScript-kod till *script*-blocket för att lägga till ett lager med sökningskartnålar i Kartkontroll:

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. Spara filen på din dator. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Använda Search Service för att hitta orienteringspunkter i närheten

Det här avsnittet visar hur du API:et Search Service i Azure Location Based Services för att hitta en orienteringspunkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. 

1. Öppna filen **MapSearch.html** som skapades i föregående avsnitt och lägg till följande JavaScript-kod i *script*-blocket för att illustrera Search Service. 
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
    Det här kodstycket skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägger till en händelsehanterare för att analysera det inkommande svaret. För ett lyckat svar samlar det in adresser, namn, latitud- och logitudinformation för varje plats som returneras i variabeln `searchPins`. Slutligen lägger det till den här samlingen av platser i kontrollen `map` som kartnålar. 

2. Lägg till följande kod i *script*-blocket för att skicka XMLHttpRequest till Search Service i Azure Location Based Services:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Det här kodfragmentet använder det enkla söknings-API:et i Search Service som kallas **Fuzzy Search**. Det hanterar de mest otydliga indata, alla kombinationer av adresser eller *orienteringspunkter*. Det söker efter **bensinstationer** i närheten, efter den angivna adressen i latitud och longitud och inom angiven radie. Det använder ditt kontos primärnyckel som angavs tidigare i exempelfilen för att anropa Location Based Services. Det returnerar resultatet som par av latitud/longitud för de funna platserna. Du kan se sökningskartnålarna genom att öppna HTML-sidan i webbläsaren. 

3. Lägg till följande rader i *script*-blocket för att skapa popup-fönster för de orienteringspunkter som returneras av Search Service:

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
    API:et **atlas. Popup** ger ett informationsfönster som är fäst vid motsvarande position på kartan. Det här kodfragmentet anger innehåll och position för popup-fönstret samt lägger till en händelselyssnare i kontrollen `map` som väntar på att _musen_ ska rullas över popup-fönstret. 

4. Spara filen och öppna filen **MapSearch.html** i valfri webbläsare och se resultatet. Nu visas information i popup-fönster på kartan i webbläsaren när du hovrar över någon av de sökningskartnålar som visas, ungefär som nedan. 

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure Location Based Services
> * Hitta primärnyckeln för ditt konto
> * Skapa ny webbsida med API:et Kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

Gå vidare till självstudiekursen om att [visa vägbeskrivning till en orienteringspunkt med hjälp av Azure Location Based Services](./tutorial-route-location.md) för att lära dig hur du använder Azure Location Based Services för att visa en vägbeskrivning till din orienteringspunkt. 
