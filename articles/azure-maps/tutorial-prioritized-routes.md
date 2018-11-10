---
title: Flera vägar med Azure Maps | Microsoft Docs
description: Hitta rutter för olika färdmedel med hjälp av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 10/29/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 67b68489f2e06b9149f842f293a769fa7f688be0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412711"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Hitta rutter för olika färdmedel med hjälp av Azure Maps

Den här självstudiekursen visar hur du använder Azure Maps-kontot och Route Service för att hitta rutten till orienteringspunkt, prioriterad efter färdmedel. Du visar två olika vägar på kartan, en för bilar och lastbilar som kan ha vägbegränsningar på grund av höjd, vikt eller farlig last. I den här guiden får du lära dig att:

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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera `onload`-händelsen i innehållet på sidan, som anropar funktionen `GetMap` när sidans innehåll har lästs in. Den här funktionen innehåller infogad JavaScript-kod för att komma åt Azure Maps-API:erna.

3. Lägg till följande JavaScript-kod i funktionen `GetMap`. Ersätt strängen **\<Din Azure Maps-nyckel\>** med den primärnyckel som du kopierade från Maps-kontot.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta och är en komponent i API:et Azure Kartkontroll.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisera trafikflödet

1. Lägga till displayen för trafikflöde i kartan. `map.events.add` ser till att alla kartfunktioner som lagts till på kartan läses in först när kartan har lästs in helt.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     En inläsningshändelse har lagts till på kartan, som utlöses när kartresurserna har lästs in helt. I händelsehanteraren för kartinläsning anges tradikflödesinställningen på kartan till `relative`, vilket är hastigheten på fartleden i förhållande till fritt flöde. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde.

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Du bör se gatorna i Los Angeles med deras aktuella trafikinformation.

   ![Visa trafikkarta](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien beräknas och renderas två vägar på kartan. En väg som använder fartleder som är tillgängliga för bilar och den andra som är tillgänglig för lastbilar. Vid rendering visar vi en symbolikon för början och slutet på vägen samt linjer med olika färger för varje vägfärd.

1. När du har initierat kartan lägger du till följande JavaScript-kod i GetMap-funktionen.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    En inläsningshändelse har lagts till på kartan, som utlöses när kartresurserna har lästs in helt. I händelsehanteraren för kartinläsningen skapas en datakälla för att lagra väglinjerna samt start- och slutpunkterna. Ett linjeskikt skapas och ansluts till datakällan för att definiera hur väglinjen ska renderas. Uttryck används för att hämta linjens bredd och färg från egenskaperna för väglinjefunktionen. Ett filter läggs till för att säkerställa att det här lagret endast renderar GeoJSON LineString-data. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att radlinjen inte täcker för vägetiketterna. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start- och slutpunkterna renderas. I det här fallet har uttryck lagts till för att hämta information om ikonbild och textetikett från egenskaperna för varje punktobjekt. 
    
2. För den här självstudien ska du ange en startpunkt som ett fiktivt företag i Seattle som heter Fabrikam, och målplatsen som ett Microsoft-kontor. I händelsehanteraren för kartinläsning lägger du till följande kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Den här koden skapar två [GeoJSON-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten. Egenskaperna `title` och `icon` har lagts till i varje punkt.

3. Lägg sedan till följande JavaScript-kod för att lägga till kartnålar för start- och slutpunkterna på kartan:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Det här avgränsningsfältet används till att ange kartkameravyn över start- och slutpunkterna med hjälp av funktionen `map.setCamera`. En utfyllnad läggs till för att kompensera för bildpunktsdimensionerna för symbolikonerna.

4. Spara filen och uppdatera webbläsaren om du vill se de fästen som visas på kartan. Nu är kartan centrerad över Seattle, och du kan se de runda blå kartnålarna som markerar startpunkten, och den blå kartnålen som markerar slutpunkten.

   ![Visa karta med start- och slutpunkter](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge rutter prioriterade efter färdmedel

Det här avsnittet visar hur du använder API:et Route Service i Azure Maps för att hitta flera rutter från en viss startpunkt till ett mål, beroende på färdmedel. Route Service tillhandahåller API:er för att planera den *snabbaste*, *kortaste*, *, miljövänligaste* eller *mest spännande* rutten mellan två platser, med hänsyn till aktuella trafikförhållanden. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Samtliga följande kodblock ska läggas till **i kartinläsningsfunktionen eventListener i kartan**, så att de laddas först när kartan har lästs in helt.

1. Instansiera tjänstklienten genom att lägga till följande Javascript-kod i händelsehanteraren för kartinläsning.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Lägg till följande block med kod för att konstruera en vägfrågesträng.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Lägg till följande JavaScript-kod för att begära en väg för en lastbil som har USHazmatClass2-klassificerad last och visa resultatet:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Kodavsnittet ovan kör frågor mot Azure Maps-vägtjänsten via metoden [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) och parsar sedan svaret till GeoJSON-format med hjälp av metoden [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Sedan skapar det en matris med koordinater för den väg som returneras och lägger till den i datakällan, men det lägger även till ett index på 0 så att den renderas före andra linjer i datakällan. Detta görs eftersom beräkningen av lastbilsvägen ofta tar längre tid än beräkningen för en bilväg, och om lastbilens väglinje läggs till i datakällan efter bilvägen renderas den före bilvägen. Två egenskaper läggs till i lastbilens väglinje: en linjefärg med en fin blå nyans samt en linjebredd på 9 bildpunkter. 

4. Lägg till följande JavaScript-kod för att begära en väg för en bil och visa resultatet:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Det här kodfragmentet använder samma ruttfråga för en bil som för en lastbil. Den frågar Azure Maps-vägtjänsten via metoden [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) och parsar sedan svaret i GeoJSON-format med hjälp av metoden [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Därefter skapas en matris med koordinater för den återgivna rutten, och denna läggs till i datakällan. Två egenskaper läggs till i bilens väglinje: en linjefärg med lila nyans samt en linjebredd på 5 bildpunkter. 

5. Spara filen **MapTruckRoute.html** och uppdatera webbläsaren så att den visar resultatet. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande.

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Lastbilsvägen är blå och tjockare och bilvägen är lila och tunnare. Bilvägen sträcker sig över Lake Washington via I-90, som går genom tunnlarna under bostadsområdena och begränsar farlig avfallslast. Lastbilsvägen som anger en USHazmatClass2-lasttyp dirigeras korrekt för att använda en annan väg.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

Du kan komma åt kodexemplet för den här självstudien här:

> [Flera vägar med Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[Se exemplet live här](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Mer information om täckning och funktionerna i Azure Maps:

> [!div class="nextstepaction"]
> [Zoomningsnivåer och vanliga rutnät](zoom-levels-and-tile-grid.md)

Fler kodexempel och en interaktiv kodupplevelse:

> [!div class="nextstepaction"]
> [Så här använder du Kartkontroll](how-to-use-map-control.md)
