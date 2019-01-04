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
ms.date: 12/17/2018
ms.author: juliako
ms.openlocfilehash: 32b9664d12d6fe3a44329665c730dbc8709430f2
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650849"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Skapa filter med Media Services REST API

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du definierar ett filter för en Video på begäran tillgången och använda REST API: er för att skapa [kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters). 

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

Mer information finns i [skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Skapa filter för tillgången  

I ”Media Services v3” Postman-samlingen som du laddade ned, väljer **tillgångar**->**skapa eller uppdatera tillgången Filter**.

Den **PLACERA** HTTP-frågemetoden liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Välj den **brödtext** fliken och klistra in json kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Tillgången filtret har skapats.

Mer information om hur du skapar eller uppdaterar tillgången filter finns [skapa eller uppdatera](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-rest.md) 
