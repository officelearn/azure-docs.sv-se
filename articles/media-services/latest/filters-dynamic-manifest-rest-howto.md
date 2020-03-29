---
title: Skapa filter med Azure Media Services v3 REST API
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma specifika avsnitt i en ström. Media Services skapar dynamiska manifest för att uppnå denna selektiva strömning.
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
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780342"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Skapa filter med REST API FÖR Media Services

När du levererar ditt innehåll till kunder (strömma livehändelser eller Video on Demand) kan din klient behöva mer flexibilitet än vad som beskrivs i standardtillgångens manifestfil. Med Azure Media Services kan du definiera kontofilter och tillgångsfilter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [Dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du definierar ett filter för en video på begäran tillgång och använda REST API: er för att skapa [kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgångsfilter](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Krav 

För att kunna utföra stegen som beskrivs i det här avsnittet måste du:

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Konfigurera Postman för REST API-anrop för Azure Media Services](media-rest-apis-with-postman.md).

    Se till att följa det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definiera ett filter  

Följande är **brödtextexempelet Begär** som definierar de villkor för spårval som läggs till i manifestet. Det här filtret innehåller alla ljudspår som är EC-3 och alla videospår som har bithastighet i intervallet 0-1000000.

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

I postmanssamlingen som du hämtade väljer du **Kontofilter**->**Skapa eller uppdatera ett kontofilter**.

Metoden **PUT** HTTP-begäran liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Markera fliken **Brödtext** och klistra in json-koden som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Filtret har skapats.

Mer information finns i [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa tillgångsfilter  

I postmansamlingen "Media Services v3" som du hämtade väljer du **Tillgångar**->**Skapa eller uppdatera tillgångsfilter**.

Metoden **PUT** HTTP-begäran liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Markera fliken **Brödtext** och klistra in json-koden som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Tillgångsfiltret har skapats.

Mer information om hur du skapar eller uppdaterar tillgångsfilter finns i [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streamingpositionerare

Du kan ange en lista över tillgångs- eller kontofilter som gäller för din streamingpositionerare. Den [dynamiska paketeraren (Streaming Endpoint)](dynamic-packaging-overview.md) tillämpar den här listan med filter tillsammans med de som klienten anger i URL:en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i URL + filter som du anger på strömningspositioneraren. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill visa filternamnen i URL:en.

Om du vill skapa och associera filter med en strömningspositionerare `properties.filters` med REST använder du [strömningspositionerarna - Skapa](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API och anger i [begärandetexten](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Strömma med hjälp av filter

När du har definierat filter kan klienterna använda dem i webbadressen för direktuppspelning. Filter kan användas för adaptiv bitrate streaming protokoll: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på webbadresser med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-rest.md) 
