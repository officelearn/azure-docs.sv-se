---
title: Flera rutter med Azure Location Based Services | Microsoft Docs
description: "Hitta rutter för olika färdmedel med hjälp av Azure Location Based Services"
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
ms.openlocfilehash: 9501f3c8fa1abb6bcbfee086c542139596ab5504
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Hitta rutter för olika färdmedel med hjälp av Azure Location Based Services

Den här självstudiekursen visar hur du använder Azure Location Based Services-kontot och Route Service SDK för att hitta rutten till orienteringspunkt, prioriterad efter färdmedel. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera Route Service-frågan
> * Återge rutter prioriterade efter färdmedel

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom ihåg att [skapa ditt Azure Location Based Services-konto](./tutorial-search-location.md#createaccount) och [hämta en nyckel från ditt konto](./tutorial-search-location.md#getkey) innan du fortsätter. Du kanske också vill läsa mer om hur du använder API:erna Kartkontroll och Search Service som beskrivs i självstudiekursen [Söka efter orienteringspunkter i närheten med hjälp av Azure Location Based Services](./tutorial-search-location.md) samt lära dig grundläggande användning av Route Service API:erna som beskrivs i kursen [Rutt till en orienteringspunkt med hjälp av Azure Location Bases Services](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Konfigurera Route Service-frågan

Använd följande steg för att skapa en statisk HTML-sida inbäddad med API:et Kartkontroll i Location Based Services. 

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
    Observera att HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure Location Based Services-biblioteket. Notera också *script*-segmentet i HTML-filens brödtext som innehåller den infogade JavaScript-koden för att komma åt Azure Location Based Services-API:erna.
3. Lägg till följande JavaScript-kod i HTML-filens *script*-block. Ersätt platshållaren *<insert-key>* med Location Based Services-kontots primärnyckel.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta och är en komponent i API:et Azure Kartkontroll.

4. Lägg till följande JavaScript-kod till *script*-blocket för att lägga till visning av trafikflöde på kartan:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Den här koden anger trafikflödet till `relative`, vilket är hastigheten på vägen i förhållande till fritt flöde. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde. 

5. Lägg till följande JavaScript-kod för att skapa kartnålar för start- och slutpunkter för rutten:

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
    Den här koden skapar två [GeoJSON-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten. 

6. Lägg till följande JavaScript-kod för att lägga till lager i *linestrings* i Kartkontroll för att visa rutter baserat på färdmedel, till exempel _bil_ och _lastbil_.

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

7. Lägg till följande JavaScript-kod för att lägga till start- och slutpunkterna på kartan:

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
    API:et **map.setCameraBounds** justerar kartfönstret enligt koordinaterna för start- och slutpunkterna. API:et **map.addPins** lägger till punkterna i Kartkontroll som visuella komponenter.

8. Spara filen **MapTruckRoute.html** på din dator. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge rutter prioriterade efter färdmedel

Det här avsnittet visar hur du använder API:et Route Service i Azure Location Based Services för att hitta flera rutter från en viss startpunkt till ett mål, beroende på färdmedel. Route Service tillhandahåller API:er för att planera den snabbaste, kortaste eller miljövänligaste rutten mellan två platser, med hänsyn för trafikförhållanden i realtid. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. 

1. Öppna filen **MapTruckRoute.html** som skapades i föregående avsnitt och lägg till följande JavaScript-kod i *script*-blocket för att hämta rutten för en lastbil med hjälp av Route Service.

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
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
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
    Det här kodfragmentet skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägger till en händelsehanterare för att analysera det inkommande svaret. För ett lyckat svar skapas en matris med koordinater för den återgivna rutten och den läggs till i kartlagret `truckRouteLayerName`. 
    
    Det här kodstycket skickar också frågan till Route Service för att hämta rutten för angiven start- och slutpunkt, för din kontonyckel. Följande valfria parametrar används för att ange rutten för en tung lastbil:
   - Parametern `travelMode=truck` anger resläget som *truck* (lastbil). Andra färdmedel som stöds är *taxi*, *bus*, *van*, *motorcycle* och standardvärdet *car* (bil).
   - Parametrarna `vehicleWidth`, `vehicleHeight`, och `vehicleLength` anger fordonets dimensioner i meter och beaktas endast om färdmedlet är *truck* (lastbil).
   - `vehicleLoadType` klassificerar lasten som farlig och begränsad på vissa vägar. Detta beaktas för närvarande endast för läget *truck* (lastbil).

2. Lägg till följande JavaScript-kod för att hämta rutten för en bil med hjälp av Route Service:

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
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Det här kodfragmentet skapar en till [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägger till en händelsehanterare för att analysera det inkommande svaret. För ett lyckat svar skapas en matris med koordinater för den återgivna rutten och den läggs till i kartlagret `carRouteLayerName`. 
    
    Det här kodstycket skickar också frågan till Route Service för att hämta rutten för de angivna start- och slutpunkten, för din kontonyckel. Eftersom inga andra parametrar används returneras rutten för standardläget *car* (bil). 

3. Spara filen **MapTruckRoute.html** lokalt och öppna den i valfri webbläsare för att se resultatet. För en lyckad anslutning med den API:er i Location Based Services bör du se en karta som liknar följande. 

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Observera att lastbilsrutten har blå färg, medan bilrutten är lila.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera Route Service-frågan
> * Återge rutter prioriterade efter färdmedel

Gå vidare till artiklarna om **begrepp** och **instruktioner** för att lära mer om Azure Location Based Services SDK i mer detalj. 
