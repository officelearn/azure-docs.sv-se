---
title: Använd CLI för att skapa filter med Azure Media Services | Microsoft Docs
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896061"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI 

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran), kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du konfigurerar ett filter för en Video på begäran tillgång och använda CLI för Media Services v3 för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgången filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Krav 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definiera ett filter 

I följande exempel definierar spåra val av villkoren som läggs till sista manifestet. Det här filtret innehåller alla ljud spår som är EC-3 och alla video spår som har bit hastighet i intervallet 0-1000000.

> [!TIP]
> Observera att du måste ta med JSON-objektet "egenskaper" om du planerar att definiera **filter** i vila.  

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

Med kommandot kan du skicka en valfri `--tracks` parameter som innehåller JSON som representerar spårnings valen.  Använd @ {File} för att läsa in JSON från en fil. Om du använder Azure CLI lokalt anger du hela fil Sök vägen:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa filter för tillgången

Följande [az ams tillgången-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) kommandot skapar ett filter för tillgången med filter spåra val som var [definierade tidigare](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över till gångs-eller konto filter, som gäller för din strömmande positionerare. Den [dynamiska Paketeraren (slut punkt för direkt uppspelning)](dynamic-packaging-overview.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i de URL: er som du anger på en strömmande positionerare. Vi rekommenderar att du använder den här funktionen om du vill tillämpa filter men inte vill visa filter namnen i URL: en.

Följande CLI-kod visar hur du skapar en strömmande lokaliserare och anger `filters`. Det här är en valfri egenskap som tar en blankstegsavgränsad lista över till gångs filter namn och/eller konto filter namn.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Data ström med filter

När du har definierat filter kan klienterna använda dem i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming.

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
