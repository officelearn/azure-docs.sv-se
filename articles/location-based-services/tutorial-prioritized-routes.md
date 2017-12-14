---
title: "Multipla vägar med Azure baserad platstjänster | Microsoft Docs"
description: "Hitta vägar för olika lägen för resa med hjälp av Azure baserad platstjänster"
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
ms.openlocfilehash: 7d8eb900bdc90a391d4121b7bfb863fc274fc564
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Hitta vägar för olika lägen för resa med hjälp av Azure baserad platstjänster

Den här kursen visar hur du använder Azure plats Services-konto och väg Service SDK, för att hitta vägen till intressant, prioriteras av läget resor. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera Route Service-fråga
> * Återge vägar prioriteras av läget för resa

## <a name="prerequisites"></a>Krav

Innan du fortsätter, kontrollera att [skapa Azure baserad platstjänster kontot](./tutorial-search-location.md#createaccount), och [hämta nyckel för prenumerationen för ditt konto](./tutorial-search-location.md#getkey). Du kan också se hur du använder Kartkontrollen och API: er för Search-tjänsten enligt beskrivningen i självstudierna [Sök Närliggande intressant med hjälp av Azure baserad platstjänster](./tutorial-search-location.md), samt Lär dig grundläggande användning av flödet Service API: er som beskrivs i kursen [väg till en plats med hjälp av Azure baserad platstjänster intressanta](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Konfigurera Route Service-fråga

Använd följande steg för att skapa en statisk HTML-sida inbäddade med plats baserat tjänsterna kartan kontroll-API. 

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapTruckRoute.html**. 
2. Lägg till följande HTML-komponenter i filen:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    Observera att HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure baserad platstjänster-biblioteket. Notera också den *skriptet* segment som lagts till i brödtexten för HTML-format, som innehåller infogat JavaScript-kod för åtkomst till Azure kartan kontroll-API.
3. Lägg till följande JavaScript-kod till den *skriptet* block med HTML-fil. Ersätt platshållaren *< INS >* med ditt konto baserat platstjänster primärnyckel.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var subscriptionKey = "<insert-key>";
    var map = new atlas.Map("map", {
        "subscription-key": subscriptionKey
    });
    ```
    Den **atlas. Kartan** ger kontrollen för en visual och interaktiva webb och är en komponent i Azure kartan kontroll-API: et.

4. Lägg till följande JavaScript-kod till den *skriptet* block, att lägga till det flöde som visningen på trafik till kartan:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Den här koden anger flödet i nätverkstrafiken till `relative`, vilket är hastigheten på vägen i förhållande till kostnadsfri flödet. Du kan också ange den till `absolute` hastigheten på väg, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från kostnadsfri flöde. 

5. Lägg till följande JavaScript-kod för att skapa de PIN-koderna för start- och slutpunkterna för vägen:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Den här koden skapar två [GeoJSON objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för vägen. 

6. Lägg till följande JavaScript-kod för att lägga till lager i *linestrings* att Kartkontrollen att visa rutter baserat på transportsätt, till exempel _bilen_ och _lastbil_.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. Lägg till följande JavaScript-kod för att lägga till start- och slutpunkter på kartan:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    API: et **map.setCameraBounds** justerar fönstret kartan enligt koordinaterna för start- och slutpunkter. API: et **map.addPins** lägger till punkterna i kartkontrollen som visuella komponenter.

8. Spara den **MapTruckRoute.html** fil på din dator. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge vägar prioriteras av läget för resa

Det här avsnittet visar hur du använder Azure plats baserat tjänsterna väg Service API för att söka efter multipla vägar från en viss start peka på ett mål, baserat på din transportsätt. Tjänsten vägen innehåller API: er för att planera de snabbaste kortaste eller eko flödet mellan två platser, med tanke på villkor som realtid trafik. Det gör även att användare att planera vägar i framtiden genom att använda Azures omfattande historiska trafik databasen och förutsäga väg varaktighet för varje dag och tid. 

1. Öppna den **MapTruckRoute.html** filen skapas i föregående avsnitt och Lägg till följande JavaScript-kod till den *skriptet* block, att hämta flödet för en lastbil med hjälp av tjänsten vägen.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + subscriptionKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Det här kodstycket skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/), och lägger till en händelsehanterare för att analysera inkommande svaret. Den skapar en matris med koordinater för det flöde som returneras för ett lyckat svar och lägger till den på kartan `truckRouteLayerName` lager. 
    
    Det här kodstycket skickar också frågan till tjänsten väg att hämta vägen för start- och slutpunkt för prenumerationen kontonyckel. Följande valfria parametrar används för att ange flödet för en tung lastbil:-parametern `travelMode=truck` anger läget för resa som *lastbil*. Andra former av resa som stöds är *taxi*, *bus*, *van*, *motorcykel*, och standardvärdet *bilen* . 
        -Parametrarna `vehicleWidth`, `vehicleHeight`, och `vehicleLength` ange dimensionerna för programuppdatering i mätare och anses endast om läget för resa är *lastbil*. 
        - `vehicleLoadType` Klassificerar last som farliga och begränsade på vissa vägar. Detta betraktas som för närvarande endast för den *lastbil* läge. 

2. Lägg till följande JavaScript-kod för att få flödet för en bil med hjälp av tjänsten väg:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + subscriptionKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Det här kodstycket skapar en annan [XMLHttpRequest](https://xhr.spec.whatwg.org/), och lägger till en händelsehanterare för att analysera inkommande svaret. Den skapar en matris med koordinater för det flöde som returneras för ett lyckat svar och lägger till den på kartan `carRouteLayerName` lager. 
    
    Det här kodstycket skickar också frågan till tjänsten väg att hämta vägen för start- och slutpunkt för prenumerationen kontonyckel. Eftersom inga andra parametrar används väg att resa standardläget *bilen* returneras. 

3. Spara den **MapTruckRoute.html** filen lokalt, och sedan öppna den i en webbläsare som du väljer och se resultatet. För en lyckad anslutning med den plats baserat tjänsterna API: er, bör du se en karta som liknar följande. 

    ![Prioriterad vägar med Azure väg Service](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Observera att lastbil flödet i blå färg, medan bil vägen är lila.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera Route Service-fråga
> * Återge vägar prioriteras av läget för resa

Gå vidare till den **begrepp** och **hur man** artiklar för att läsa Azure plats baserat Services SDK i mer detalj. 
