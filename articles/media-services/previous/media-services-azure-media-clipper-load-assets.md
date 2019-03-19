---
title: Läsa in tillgångar till Azure Media Clipper | Microsoft Docs
description: Steg för att läsa in tillgångar i Azure Media Clipper
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec8cd06be78bbd8df0bca390696e736c3a6ee075
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002626"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Läsa in tillgångar i Azure Media Clipper  

Tillgångar kan läsas in i Azure Media Clipper av två metoder:
1. Statiskt skicka i ett bibliotek med tillgångar
2. Dynamiskt Generera en lista över tillgångar via API: et

## <a name="statically-load-videos-into-clipper"></a>Statiskt läsa in videor i Clipper
Läs in videor i Clipper så att användarna kan skapa klipp utan att välja videor från panelen för val av tillgången statiskt.

I det här fallet skicka du in en statisk uppsättning tillgångar till Clipper. Varje tillgång innehåller en AMS tillgången/filter-ID, namn, publicerade strömnings-URL. Om så är tillämpligt, en Autentiseringstoken för innehållsskydd eller en matris med miniatyr URL: er kan också skickas. Om du i, fylls miniatyrbilderna i inloggningsgränssnittet. I scenarier där resursbiblioteket är statiska och små, kan du skicka tillgången kontraktet för varje resurs i biblioteket.

> [!NOTE]
> Vid inläsning av tillgångar statiskt till Clipper tillgångar läggs **direkt till tidslinjen** och **tillgången fönstret återges inte**. Den första tillgången har lagts till i tidslinjen och resten av resurserna som staplats på höger sida av tidslinjen).

Läs in en statisk resursbiblioteket genom att använda den **läsa in** metod för att skicka in en JSON-representation för varje tillgång. Följande kodexempel visar JSON-representation för en tillgång.

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
> Vi rekommenderar att kedja load ()-metod som anropar med metoden ready(handler) som visas i föregående exempel. I föregående exempel garanterar att widgeten är klar innan du läser in tillgångar.

> [!NOTE]
> För miniatyrbilden URL: er som fungerar som förväntat på tidslinjen för Clipper som de måste jämnt fördelad över videon (baserat på) och i kronologisk ordning inom matrisen. Du kan använda följande förinställda JSON-kodfragment som en exempel-referens för att skapa avbildningar med processorn ”Media Encoder Standard”:

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
Läs in videor i Clipper så att användarna att välja videor från panelen för val av tillgången till clip mot dynamiskt.

Du kan också läsa in tillgångar dynamiskt via en motringning. I scenarier där tillgångar genereras dynamiskt eller biblioteket är stor, bör du läsa in via motringningen. För att läsa in tillgången dynamiskt, måste du implementera den valfria onLoadAssets Återanropsfunktionen. Den här funktionen skickas till Clipper vid initieringen. Löst tillgångar bör följa samma kontraktet som läses in statiskt tillgångar. Följande kodexempel illustrerar de Metodsignaturen, förväntade indata och utdata som förväntas.

> [!NOTE]
> Vid inläsning av tillgångar dynamiskt till Clipper tillgångar återges i den **tillgången panelen för val av**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
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
