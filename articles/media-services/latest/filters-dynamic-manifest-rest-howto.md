---
title: Skapa filter med Azure Media Services v3 REST API
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
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780342"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Skapa filter med Media Services REST API

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du definierar ett filter för en Video på begäran tillgången och använda REST API: er för att skapa [kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Krav 

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).

    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definiera ett filter  

Följande är de **Begärandetext** exempel som definierar de spåra val av villkor som läggs till i manifestet. Det här filtret innehåller alla ljud spår som är EC-3 och alla video spår som har bit hastighet i intervallet 0-1000000.

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

I Postman-samlingen "Media Services v3" som du laddade ned väljer du **till gångar**->**skapa eller uppdatera till gångs filter**.

Den **PLACERA** HTTP-frågemetoden liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Välj den **brödtext** fliken och klistra in json kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Tillgången filtret har skapats.

Mer information om hur du skapar eller uppdaterar tillgången filter finns [skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över till gångs-eller konto filter, som gäller för din strömmande positionerare. Den [dynamiska Paketeraren (slut punkt för direkt uppspelning)](dynamic-packaging-overview.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i de URL: er som du anger på en strömmande positionerare. Vi rekommenderar att du använder den här funktionen om du vill tillämpa filter men inte vill visa filter namnen i URL: en.

Om du vill skapa och associera filter med en strömmande positionerare med hjälp av REST använder du [strömmande positionerare – skapa](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API och ange `properties.filters` i [begär ande texten](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Data ström med filter

När du har definierat filter kan klienterna använda dem i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-rest.md) 
