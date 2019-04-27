---
title: Flera vägar med Azure Maps | Microsoft Docs
description: Hitta rutter för olika färdmedel med hjälp av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3e33eef430db627a82bb82e8c22ddc861d08982b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692114"
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
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

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

3. Lägg till följande JavaScript-kod i funktionen `GetMap`. Ersätt strängen `<Your Azure Maps Key>` med den primära nyckeln som du kopierade från ditt Maps-konto.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    Den `atlas.Map` klassen ger kontroll en visuell och interaktiv webbkarta och är en komponent i Azure kartan kontroll-API: et.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisera trafikflödet

1. Lägga till displayen för trafikflöde i kartan. Kartorna `ready` händelse väntar tills maps-resurser är inläst och klar att på ett säkert sätt interagera med den.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    I kartan `ready` händelsehanterare, inställningen traffic flow på kartan har angetts `relative`, vilket är hastigheten på vägen i förhållande till fritt flöde. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde.

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Om du interagera med kartan och zooma in på Los Angeles, bör du se gator med aktuella trafikdata.

   ![Visa trafikkarta](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien beräknas och renderas två vägar på kartan. En väg som använder fartleder som är tillgängliga för bilar och den andra som är tillgänglig för lastbilar. Vid rendering visar vi en symbolikon för början och slutet på vägen samt linjer med olika färger för varje vägfärd.

1. Efter initiering av kartan, lägger du till följande JavaScript-kod i mappningarna `ready` händelsehanterare.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
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
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    I mappningarna `ready` händelsehanterare, skapas en datakälla för att lagra vägen rader samt start- och punkter. Ett linjeskikt skapas och ansluts till datakällan för att definiera hur väglinjen ska renderas. Uttryck används för att hämta linjens bredd och färg från egenskaperna för väglinjefunktionen. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att radlinjen inte täcker för vägetiketterna. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start- och slutpunkterna renderas. I det här fallet har uttryck lagts till för att hämta information om ikonbild och textetikett från egenskaperna för varje punktobjekt. 
    
2. För den här självstudien ska du ange en startpunkt som ett fiktivt företag i Seattle som heter Fabrikam, och målplatsen som ett Microsoft-kontor. I mappningarna `ready` händelsehanterare, Lägg till följande kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
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

    Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Den här avgränsningsruta används för att ange kameror kartvyn över hela vägen med hjälp av den `map.setCamera` funktion. En utfyllnad läggs till för att kompensera för bildpunktsdimensionerna för symbolikonerna.

4. Spara filen och uppdatera webbläsaren om du vill se de fästen som visas på kartan. Nu är kartan centrerad över Seattle, och du kan se de runda blå kartnålarna som markerar startpunkten, och den blå kartnålen som markerar slutpunkten.

   ![Visa karta med start- och slutpunkter](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge rutter prioriterade efter färdmedel

Det här avsnittet visar hur du använder Maps route service API för att hitta flera vägar från en viss startpunkt till slutpunkten baserat på din transportsätt. Route Service tillhandahåller API:er för att planera den *snabbaste*, *kortaste*, *, miljövänligaste* eller *mest spännande* rutten mellan två platser, med hänsyn till aktuella trafikförhållanden. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Samtliga följande kodblock ska läggas till **i kartinläsningsfunktionen eventListener i kartan**, så att de laddas först när kartan har lästs in helt.

1. I funktionen GetMap lägger du till följande Javascript-kod.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Den `SubscriptionKeyCredential` skapar en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-förfrågningar till Azure Maps med prenumerationsnyckeln. Den `atlas.service.MapsURL.newPipeline()` tar den `SubscriptionKeyCredential` princip och skapar en [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instans. Den `routeURL` representerar en URL för Azure Maps [väg](https://docs.microsoft.com/rest/api/maps/route) åtgärder.

2. När du har installerat autentiseringsuppgifter och URL: en, Lägg till följande JavaScript kod för att skapa ett flöde från början till slutpunkt för en lastbil körts USHazmatClass2 har klassificerat lasten och visa resultatet.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Det här kodfragmentet ovan frågar Azure Maps-tjänst för händelsedirigering via den [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metod. Raden route sedan extraheras från samlingen GeoJSON-funktionen från svaret som extraheras med hjälp av den `geojson.getFeatures()` metoden. Raden route läggs sedan till datakällan. Det ger också ett index på 0 så att den renderas före andra rader i datakällan. Detta görs eftersom beräkningen av lastbilsvägen ofta tar längre tid än beräkningen för en bilväg, och om lastbilens väglinje läggs till i datakällan efter bilvägen renderas den före bilvägen. Två egenskaper läggs till i raden lastbil route, en linjefärg som är en fin blå och en linjebredd på nio bildpunkter.

3. Lägg till följande JavaScript-kod för att skapa en väg för en bil och visa resultaten.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Det här kodfragmentet ovan frågar Azure Maps-tjänst för händelsedirigering via den [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metod. Raden route sedan extraheras från samlingen GeoJSON-funktionen från svaret som extraheras med hjälp av den `geojson.getFeatures()` metoden. Raden route läggs sedan till datakällan. Två egenskaper läggs till i raden bil route, en linjefärg som är en färgton lila och en linjebredd på fem bildpunkter.  

4. Spara filen **MapTruckRoute.html** och uppdatera webbläsaren så att den visar resultatet. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande.

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Lastbilsvägen är blå och tjockare och bilvägen är lila och tunnare. Bilvägen sträcker sig över Lake Washington via I-90, som går genom tunnlarna under bostadsområdena och begränsar farlig avfallslast. Lastbilsvägen som anger en USHazmatClass2-lasttyp dirigeras korrekt för att använda en annan väg.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

> [!div class="nextstepaction"]
> [Visa fullständiga källkoden](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Se live-exempel](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Nästa självstudiekurs visar processen med att skapa en enkel butikslokaliserare med hjälp av Azure Maps.

> [!div class="nextstepaction"]
> [Skapa en butikslokaliserare med hjälp av Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Använda datadrivna style uttryck](data-driven-style-expressions-web-sdk.md)