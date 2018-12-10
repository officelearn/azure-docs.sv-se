---
title: Använd CLI för att skala Mediereserverade enheter – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder CLI för att skala mediebearbetning med Azure Media Services.
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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2b10de83e00b3668f70461f76634c560bcbea1a4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133799"
---
# <a name="scaling-media-processing"></a>Skala mediebearbetning

Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera Mediereserverade enheter (MRUs). Detaljerad översikt finns i [skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). 

Den här artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) att skala MRUs.

> [!NOTE]
> För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. <br/>Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Förutsättningar 

- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

- [Skapa ett Media Services-konto](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skala Mediereserverade enheter med CLI

Följande [az ams-konto mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) kommandot anger Mediereserverade enheter på ”amsaccount”-konto med hjälp av den **antal** och **typ** parametrar.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet, typen och tid som MRUs levereras i ditt konto. Avgifter oavsett om du köra några jobb. En detaljerad förklaring finns i avsnittet vanliga frågor och svar i den [prissättning för Media Services](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
