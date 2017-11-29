---
title: "Hitta vägen med Azure baserad platstjänster | Microsoft Docs"
description: "Vidarebefordra till en av intresse med hjälp av Azure baserad platstjänster"
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
ms.openlocfilehash: 0f784e8ecd8fc94c12df1a819055718e06547b6b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Vidarebefordra till en av intresse med hjälp av Azure baserad platstjänster

Den här kursen visar hur du använder Azure plats Services-konto och väg Service SDK, för att hitta vägen till intressant. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Hämta adressen koordinater
> * Fråga väg Service för instruktioner peka intressanta

## <a name="prerequisites"></a>Krav

Innan du fortsätter, kontrollera att [skapa Azure baserad platstjänster kontot](./tutorial-search-location.md#createaccount), och [hämta nyckel för prenumerationen för ditt konto](./tutorial-search-location.md#getkey). Du kan också se hur du använder Kartkontrollen och API: er för Search-tjänsten enligt beskrivningen i självstudierna [Sök Närliggande intressant med hjälp av Azure baserad platstjänster](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Hämta adressen koordinater

Använd följande steg för att skapa en statisk HTML-sida inbäddade med plats baserat tjänsterna kartan kontroll-API. 

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
    Observera hur HTML-huvudet bäddar in resursplatser för CSS- och JavaScript-filer för Azure baserad platstjänster-biblioteket. Notera också den *skriptet* segment i brödtexten för HTML-fil som innehåller infogat JavaScript-kod för att komma åt Azure plats baserat tjänstens API: er.

3. Lägg till följande JavaScript-kod till den *skriptet* block med HTML-fil. Ersätt platshållaren *< INS >* med ditt konto baserat platstjänster primärnyckel.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    Den **atlas. Kartan** ger kontrollen för en visual och interaktiva webb och är en komponent i Azure kartan kontroll-API: et.

4. Lägg till följande JavaScript-kod till den *skriptet* block. Detta lägger till ett lager av *linestrings* att Kartkontrollen att visa vägen:

    ```HTML
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

5. Lägg till följande JavaScript-kod för att skapa start- och slutpunkterna för vägen:

    ```HTML
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
    Den här koden skapar två [GeoJSON objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för vägen. Slutpunkten är en latitud/longitud kombination av något av de *bensin stationer* sökte i föregående kursen [Sök Närliggande intressant med hjälp av Azure baserad platstjänster](./tutorial-search-location.md).

6. Lägg till följande JavaScript-kod för att lägga till PIN-koder för start- och slutpunkter på kartan:

    ```HTML
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
    API: et **map.setCameraBounds** justerar fönstret kartan enligt koordinaterna för start- och slutpunkter. API: et **map.addPins** lägger till punkterna i kartkontrollen som visuella komponenter.

7. Spara den **MapRoute.html** fil på din dator. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Fråga väg Service för instruktioner peka intressanta

Det här avsnittet visar hur du använder Azure plats baserat tjänsterna väg Service API för att hitta vägen från en viss startpunkt till ett mål. Tjänsten vägen innehåller API: er för att planera de snabbaste kortaste eller eko flödet mellan två platser, med tanke på villkor som realtid trafik. Det gör även att användare att planera vägar i framtiden genom att använda Azures omfattande historiska trafik databasen och förutsäga väg varaktighet för varje dag och tid. 

1. Öppna den **MapRoute.html** filen skapas i föregående avsnitt och Lägg till följande JavaScript-kod till den *skriptet* block som illustrerar flödet-tjänsten.

    ```HTML
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
    Det här kodstycket skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/), och lägger till en händelsehanterare för att analysera inkommande svaret. Ett lyckat svar skapar en matris med koordinater för linjesegment första vägens returneras. Det lägger sedan till den här uppsättningen koordinater för den här vägen på kartan *linestrings* lager.

2. Lägg till följande kod i den *skriptet* block, att skicka XMLHttpRequest till Azure plats baserat tjänsterna väg tjänsten:

    ```HTML
            var url = "https://atlas.microsoft.com/route/directions/json?";
            url += "&api-version=1.0";
            url += "&subscription-key=" + subscriptionKey;
            url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    
            xhttp.open("GET", url, true);
            xhttp.send();
    ```
    Begäran ovan visar de obligatoriska parametrarna är prenumeration kontonyckel och koordinaterna för start- och slutpunkterna i angiven ordning. 

3. Spara den **MapRoute.html** filen lokalt, och sedan öppna den i en webbläsare som du väljer och se resultatet. För en lyckad anslutning med den plats baserat tjänsterna API: er, bör du se en karta som liknar följande. 

    ![Azure Kartkontrollen och Route-tjänsten](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Hämta adressen koordinater
> * Fråga väg Service för instruktioner peka intressanta

Gå vidare till självstudiekursen [hitta vägar för olika lägen för resa med hjälp av Azure baserad platstjänster](./tutorial-prioritized-routes.md) att lära dig mer om Azure baserad platstjänsterna för att prioritera vägar till intressant, baserat på transportsätt. 
