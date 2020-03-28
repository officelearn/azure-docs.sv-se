---
title: 'Självstudiekurs: Hitta vägen till en plats | Microsoft Azure Maps'
description: Den här självstudien visar hur du återger vägen till en plats (intressepunkt) på en karta med hjälp av Microsoft Azure Maps Routing Service.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333768"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Självstudiekurs: Vägen till en intressant punkt med Hjälp av Azure Maps

Den här självstudiekursen visar hur du använder Azure Maps-kontot och Route Service SDK för att hitta rutten till en orienteringspunkt. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Ställa in adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

## <a name="prerequisites"></a>Krav

Innan du fortsätter behöver du följa instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps), du behöver en prenumeration med S1-prisnivå. Följ stegen för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta

Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapRoute.html**.
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

    `atlas.Map` ger kontroll över en visuell och interaktiv webbkarta och är en komponent i Azure Kartkontroll-API:et.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien renderas en enkel väg med hjälp av en symbolikon för början och slutet av vägen och en linje för vägens färd.

1. När du har initierat kartan lägger du till följande JavaScript-kod.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    I händelsehanteraren kartor `ready` skapas en datakälla för att lagra flödesraden och start- och slutpunkterna. Ett linjeskikt skapas och ansluts till datakällan för att definiera hur väglinjen ska renderas. Rutten linjen kommer att återges som en fin nyans av blått. Den har en bredd på fem pixlar, rundade linjekopplingar och lock. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att radlinjen inte täcker för vägetiketterna. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start- och slutpunkterna återges. I det här fallet har uttryck lagts till för att hämta information om ikonbild och textetikett från egenskaper på varje punktobjekt. 
    
2. För den här självstudien anger du startpunkt som Microsoft, och målpunkt som en bensinstation i Seattle. Lägg till `ready` följande kod i händelsehanteraren kartor.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Den här koden skapar två [GeoJSON Point-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten och lägger till punkterna i datakällan. Egenskaperna `title` och `icon` har lagts till i varje punkt. Det sista blocket ställer in kameravyn med start- och slutpunkternas latitud och longitud med hjälp av kartans [setCamera-egenskap.](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

3. Spara filen **MapRoute.html** och uppdatera webbläsaren. Nu kartan är centrerad över Seattle, och du kan se den blå stiftet som markerar startpunkten och den runda blå stift som markerar slutpunkten.

   ![Visa rutter start- och slutpunkt på kartan](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Hämta anvisningar

Det här avsnittet visar hur du använder Azure Maps route service API. Vägtjänstens API hittar vägen från en given startpunkt till en slutpunkt. Inom denna tjänst finns det API:er för att planera *snabbaste,* *kortaste,* *eco*eller *spännande* rutter mellan två platser. Med den här tjänsten kan användare också planera vägar i framtiden med hjälp av Azures omfattande historiska trafikdatabas. Användare kan se förutsägelsen av ruttens varaktigheter när som helst vald dag och tid. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Alla följande funktioner bör läggas till **i kartan redo eventListener** för att säkerställa att de läser in när kartresurserna är redo att nås.

1. Lägg till följande i JavaScript-koden i funktionen GetMap.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Skapar `SubscriptionKeyCredential` en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-begäranden till Azure Maps med prenumerationsnyckeln. Tar `atlas.service.MapsURL.newPipeline()` i `SubscriptionKeyCredential` principen och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `routeURL` en URL till Azure Maps [Route-åtgärder.](https://docs.microsoft.com/rest/api/maps/route)

2. När du har konfigurerat autentiseringsuppgifter och URL:en lägger du till följande JavaScript-kod för att skapa vägen från startpunkt till slutpunkt. Begäranden `routeURL` om azure maps-rutttjänsten för att beräkna vägbeskrivningar. En GeoJSON-funktionssamling från svaret extraheras sedan med `geojson.getFeatures()` hjälp av metoden och läggs till i datakällan.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Spara filen **MapRoute.html** och uppdatera webbläsaren. För en lyckad anslutning med den API:er i Maps bör du se en karta som liknar följande.

    ![Azure Kartkontroll och Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Ställa in adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

> [!div class="nextstepaction"]
> [Visa fullständig källkod](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Visa direktexempel](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Nästa självstudie visar hur du skapar en vägfråga med begränsningar som resläge eller typ av last och visar sedan flera vägar på samma karta.

> [!div class="nextstepaction"]
> [Hitta rutter för olika färdmedel](./tutorial-prioritized-routes.md)
