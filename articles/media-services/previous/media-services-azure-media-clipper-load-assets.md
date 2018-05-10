---
title: Läsa in tillgångar i Azure Media Clipper | Microsoft Docs
description: Steg för att läsa in tillgångar i Azure Media Clipper
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="loading-assets-into-azure-media-clipper"></a>Läsa in tillgångar i Azure Media Clipper
Tillgångar kan läsas in i Azure Media Clipper av två metoder:
1. Skicka statiskt i ett bibliotek med resurser
2. Dynamiskt Generera en lista över resurser via API

## <a name="statically-load-videos-into-clipper"></a>Läs in videor statiskt till Clipper
Läs in videor statiskt i Clipper så att användarna att skapa klipp utan att välja videor från panelen tillgången val.

I så fall måste skicka du i en statisk uppsättning tillgångar till Clipper. Varje tillgång innehåller en AMS tillgången/filter-ID, namn, publicerade strömnings-URL. Om tillämpligt, en innehållsskydd autentiseringstoken eller en matris med miniatyr URL: er kan överföras. Om skickades i fylls miniatyrerna i gränssnittet. I scenarier där resursbiblioteket är statiska och liten, kan du överföra i tillgångsinformation kontraktet för varje resurs i biblioteket.

> [!NOTE]
> När du läser in tillgångar statiskt till Clipper tillgångar läggs **direkt till en tidslinje** och **tillgången fönstret återges inte**. Första tillgången har lagts till i tidslinjen och resten av tillgångar är staplade på höger sida av tidslinjen).

Om du vill läsa in en statisk resursbiblioteket, Använd den **ladda** metod för att skicka in en JSON-representation av varje tillgång. Följande kodexempel illustrerar JSON-representation för en tillgång.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Det rekommenderas att kedjan load ()-metoden anropar med metoden ready(handler) som visas i föregående exempel. Föregående exempel garanterar att widgeten är klar innan du läser in tillgångar.

> [!NOTE]
> För miniatyren URL: er som fungerar som förväntat i tidslinjen Clipper de måste vara jämnt fördelad över videon (baserat på varaktigheten) och i kronologisk ordning i matrisen. Du kan använda följande JSON förinställda kodavsnitt som exempel referens för att skapa bilder med processorn ”Media Encoder Standard”:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>För att läsa in videor i Clipper
Läs in videor dynamiskt i Clipper så att användarna att välja videor från panelen tillgången valet att Beskär mot.

Alternativt kan du läsa in tillgångar dynamiskt via ett återanrop. I scenarier där tillgångar genereras dynamiskt eller biblioteket är stort, bör du läsa via återanropet. Om du vill läsa in tillgången dynamiskt, måste du implementera Återanropsfunktionen valfria onLoadAssets. Den här funktionen har skickats till Clipper vid initieringen. Löst tillgångar bör följa samma kontrakt som läses in statiskt tillgångar. Följande kodexempel illustrerar Metodsignaturen, förväntade indata och utdata som förväntas.

> [!NOTE]
> När dynamiskt vill läsa in tillgångar i Clipper tillgångar återges i den **tillgången markeringen panelen**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```