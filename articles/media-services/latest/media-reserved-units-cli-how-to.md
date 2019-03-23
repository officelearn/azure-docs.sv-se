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
ms.date: 03/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f0d3c5748afaac2544232fc1ff84316d9eb7347
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351073"
---
# <a name="scaling-media-processing"></a>Skala mediebearbetning

Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera Mediereserverade enheter (MRUs). MRUs avgör hastigheten med vilken din mediebearbetning uppgifter bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** eller **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. 

Förutom att ange typ av reserverad enhet kan ange du att etablera ditt konto med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Till exempel om ditt konto har fem reserverade enheter, och sedan fem medieuppgifter körs samtidigt så länge som det finns aktiviteter som ska bearbetas. De återstående aktiviteterna ska vänta i kön och ska få hämtas för bearbetning av sekventiellt när en aktivitet är klar. Om ett konto inte har några mediereserverade enheter etablerade sedan hämtas uppgifter sekventiellt. I det här fallet beror väntetiden mellan en uppgift slutförs och nästa start på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhetstyper

Tabellen nedan hjälper dig att fatta ett beslut när du väljer mellan olika kodning hastigheter. Det ger också några benchmark-fall på [en video som du kan hämta](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) att utföra egna test:

|RU-typ|Scenario|Exempel resulterar för den [7 min 1080 p video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Enkel bithastighet kodning. <br/>Filer på SD eller under lösningar, tid inte känsliga, låg kostnad.|Kodning som ska enkel bithastighet SD upplösning MP4-fil med hjälp av ”H264 enkel bithastighet, SD 16 x 9” tar 10 minuter.|
| **S2**|Enkel bithastighet och flera bithastigheter kodning.<br/>Normal användning för både SD och HD encoding.|Kodning med ”H264, enkel bithastighet, 720p” förinställda tar cirka åtta minuter.<br/><br/>Kodning med ”H264, flera bithastigheter, 720p” förinställning tar cirka 16,8 minuter.|
| **S3**|Enkel bithastighet och flera bithastigheter kodning.<br/>Fullständig HD och 4K högupplöst video. Tid känsliga och snabbare arbetet kodning.|Kodning med ”H264, enkel bithastighet, 1080p” förinställda tar cirka 4 minuter.<br/><br/>Kodning med ”H264 Multibithastighet 1080p” förinställning tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden

* För analys av ljud och Videoanalys jobb som utlöses av Media Services v3 eller Video Indexer, rekommenderas starkt S3 enhetstyp.
* Om du använder den delade poolen, det vill säga har utan att några mediereserverade enheter sedan koda aktiviteterna samma prestanda som med mediereserverade S1-enheter. Men det finns ingen övre gräns för den tid som dina aktiviteter kan åtgärder i kö och högst endast en aktivitet kommer att köras vid en given tidpunkt.

Resten av artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) att skala MRUs.

> [!NOTE]
> För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).
>
> För närvarande kan använda du inte Azure-portalen för att hantera andra v3-resurser. Använd den [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), eller någon av stöds [SDK: er](developers-guide.md).

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skala Mediereserverade enheter med CLI

Kör `mru`-kommandot.

Följande [az ams-konto mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) kommandot anger Mediereserverade enheter på ”amsaccount”-konto med hjälp av den **antal** och **typ** parametrar.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet, typen och tid som MRUs levereras i ditt konto. Avgifter oavsett om du köra några jobb. En detaljerad förklaring finns i avsnittet vanliga frågor och svar i den [prissättning för Media Services](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

* [Kvoter och begränsningar](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
