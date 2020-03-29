---
title: Använd CLI för att skapa filter med Azure Media Services| Microsoft-dokument
description: Den här artikeln visar hur du använder CLI för att skapa filter med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896061"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI 

När du levererar ditt innehåll till kunder (strömma livehändelser eller Video on Demand) kan klienten behöva mer flexibilitet än vad som beskrivs i standardtillgångens manifestfil. Med Azure Media Services kan du definiera kontofilter och tillgångsfilter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [Dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du konfigurerar ett filter för en video på begäran tillgång och använda CLI för Media Services v3 för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgångsfilter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Krav 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppsnamnet och mediatjänstkontonamnet. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definiera ett filter 

I följande exempel definieras de spårvalsvillkor som läggs till i det slutliga manifestet. Det här filtret innehåller alla ljudspår som är EC-3 och alla videospår som har bithastighet i intervallet 0-1000000.

> [!TIP]
> Om du planerar att definiera **filter** i REST måste du se att du måste inkludera JSON-objektet "Egenskaper".  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Skapa kontofilter

Följande az [ams-kontofilter-kommando](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) skapar ett kontofilter med filterspårval som [definierades tidigare](#define-a-filter). 

Med kommandot kan du `--tracks` skicka en valfri parameter som innehåller JSON som representerar spårvalen.  Använd @{file} för att läsa in JSON från en fil. Om du använder Azure CLI lokalt anger du hela filsökvägen:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa tillgångsfilter

Följande kommando [för az ams-tillgångsfilter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) skapar ett tillgångsfilter med filterspårsval som [definierades tidigare](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streamingpositionerare

Du kan ange en lista över tillgångs- eller kontofilter som gäller för din streamingpositionerare. Den [dynamiska paketeraren (Streaming Endpoint)](dynamic-packaging-overview.md) tillämpar den här listan med filter tillsammans med de som klienten anger i URL:en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i URL + filter som du anger på strömningspositioneraren. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill visa filternamnen i URL:en.

Följande CLI-kod visar hur du skapar `filters`en streaming locator och anger . Det här är en valfri egenskap som tar en utrymmesavgränsad lista över tillgångsfilternamn och/eller kontofilternamn.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Strömma med hjälp av filter

När du har definierat filter kan klienterna använda dem i webbadressen för direktuppspelning. Filter kan användas för adaptiv bitrate streaming protokoll: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på webbadresser med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Se även

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
