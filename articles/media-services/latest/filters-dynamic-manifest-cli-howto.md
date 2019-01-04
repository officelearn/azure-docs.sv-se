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
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000535"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI 

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran), kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du konfigurerar ett filter för en Video på begäran tillgång och använda CLI för Media Services v3 för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgången filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Förutsättningar 

- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

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
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Om du använder Azure Cloud Shell kan du ladda upp filen till Cloud Shell (hitta knappen uppladdning/nedladdning filer överst i fönstret shell). Du kan sedan hänvisa till filen så här:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Skapa filter för tillgången

Följande [az ams tillgången-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) kommandot skapar ett filter för tillgången med filter spåra val som var [definierade tidigare](#define-a-filter). 

> [!TIP]
> Se information om att ange platsen för filnamnet i föregående avsnitt.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Se även [JSON-exempel för filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
