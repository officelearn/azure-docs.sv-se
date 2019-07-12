---
title: Skapa filter med Azure Media Services REST API | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att uppnå den här selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786457"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Skapa filter med Media Services REST API

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du definierar ett filter för en Video på begäran tillgången och använda REST API: er för att skapa [kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Förutsättningar 

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).

    Se till att följa det sista steget i avsnittet [hämta Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definiera ett filter  

Följande är de **Begärandetext** exempel som definierar de spåra val av villkor som läggs till i manifestet. Det här filtret innehåller alla ljudspår som är EG-3 och alla video spår har bithastighet i 0-1000000 intervall.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Skapa kontofilter

I den Postman-samling som du laddade ned, väljer **kontofilter**->**skapa eller uppdatera en kontofilter**.

Den **PLACERA** HTTP-frågemetoden liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Välj den **brödtext** fliken och klistra in json kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Filtret har skapats.

Mer information finns i [skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa filter för tillgången  

I ”Media Services v3” Postman-samlingen som du laddade ned, väljer **tillgångar**->**skapa eller uppdatera tillgången Filter**.

Den **PLACERA** HTTP-frågemetoden liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Välj den **brödtext** fliken och klistra in json kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Tillgången filtret har skapats.

Mer information om hur du skapar eller uppdaterar tillgången filter finns [skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med Strömningspositionerare

Du kan ange en lista över tillgång eller konto filter som skulle gälla för dina Strömningspositionerare. Den [dynamisk Paketeraren (slutpunkt för direktuppspelning)](dynamic-packaging-overview.md) gäller den här listan över filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar en [dynamiska Manifest](filters-dynamic-manifest-overview.md), som grundar sig på filter i URL: en + filter som du anger på Strömningspositionerare. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill exponera filternamn i URL: en.

För att skapa och koppla filter till en Strömningspositionerare med hjälp av REST, använda den [positionerare för direktuppspelning - skapa](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API och ange `properties.filters` i den [Begärandetext](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream med hjälp av filter

När du har definierat filter kan klienterna använda dem i strömnings-URL. Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protocol|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-rest.md) 
