---
title: Hitta rutten med Azure Location Based Services | Microsoft Docs
description: "Rutt till en orienteringspunkt med hjälp av Azure Location Based Services"
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
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Rutt till en orienteringspunkt med hjälp av Azure Location Based Services

Den här självstudiekursen visar hur du använder Azure Location Based-kontot och Route Service SDK för att hitta rutten till en orienteringspunkt. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Hämta adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom ihåg att [skapa ditt Azure Location Based Services-konto](./tutorial-search-location.md#createaccount) och [hämta prenumerationsnyckeln för ditt konto](./tutorial-search-location.md#getkey) innan du fortsätter. Du kan också notera hur du använder API:er för Kartkontroll och Search Service enligt beskrivningen i självstudiekursen [Söka efter orienteringspunkter i närheten med hjälp av Azure Location Based Services](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Hämta adresskoordinater

Använd följande steg för att skapa en statisk HTML-sida inbäddad med API:et Kartkontroll i Location Based Services. 

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapRoute.html**. 
2. Lägg till följande HTML-komponenter i filen:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    Observera hur HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure Location Based Services-biblioteket. Notera också segmentet *script* i HTML-filens brödtext som innehåller den infogade JavaScript-koden för att komma åt Azure Location Based Services-API:erna.

3. Lägg till följande JavaScript-kod i HTML-filens *script*-block. Använd primärnyckeln från ditt Location Based Services-konto i skriptet.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta och är en komponent i API:et Azure Kartkontroll.

4. Lägg till följande JavaScript-kod i *script*-blocket. Detta lägger till ett lager av *linestrings* i Kartkontroll för att visa rutten:

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. Lägg till följande JavaScript-kod för att skapa start- och slutpunkter för rutten:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Den här koden skapar två [GeoJSON-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten. Slutpunkten är en latitud–longitud-kombination för en av de *bensinstationer* som söktes i föregående kurs [Söka efter orienteringspunkter i närheten med hjälp av Azure Location Based Services](./tutorial-search-location.md).

6. Lägg till följande JavaScript-kod för att lägga till kartnålar för start- och slutpunkterna på kartan:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    API:et **map.setCameraBounds** justerar kartfönstret enligt koordinaterna för start- och slutpunkterna. API:et **map.addPins** lägger till punkterna i Kartkontroll som visuella komponenter.

7. Spara filen **MapRoute.html** på din dator. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Fråga Route Service om vägbeskrivning till orienteringspunkt

Det här avsnittet visar hur du använder API:et Route Service i Azure Location Based Services för att hitta rutten från en viss startpunkt till ett mål. Route Service tillhandahåller API:er för att planera den snabbaste, kortaste eller miljövänligaste rutten mellan två platser, med hänsyn för trafikförhållanden i realtid. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. 

1. Öppna filen **MapRoute.html** som skapades i föregående avsnitt och lägg till följande JavaScript-kod i *script*-blocket för att illustrera Route Service.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    Det här kodfragmentet skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägger till en händelsehanterare för att analysera det inkommande svaret. Vid ett lyckat svar skapas en matris med koordinater för linjesegment för den första rutten som returneras. Sedan läggs koordinaterna för den här rutten till i kartlagret *linestrings*.

2. Lägg till följande kod i *script*-blocket för att skicka XMLHttpRequest till Route Service i Azure Location Based Services:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    Förfrågan ovan visar de obligatoriska parametrarna, som är din kontonyckel och koordinaterna för start- och slutpunkterna i denna ordning. 

3. Spara filen **MapRoute.html** lokalt och öppna den i valfri webbläsare för att se resultatet. För en lyckad anslutning med den API:er i Location Based Services bör du se en karta som liknar följande. 

    ![Azure Kartkontroll och Route Service](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Hämta adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

Gå vidare till självstudiekursen [Hitta rutter för olika färdmedel med hjälp av Azure Location Based Services](./tutorial-prioritized-routes.md) och lär dig mer om att använda Azure Location Based Services för att prioritera rutter till orienteringspunkten beroende på transportsätt. 
