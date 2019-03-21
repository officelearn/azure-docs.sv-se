---
title: Hitta vägen med Azure Maps | Microsoft Docs
description: Rutt till en orienteringspunkt med hjälp av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b17a9660e16a1cb05c088e97d4ad18dd20fd4216
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588797"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Rutt till en orienteringspunkt med hjälp av Azure Maps

Den här självstudiekursen visar hur du använder Azure Maps-kontot och Route Service SDK för att hitta rutten till en orienteringspunkt. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny webbsida med API:n för kartkontroll
> * Ställa in adresskoordinater
> * Fråga Route Service om vägbeskrivning till orienteringspunkt

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter ska du följa stegen i föregående självstudie för att [skapa ditt Azure Maps-konto](./tutorial-search-location.md#createaccount) och [hämta prenumerationsnyckeln för ditt konto](./tutorial-search-location.md#getkey).

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
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>

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

3. Lägg till följande JavaScript-kod i funktionen `GetMap`. Ersätt strängen **\<Din Azure Maps-nyckel\>** med den primärnyckel som du kopierade från Maps-kontot.

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

    **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta och är en komponent i API:et Azure Kartkontroll.

4. Spara filen och öppna den i webbläsaren. Du har nu en grundläggande karta som du kan utveckla mer.

   ![Visa grundläggande karta](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definiera hur vägen ska renderas

I den här självstudien renderas en enkel väg med hjälp av en symbolikon för början och slutet av vägen och en linje för vägens färd.

1. Efter initiering av kartan, lägger du till följande JavaScript-kod.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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

    En inläsningshändelse har lagts till på kartan, som utlöses när kartresurserna har lästs in helt. I händelsehanteraren för kartinläsningen skapas en datakälla för att lagra väglinjen samt start- och slutpunkterna. En linjeskikt skapas och ansluts till datakällan för att definiera hur raden route återges. Raden route återges i en färgton blå med 5 bildpunkter och avrundade rad kopplingar och caps bredden. Ett filter läggs till för att säkerställa att det här lagret endast renderar GeoJSON LineString-data. När du lägger till skiktet på kartan skickas en andra parameter med värdet `'labels'`, där det anges att det här lagret ska renderas under kartetiketterna. Detta säkerställer att radlinjen inte täcker för vägetiketterna. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur start- och slutpunkterna renderas. I det här fallet har uttryck lagts till för att hämta information om ikonbild och textetikett från egenskaperna för varje punktobjekt.

2. Den här självstudien anger du startpunkten till Microsoft campus och slutpunkten som en Bensinstation i Seattle. I händelsehanteraren för kartinläsning lägger du till följande kod.

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

    Den här koden skapar två [GeoJSON Point-objekt](https://en.wikipedia.org/wiki/GeoJSON) som representerar start- och slutpunkterna för rutten och lägger till punkterna till datakällan. Egenskaperna `title` och `icon` har lagts till i varje punkt. Det sista blocket anger kameravyn med latitud och longitud informationen från start- och slutpunkterna, med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) egenskapen.

3. Spara filen **MapRoute.html** och uppdatera webbläsaren. Nu kartan centreras över Seattle och du kan se blå PIN-koden Markera startpunkten och runda blå PIN-koden genom att markera den Slutför punkten.

   ![Visa karta med start- och slutpunkterna markerade](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Hämta anvisningar

Det här avsnittet visar hur du använder Azure Maps route service API för att hitta rutten från en viss startpunkt till slutpunkten. Route Service tillhandahåller API:er för att planera den *snabbaste*, *kortaste*, *, miljövänligaste* eller *mest spännande* rutten mellan två platser. Användare kan även planera rutter i framtiden genom att använda Azures omfattande historiska trafikdatabas för att förutsäga hur snabba olika rutter är på olika dagar och tidpunkter. Mer information finns i [Hämta väganvisningar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Samtliga följande funktioner ska läggas till **i kartinläsningsfunktionen eventListener i kartan**, så att de laddas först när kartan har lästs in helt.

1. I funktionen GetMap lägger du till följande Javascript-kod.

    ```Javascript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```
   Den **SubscriptionKeyCredential** skapar en **SubscriptionKeyCredentialPolicy** att autentisera HTTP-förfrågningar till Azure Maps med prenumerationsnyckeln. Den **atlas.service.MapsURL.newPipeline()** tar den **SubscriptionKeyCredential** princip och skapar en [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instans. Den **routeURL** representerar en URL för Azure Maps [väg](https://docs.microsoft.com/rest/api/maps/route) åtgärder.

2. När du har installerat autentiseringsuppgifter och URL: en, lägger du till följande JavaScript-kod för att konstruera vägen från start till slutpunkten. Den **routeURL** begäranden Azure Maps dirigera tjänsten för att beräkna riktningar i vägen. En samling med GeoJSON-funktionen från svaret extraheras sedan med hjälp av den **geojson.getFeatures()** metod och läggs till i datakällan.

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

Du kan komma åt kodexemplet för den här självstudien här:

> [Hitta vägen med Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Se detta exempel live här](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Nästa självstudie visar hur du skapar en vägfråga med begränsningar som resläge eller typ av last och visar sedan flera vägar på samma karta.

> [!div class="nextstepaction"]
> [Hitta rutter för olika färdmedel](./tutorial-prioritized-routes.md)
