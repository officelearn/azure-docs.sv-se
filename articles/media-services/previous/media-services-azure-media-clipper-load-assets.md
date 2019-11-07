---
title: Läs in till gångar i Azure Media Clipper | Microsoft Docs
description: Steg för att läsa in till gångar i Azure Media Clipper
services: media-services
keywords: klipp; subclip; encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685040"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Läsa in till gångar i Azure Media Clipper  

Till gångar kan läsas in i Azure Media Clipper på två sätt:
1. Överföra statiskt i ett bibliotek med till gångar
2. Skapa en lista över till gångar dynamiskt via API

## <a name="statically-load-videos-into-clipper"></a>Läs in videor statiskt i Clipper
Läs in videor statiskt i Clipper och gör det möjligt för slutanvändare att skapa klipp utan att välja Videor från panelen till gångs val.

I det här fallet skickar du en statisk uppsättning till gångar till Clipper. Varje till gång innehåller en AMS till gång/filter-ID, namn, publicerad strömnings-URL. I förekommande fall kan en autentiseringstoken för innehålls skydd eller en matris med URL: er för miniatyrer överföras. Om det skickas i, fylls miniatyrerna i i gränssnittet. I scenarier där till gångs biblioteket är statiskt och litet kan du överföra till gångs kontraktet för varje till gång i biblioteket.

> [!NOTE]
> När du läser in till gångar statiskt i Clipper läggs till gångar **direkt till i tids linjen** och **till gångs fönstret återges inte**. Den första till gången läggs till i tids linjen och resten av till gångarna staplas på höger sida av tids linjen.

Om du vill läsa in ett statiskt resurs bibliotek använder du metoden **load** för att skicka en JSON-representation av varje till gång. Följande kod exempel illustrerar JSON-representationen för en till gång.

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
> Vi rekommenderar att du kedjar metod anropet load () med den färdiga (hanterare) metoden som visas i föregående exempel. Föregående exempel garanterar att widgeten är klar innan du läser in till gångarna.

> [!NOTE]
> För att miniatyr-URL: erna ska fungera som förväntat i Clipper-tidslinje måste de fördelas jämnt över videon (baserat på varaktigheten) och i kronologisk ordning inom matrisen. Du kan använda följande JSON-förvalda fragment som en exempel referens för att skapa avbildningar med processorn Media Encoder Standard:

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

## <a name="dynamically-load-videos-in-clipper"></a>Ladda videor dynamiskt i Clipper
Läs in videor dynamiskt i Clipper om du vill att slutanvändare ska kunna välja Videor från panelen till gångs val.

Du kan också läsa in till gångar dynamiskt via ett återanrop. I scenarier där till gångar dynamiskt genereras eller om biblioteket är stort, bör du läsa in via återanropet. Om du vill läsa in till gången dynamiskt måste du implementera den valfria återanrops funktionen onLoadAssets. Den här funktionen överförs till Clipper vid initiering. Matchade till gångar bör följa samma kontrakt som statiskt inlästa till gångar. Följande kod exempel illustrerar Metodsignaturen, förväntade indata och förväntade utdata.

> [!NOTE]
> När du läser in till gångar dynamiskt i Clipper, återges till gångar i **panelen till gångs urval**.

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
