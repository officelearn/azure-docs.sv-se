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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099314"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI 

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran), kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du konfigurerar ett filter för en Video på begäran tillgång och använda CLI för Media Services v3 för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgången filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Förutsättningar 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definiera ett filter 

I följande exempel definierar spåra val av villkoren som läggs till sista manifestet. Det här filtret innehåller alla ljudspår som är EG-3 och alla video spår har bithastighet i 0-1000000 intervall.

Filter som definieras i REST, innehålla ”egenskaper” wrapper JSON-objekt.  

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

## <a name="next-step"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
