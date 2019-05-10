---
title: Använd CLI för att skapa filter med Azure Media Services | Microsoft Docs
description: Det här avsnittet visar hur du använder CLI för att skapa filter med Media Services.
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
ms.date: 05/07/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8e1c031643fc3ce75d99ad619ce46b38c9cba82c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472702"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI 

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran), kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du konfigurerar ett filter för en Video på begäran tillgång och använda CLI för Media Services v3 för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgången filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Nödvändiga komponenter 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definiera ett filter 

I följande exempel definierar spåra val av villkoren som läggs till sista manifestet. Det här filtret innehåller alla ljudspår som är EG-3 och alla video spår har bithastighet i 0-1000000 intervall.

> [!TIP]
> Om du planerar att definiera **filter** i REST, Lägg märke till att du behöver inkludera ”egenskaper” wrapper JSON-objekt.  

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

Följande [az ams-konto-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) kommandot skapar ett kontofilter med filter spåra val som var [definierade tidigare](#define-a-filter). 

Kommandot kan du skicka en valfri `--tracks` parameter som innehåller JSON som representerar spåra val.  Använd @{file} för att läsa in JSON från en fil. Om du använder Azure CLI lokalt kan du ange hela sökvägen:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Skapa filter för tillgången

Följande [az ams tillgången-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) kommandot skapar ett filter för tillgången med filter spåra val som var [definierade tidigare](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).


## <a name="associate-filters-with-streaming-locator"></a>Associera filter med Strömningspositionerare

Du kan ange en lista över tillgång eller konto filter som skulle gälla för dina Strömningspositionerare. Den [dynamisk Paketeraren (slutpunkt för direktuppspelning)](dynamic-packaging-overview.md) gäller den här listan över filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar en [dynamiska manifest](filters-dynamic-manifest-overview.md), som grundar sig på filter i URL: en + filter som du anger på Strömningspositionerare. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill exponera filternamn i URL: en.

Följande CLI-kod visar hur du skapar en positionerare för direktuppspelning och ange `filters`. Det här är en valfri egenskap som tar en blankstegsavgränsad lista med filtret resursnamn och/eller filter kontonamn.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream med hjälp av filter

När du har definierat filter kan klienterna använda dem i strömnings-URL. Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
