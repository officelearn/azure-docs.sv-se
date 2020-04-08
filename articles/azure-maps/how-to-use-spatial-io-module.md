---
title: Så här använder du azure maps rumsliga IO-modulen | Microsoft Azure Maps
description: Lär dig hur du använder den spatiala IO-modulen som tillhandahålls av Azure Maps Web SDK. Den här modulen innehåller robusta funktioner för att göra det enkelt för utvecklare att integrera spatialdata med Azure Maps web sdk.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804647"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Så här använder du Azure Maps Spatial IO-modulen

Azure Maps Web SDK tillhandahåller den **rumsliga IO-modulen**, som integrerar rumsliga data med Azure Maps web SDK med JavaScript eller TypeScript. De robusta funktionerna i den här modulen gör det möjligt för utvecklare att:

- [Läs och skriv vanliga rumsliga datafiler](spatial-io-read-write-spatial-data.md). Filformat som stöds inkluderar: KML, KMZ, GPX, GeoRSS, GML, GeoJSON och CSV filer som innehåller kolumner med rumslig information. Stöder även välkänd text (WKT).
- [Anslut till OGC-tjänster (Open Geospatial Consortium) och integrera med Azure Maps web SDK. Overlay Web Map Services (WMS) och Web Map Tile Services (WMTS) som lager på kartan](spatial-io-add-ogc-map-layer.md).
- [Fråga data i en WFS -tjänst (Web Feature Service).](spatial-io-connect-wfs-service.md)
- [Överlagra komplexa datauppsättningar som innehåller formatinformation och låta dem återges automatiskt med minimal kod](spatial-io-add-simple-data-layer.md).
- [Utnyttja xml-filer med hög hastighet och avgränsade filläsare och skrivklasser](spatial-io-core-operations.md).

I den här guiden får vi lära oss att integrera och använda spatial IO-modulen i ett webbprogram.

Den här videon innehåller en översikt över Spatial IO-modulen i Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Integrera enkelt spatialdata i Azure Maps – Microsoft Channel 9 Video"></iframe>


> [!WARNING]
> Använd endast data och tjänster som kommer från en källa som du litar på, särskilt om du refererar till dem från en annan domän. Den rumsliga IO-modulen vidtar åtgärder för att minimera risken, men det säkraste tillvägagångssättet är för inte tillåta några danagerous data i ditt program till att börja med. 

## <a name="prerequisites"></a>Krav

Innan du kan använda spatial IO-modulen måste du [skapa ett Azure Maps-konto](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) och [få den primära prenumerationsnyckeln för ditt konto](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Installera spatial IO-modulen

Du kan läsa in Azure Maps spatial IO-modulen med något av de två alternativen:

* Den globalt värd Azure CDN för Azure Maps rumsliga IO-modulen. För det här alternativet lägger du till `<head>` en referens till JavaScript i elementet i HTML-filen.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Källkoden för [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) kan läsas in lokalt och sedan vara värd med din app. Det här paketet innehåller även TypeScript-definitioner. Använd följande kommando för det här alternativet för att installera paketet:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Lägg sedan till en referens till `<head>` JavaScript i elementet i HTML-dokumentet:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Använda modulen Spatial IO

1. Skapa en ny HTML-fil.

2. Läs in Azure Maps Web SDK och initiera kartkontrollen. Mer information finns i [kartkontrollguiden](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) för Azure Maps. När du är klar med det här steget bör HTML-filen se ut så här:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Läs in Azure Maps spatial IO-modulen. Använd cdn för azure maps spatial IO-modulen för den här övningen. Lägg till referensen `<head>` nedan till elementet i HTML-filen:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Initiera en `datasource`och lägg till datakällan på kartan. Initiera en `layer`och lägg till datakällan i kartlagret. Rendera sedan både datakällan och lagret. Innan du bläddrar nedåt för att se den fullständiga koden i nästa steg bör du tänka på de bästa ställena att placera datakäll- och lagerkodavsnitten. Kom ihåg att innan vi programmatiskt manipulerar kartan, bör vi vänta tills kartresursen är klar.

    ```javascript
    var datasource, layer;
    ```

    och

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Genom att sätta ihop allt bör HTML-koden se ut som följande kod. Det här exemplet visar hur du läser en XML-fil från en URL. Läs sedan in och visa filens funktionsdata på kartan. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Kom ihåg `<Your Azure Maps Key>` att ersätta med din primära nyckel. Öppna HTML-filen så visas resultat som liknar följande bild:

    <center>

    ![Exempel på rumsliga data](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Nästa steg

Funktionen vi visade här är bara en av de många funktioner som finns i Spatial IO-modulen. Läs guiderna nedan om du vill lära dig hur du använder andra funktioner i spatial IO-modulen:

> [!div class="nextstepaction"]
> [Lägga till ett enkelt datalager](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Läsa och skriva rumsliga data](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Lägga till ett OGC-kartlager](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärnverksamheten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)

Se Azure Maps Spatial IO-dokumentation:

> [!div class="nextstepaction"]
> [Spatial IO-paket för Azure Maps](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
