---
title: Hitta vägen med Azure Maps | Microsoft Docs
description: Rutt till en orienteringspunkt med hjälp av Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 09828fade464c3b7b5f6eedaa16513e9eab49467
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989650"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Rutt till en orienteringspunkt med hjälp av Azure Maps

Den här självstudiekursen visar hur du använder Azure Maps-kontot och Route Service SDK för att hitta rutten till en orienteringspunkt. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Ställa in adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du fortsätter ska du följa stegen i föregående självstudie för att [skapa ditt Azure Maps-konto](./tutorial-search-location.md#createaccount) och [hämta prenumerationsnyckeln för ditt konto](./tutorial-search-location.md#getkey). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta 
Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll. 

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
    HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure Maps-biblioteket. Segmentet *script* i HTML-filens brödtext innehåller den infogade JavaScript-koden för att komma åt Azure Maps-API:erna.

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

   ![Visa grundläggande karta](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Ange start- och slutpunkter

För den här självstudien anger du startpunkt som Microsoft, och målpunkt som en bensinstation i Seattle. 

1. I samma *skriptblock* i **MapRoute.html**-filen lägger du till följande JavaScript-kod för att skapa start- och slutpunkter för rutten:

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
    Den här koden skapar två [GeoJSON-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten. 

2. Lägg till följande JavaScript-kod för att lägga till kartnålar för start- och slutpunkterna på kartan:

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
    **map.setCameraBounds** justerar kartfönstret enligt koordinaterna för start- och slutpunkterna. API:et **map.addPins** lägger till punkterna i Kartkontroll som visuella komponenter.

7. Spara filen **MapRoute.html** och uppdatera webbläsaren. Nu är kartan centrerad över Seattle, och du kan se de runda blå kartnålarna som markerar startpunkten, och den blå kartnålen som markerar slutpunkten.

   ![Visa karta med start- och slutpunkterna markerade](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Hämta anvisningar

Det här avsnittet visar hur du använder API:et Route Service i Azure Maps för att hitta rutten från en viss startpunkt till ett mål. Route Service tillhandahåller API:er för att planera den *snabbaste*, *kortaste*, *, miljövänligaste* eller *mest spännande* rutten mellan två platser. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Lägg först till ett lager på kartan för att visa vägen eller *linestring*. Lägg till följande JavaScript-kod i *script*-blocket:

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

2. Skapa en [XMLHttpRequest](https://xhr.spec.whatwg.org/) och lägg till en händelsehanterare för att parsa JSON-svaret som skickas av Maps-vägtjänsten. Den här koden skapar en matris med koordinater för vägens linjesegment och lägger sedan till uppsättningen koordinater för kartans linestrings-lager. 

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

3. Lägg till följande kod för att skapa frågan och skicka XMLHttpRequest till Maps-vägtjänsten:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Spara filen **MapRoute.html** och uppdatera webbläsaren. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande. 

    ![Azure Kartkontroll och Route Service](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Ställa in adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

Nästa självstudie visar hur du skapar en vägfråga med begränsningar som resläge eller typ av last och visar sedan flera vägar på samma karta. 

> [!div class="nextstepaction"]
> [Hitta rutter för olika färdmedel](./tutorial-prioritized-routes.md)
