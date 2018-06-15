---
title: Flera vägar med Azure Maps | Microsoft Docs
description: Hitta rutter för olika färdmedel med hjälp av Azure Maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ea68c70befde1922fc30d06386a2ae326460c722
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601419"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Hitta rutter för olika färdmedel med hjälp av Azure Maps

Den här självstudiekursen visar hur du använder Azure Maps-kontot och Route Service för att hitta rutten till orienteringspunkt, prioriterad efter färdmedel. Du visar två olika vägar på kartan, en för bilar och lastbilar som kan ha vägbegränsningar på grund av höjd, vikt eller farlig last. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du fortsätter ska du följa stegen i den första självstudien för att [skapa ditt Azure Maps-konto](./tutorial-search-location.md#createaccount) och [hämta prenumerationsnyckeln för ditt konto](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Skapa en ny karta 
Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll. 

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
    HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure Maps-biblioteket. *Skriptsegmentet* i HTML-brödtexten innehåller den infogade JavaScript-koden för kartan.
3. Lägg till följande JavaScript-kod i HTML-filens *script*-block. Ersätt strängen **\<din kontonyckel\>** med primärnyckeln som du kopierade från Maps-kontot.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta och är en komponent i API:et Azure Kartkontroll.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer. 

   ![Visa grundläggande karta](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisera trafikflödet

1. Om du inte anger var kartan ska fokusera ser du en vy över hela världen. För att kunna visa trafikdata ska du ställa in en mittpunkt och en zoomnivå på kartan. Ersätt koden som deklarera en `new atlas.Map` med följande JavaScript-kod: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Den här koden ställer in mittpunkten för kartan och deklarerar en zoomnivå så att du kan fokusera på ett visst område som standard. 

1. Lägg till visning av trafikflöde på kartan:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Den här koden anger trafikflödet till `relative`, vilket är hastigheten på vägen i förhållande till fritt flöde. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde. 

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Du bör se gatorna i Los Angeles med deras aktuella trafikinformation.

   ![Visa trafikkarta](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Ange start- och slutpunkter

För den här självstudien ska du ange en startpunkt som ett fiktivt företag i Seattle som heter Fabrikam, och målplatsen som ett Microsoft-kontor. 

1. Lägg till följande JavaScript-kod för att skapa kartnålar för start- och slutpunkter för rutten:

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

2. Lägg till följande JavaScript-kod för att lägga till start- och slutpunkterna på kartan:

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
    Anropet **map.setCameraBounds** justerar kartfönstret enligt koordinaterna för start- och slutpunkterna. API:et **map.addPins** lägger till punkterna i Kartkontroll som visuella komponenter.

3. Spara filen och uppdatera webbläsaren om du vill se de fästen som visas på kartan. Trots att du deklarerade kartan med en mittpunkt i Los Angeles flyttade **map.setCameraBounds** vyn för att visa start- och slutpunkter. 

   ![Visa karta med start- och slutpunkter](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge rutter prioriterade efter färdmedel

Det här avsnittet visar hur du använder API:et Route Service i Azure Maps för att hitta flera rutter från en viss startpunkt till ett mål, beroende på färdmedel. Route Service tillhandahåller API:er för att planera den *snabbaste*, *kortaste*, *, miljövänligaste* eller *mest spännande* rutten mellan två platser, med hänsyn till aktuella trafikförhållanden. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Lägg först till ett lager på kartan för att visa vägen eller *linestring*. I den här självstudien finns det två olika vägar, **bilväg** och **lastbilsväg**, var och en med sin egen design. Lägg till följande JavaScript-kod i *script*-blocket:

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

2. Lägg till följande JavaScript-kod till *skriptblocket* för att begära en väg för en lastbil och visa resultatet på kartan:

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
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
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
    
    Det här kodfragmentet skapar också frågan för Maps Route Service med din kontonyckel. Frågan innehåller koordinater för startpunkt, slutpunkt och valfria parametrar för att indikera att vägen är för tung lastbil.

2. Lägg till följande JavaScript-kod för att begära en väg för en bil och visa resultatet:

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
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Det här kodfragmentet skapar en till [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägger till en händelsehanterare för att analysera det inkommande svaret. För ett lyckat svar skapas en matris med koordinater för den återgivna rutten och den läggs till i kartlagret `carRouteLayerName`. 
    
    Det här kodfragmentet skapar också frågan för Maps Route Service med din kontonyckel. Frågan innehåller koordinaterna för startpunkt och slutpunkt. Eftersom det inte finns några fler parametrar använder Route Service *bil* som standardresläge. 

3. Spara filen **MapTruckRoute.html** och uppdatera webbläsaren så att den visar resultatet. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande. 

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Lastbilsvägen är blå och tjockare och bilvägen är lila och tunnare. Bilvägen sträcker sig över Lake Washington via I-90, som går genom tunnlarna under bostadsområdena och begränsar farlig avfallslast. Lastbilsvägen som anger en USHazmatClass2-lasttyp dirigeras korrekt för att använda en annan väg. 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

Mer information om omfattningen av och funktionerna i Azure Maps finns i [Zoomningsnivåer och rutnät](zoom-levels-and-tile-grid.md) och de andra begreppsartiklarna. 

Fler kodexempel och en interaktiv kodupplevelse finns i [Så här använder du Kartkontroll](how-to-use-map-control.md) och andra Så här-guider. 
