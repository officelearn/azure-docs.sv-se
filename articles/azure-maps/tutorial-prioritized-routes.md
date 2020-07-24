---
title: 'Självstudie: Sök efter flera vägar i färd läge | Microsoft Azure Maps'
description: I den här självstudien får du lära dig hur du hittar vägar för olika färd sätt med hjälp av Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 94ad89c29ac3a9193b81e4d64e6253b826c1eda1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075393"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Självstudie: hitta vägar för olika färd sätt med hjälp av Azure Maps

I den här självstudien visas hur du använder ditt Azure Maps-konto och Route service. Route service kan hitta vägen till din intresse punkt, prioriterad av ditt rese läge. Du kan visa två olika vägar på kartan, en för bilar och en för Last bilar. Routningstjänsten tar hänsyn till begränsningar på grund av fordonets höjd och vikt, eller om fordonet bär farligt gods. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

## <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-azure-maps-account) och väljer pris nivån S1. Följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

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

3. Lägg till följande JavaScript-kod i funktionen `GetMap`. Ersätt strängen `<Your Azure Maps Key>` med den primära nyckel som du kopierade från ditt Maps-konto.

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

    `atlas.Map`Klassen ger kontrollen för en visuell och interaktiv webb karta och är en komponent i Azure Kartkontroll-API: et.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisera trafikflödet

1. Lägga till displayen för trafikflöde i kartan. Maps- `ready` händelsen väntar tills Maps-resurserna har lästs in och är redo att interagera med den.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    I kartans `ready` händelse hanterare anges trafik flödes inställningen på kartan `relative` , vilket är hastigheten på vägen i förhållande till det fria flödet. Du kan också ange den till `absolute` hastighet på vägen, eller `relative-delay` som visar den relativa hastighet där den skiljer sig från fritt flöde.

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Om du interagerar med kartan och zoomar in till Los Angeles, bör du se gator med aktuella trafikdata.

   ![Visa trafik på en karta](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien beräknas och renderas två vägar på kartan. En väg som använder fartleder som är tillgängliga för bilar och den andra som är tillgänglig för lastbilar. När det återges visas en symbol ikon för vägens början och slut, och olika färgade linjer för varje väg Sök väg.

1. När du har initierat kartan lägger du till följande JavaScript-kod i mappnings `ready` händelse hanteraren.

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
    
    I mappar `ready` händelse hanteraren skapas en data källa för att lagra väg raderna och start-och slut punkterna. Ett linjeskikt skapas och ansluts till datakällan för att definiera hur väglinjen ska renderas. Uttryck används för att hämta linjens bredd och färg från egenskaperna för väglinjefunktionen. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att väg linjen inte besvarar väg etiketterna. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start-och slut punkterna ska renderas. I det här fallet har uttryck lagts till för att hämta ikon bilden och text etiketts information från egenskaper för varje punkt objekt. 
    
2. För den här självstudien ska du ange en startpunkt som ett fiktivt företag i Seattle som heter Fabrikam, och målplatsen som ett Microsoft-kontor. `ready`Lägg till följande kod i händelse hanteraren för Maps.

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

    Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Den här markerings rutan används för att ställa in kart kameror för visning över hela vägen med hjälp av `map.setCamera` funktionen. En utfyllnad läggs till för att kompensera för bildpunktsdimensionerna för symbolikonerna.

4. Spara filen och uppdatera webbläsaren om du vill se de fästen som visas på kartan. Nu centreras kartan över Seattle. Du kan se den runda blå PIN-koden för att markera start punkten och den blå PIN-koden för att markera slut punkten.

   ![Visa karta med start- och slutpunkter](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Återge vägar prioriterade efter färdmedel

I det här avsnittet visas hur du använder Maps Route service-API. Route API används för att hitta flera vägar från en viss start punkt till slut punkten, baserat på ditt transport sätt. Route service innehåller API: er för att planera *snabbast*, *kortaste*, *eko*eller *thrilLing* vägar. Det går inte bara att planera API: erna genom att planera mellan två platser, men de anser också de aktuella trafik villkoren. 

Med väg-API: et kan användare planera vägar i framtiden med hjälp av Azures omfattande historiska trafik databas. API: et kan förutsäga vägens varaktigheter för en specifik dag och tid. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Alla följande kodblock ska läggas till **i eventListener för kart läsning** för att säkerställa att de läses in när kartan har lästs in helt.

1. I GetMap-funktionen lägger du till följande till JavaScript-kod.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Skapar en `SubscriptionKeyCredentialPolicy` för att autentisera HTTP-begäranden till Azure Maps med prenumerations nyckeln. `atlas.service.MapsURL.newPipeline()`Principen tar i `SubscriptionKeyCredential` principen och skapar en [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -instans. `routeURL`Representerar en URL som Azure Maps [väg](https://docs.microsoft.com/rest/api/maps/route) åtgärder.

2. När du har angett autentiseringsuppgifter och URL: en lägger du till följande JavaScript-kod för att skapa en väg från start till slut punkt för en truck som bär USHazmatClass2-klassa last och visa resultatet.

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

    Det här kodfragmentet ovan frågar Azure Maps routningstjänsten via [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) -metoden. Flödes raden extraheras sedan från insamlingen för polyjson-funktionen från svaret som extraheras med hjälp av `geojson.getFeatures()` metoden. Väg linjen läggs sedan till i data källan. Ett index på 0 garanterar att det återges före andra rader i data källan. Detta görs eftersom väg beräkningen för Last bilar är ofta långsammare än en flödes beräkning. Om väg linjen för trucken läggs till i data källan efter Car-vägen, kommer den att visas ovanför den. Två egenskaper läggs till i väg linjen för trucken, en linje färg som är en bra nyans av blått och en linje bredd på nio bild punkter.

3. Lägg till följande JavaScript-kod för att skapa en väg för en bil och visa resultatet.

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

    Det här kodfragmentet ovan frågar Azure Maps routningstjänsten via [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) -metoden. Flödes raden extraheras sedan från insamlingen för polyjson-funktionen från svaret som extraheras med hjälp av `geojson.getFeatures()` metoden. Väg linjen läggs sedan till i data källan. Två egenskaper läggs till i vagn linje linjen, en linje färg som är en lila färg och en linje bredd på fem bild punkter.  

4. Spara filen **MapTruckRoute.html** och uppdatera webbläsaren så att den visar resultatet. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande.

    ![Prioriterade rutter med Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Truck-vägen är tjock blå och bilen är tunn lila. Car-vägen går över Lake Washington via I-90, som går genom tunnlar under bostads områden. Eftersom tunnlarna ligger nära bostads områden är farligt avfall Last begränsat. Truck-vägen, som anger en USHazmatClass2 Last typ, dirigeras till att använda en annan väg.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Visualisera trafikflödet på kartan
> * Skapa vägfrågor som deklarerar resläge
> * Visa flera vägar på kartan

> [!div class="nextstepaction"]
> [Visa fullständig käll kod](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Visa Live-exempel](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Nästa självstudiekurs visar processen med att skapa en enkel butikslokaliserare med hjälp av Azure Maps.

> [!div class="nextstepaction"]
> [Skapa en butikslokaliserare med hjälp av Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)