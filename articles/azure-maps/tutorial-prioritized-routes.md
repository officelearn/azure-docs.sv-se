---
title: 'Handledning: Hitta flera rutter efter färdsätt | Microsoft Azure Maps'
description: I den här självstudien får du lära dig hur du hittar vägar för olika reselägen med Hjälp av Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333791"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Självstudiekurs: Hitta vägar för olika reselägen med Azure Maps

Den här självstudien visar hur du använder ditt Azure Maps-konto och vägtjänsten. Rutttjänsten kan hitta rutten till din intressanta punkt, prioriterad av ditt färdsätt. Du kan visa två olika rutter på kartan, en för bilar och en för lastbilar. Färdtjänsten tar hänsyn till begränsningar på grund av fordonets längd och vikt eller om fordonet transporterar farlig last. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

## <a name="prerequisites"></a>Krav
Innan du fortsätter följer du instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och väljer prisnivån S1. Följ stegen för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](how-to-manage-authentication.md).

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
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

    Klassen `atlas.Map` ger kontrollen för en visuell och interaktiv webbkarta och är en komponent i Azure Map Control API.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisera trafikflödet

1. Lägga till displayen för trafikflöde i kartan. Händelsen `ready` Kartor väntar tills kartresurserna läses in och är redo att interagera säkert med den.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    I karthändelsehanteraren `ready` är trafikflödesinställningen på kartan `relative`inställd på , vilket är hastigheten på vägen i förhållande till fritt flöde. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde.

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Om du interagerar med kartan och zoomar in på Los Angeles bör du se gatorna med aktuella trafikdata.

   ![Visa trafik på en karta](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien beräknas och renderas två vägar på kartan. En väg som använder fartleder som är tillgängliga för bilar och den andra som är tillgänglig för lastbilar. När vi återges visar vi en symbolikon för början och slutet av rutten och olika färgade linjer för varje ruttsökväg.

1. När du har initierat kartan lägger du `ready` till följande JavaScript-kod i händelsehanteraren kartor.

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
    
    I händelsehanteraren kartor `ready` skapas en datakälla för att lagra flödesraderna och start- och slutpunkterna. Ett linjeskikt skapas och ansluts till datakällan för att definiera hur väglinjen ska renderas. Uttryck används för att hämta linjens bredd och färg från egenskaperna för väglinjefunktionen. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att ruttlinjen inte täcker vägmärkena. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start- och slutpunkterna ska återges. I det här fallet har uttryck lagts till för att hämta information om ikonbild och textetikett från egenskaper på varje punktobjekt. 
    
2. För den här självstudien ska du ange en startpunkt som ett fiktivt företag i Seattle som heter Fabrikam, och målplatsen som ett Microsoft-kontor. Lägg till `ready` följande kod i händelsehanteraren kartor.

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

    Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Den här begränsningsramen används för att ställa in `map.setCamera` kartkamerorna över hela rutten med hjälp av funktionen. En utfyllnad läggs till för att kompensera för bildpunktsdimensionerna för symbolikonerna.

4. Spara filen och uppdatera webbläsaren om du vill se de fästen som visas på kartan. Nu kartan är centrerad över Seattle. Du kan se den runda blå stiftet som markerar startpunkten och den blå stiftet som markerar slutpunkten.

   ![Visa karta med start- och slutpunkter](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge vägar prioriterade efter färdmedel

I det här avsnittet visas hur du använder API:et för maps-dirigerartjänsten. Flödes-API:et används för att hitta flera vägar från en viss startpunkt till slutpunkten, baserat på ditt transportsätt. Rutttjänsten tillhandahåller API:er för att planera *snabbaste,* *kortaste,* *eco*eller *spännande* rutter. Api:erna planerar inte bara rutter mellan två platser, utan de tar också hänsyn till de aktuella trafikförhållandena. 

Med väg-API:et kan användare planera vägar i framtiden med hjälp av Azures omfattande historiska trafikdatabas. API:et kan förutsäga varaktigheter för dirigerar för en viss dag och tid. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Alla följande kodblock bör läggas till **i kartbelastningshändelsenListener** för att säkerställa att de läses in när kartan har laddats in helt.

1. Lägg till följande i Javascript-koden i funktionen GetMap.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Skapar `SubscriptionKeyCredential` en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-begäranden till Azure Maps med prenumerationsnyckeln. Tar `atlas.service.MapsURL.newPipeline()` i `SubscriptionKeyCredential` principen och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `routeURL` en URL till Azure Maps [Route-åtgärder.](https://docs.microsoft.com/rest/api/maps/route)

2. När du har ställt in autentiseringsuppgifter och URL lägger du till följande JavaScript-kod för att skapa en rutt från start till slutpunkt för en lastbil som transporterar USHazmatClass2-klassad last och visar resultaten.

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

    Det här kodavsnittet ovan frågar routningstjänsten För Azure Maps via metoden [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Flödesraden extraheras sedan från GeoJSON-funktionssamlingen från `geojson.getFeatures()` svaret som extraheras med metoden. Flödesraden läggs sedan till i datakällan. Ett index på 0 säkerställer att det återges före andra rader i datakällan. Detta görs eftersom beräkningen av lastbilsrutten ofta blir långsammare än en beräkning av bilrutten. Om lastbilsruttlinjen läggs till i datakällan efter bilvägen återges den ovanför den. Två egenskaper läggs till lastbilsruttlinjen, en linjefärg som är en fin nyans av blått och en linjebredd på nio pixlar.

3. Lägg till följande JavaScript-kod för att skapa en rutt för en bil och visa resultaten.

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

    Det här kodavsnittet ovan frågar routningstjänsten För Azure Maps via metoden [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Flödesraden extraheras sedan från GeoJSON-funktionssamlingen från `geojson.getFeatures()` svaret som extraheras med metoden. Flödesraden läggs sedan till i datakällan. Två egenskaper läggs till på bilruttlinjen, en linjefärg som är en nyans av lila och en linjebredd på fem bildpunkter.  

4. Spara filen **MapTruckRoute.html** och uppdatera webbläsaren så att den visar resultatet. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande.

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Lastbilsrutten är tjockblå och bilvägen är tunn lila. Bilvägen går över Lake Washington via I-90, som går genom tunnlar under bostadsområden. Eftersom tunnlarna ligger nära bostadsområden är gods av farligt avfall begränsat. Lastbilsrutten, som anger en USHazmatClass2-lasttyp, är riktad att använda en annan motorväg.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

> [!div class="nextstepaction"]
> [Visa fullständig källkod](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Visa direktexempel](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Nästa självstudiekurs visar processen med att skapa en enkel butikslokaliserare med hjälp av Azure Maps.

> [!div class="nextstepaction"]
> [Skapa en butikslokaliserare med hjälp av Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)