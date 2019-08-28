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
ms.date: 08/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102935"
---
# <a name="scaling-media-processing"></a>Skala mediebearbetning

Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera Mediereserverade enheter (MRUs). MRUs avgör hastigheten som dina medie bearbetnings uppgifter bearbetas med. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** eller **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. 

Förutom att ange typ av reserverad enhet kan du ange för att etablera ditt konto med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medie uppgifter samtidigt så länge det finns aktiviteter som ska bearbetas. Återstående aktiviteter väntar i kön och hämtas för bearbetning i turordning när en aktivitet som körs slutförs. Om ett konto inte har några reserverade enheter, kommer uppgifter att hämtas sekventiellt. I det här fallet beror vänte tiden mellan en aktivitet och en start beroende på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhets typer

I följande tabell får du hjälp att fatta ett beslut när du väljer mellan olika kodnings hastigheter. Det innehåller också några benchmark-fall på [en video som du kan ladda ned](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) för att utföra egna tester:

|RU-typ|Scenario|Exempel resultat för den [7](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) : a minimala 1080p-videon|
|---|---|---|
| **S1**|Kodning med enkel bit hastighet. <br/>Filer vid SD eller under upplösning, inte tids känsliga, låga kostnader.|Kodning till enkel bit hastighet för SD-fil med enkel bit hastighet med "H264, enkel bit hastighet SD 16x9" tar cirka 7 minuter.|
| **S2**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Normal användning för både SD-och HD-kodning.|Kodning med för inställningen "H264, enskild bit hastighet" tar cirka 6 minuter.<br/><br/>Kodning med för inställningen "H264, Multiple bit hastighet" tar cirka 12 minuter.|
| **S3**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Högupplösta HD-och 4K-lösningar. Tids känslig, snabbare svars tids kodning.|Kodning med en för inställning för "H264, enkel bit hastighet" tar cirka 3 minuter.<br/><br/>Encoding med för inställningen "H264, Multiplable bit/1080p" tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden

* För ljud analys-och video analys jobb som utlöses av Media Services v3 eller Video Indexer rekommenderas S3-enhets typ starkt.
* Om du använder den delade poolen, det vill säga utan reserverade enheter, har dina kodade aktiviteter samma prestanda som i S1-ru: er. Det finns dock ingen övre gräns för den tid som dina aktiviteter kan spendera i köade tillstånd, och vid en och samma tidpunkt är det bara bara en aktivitet som kommer att köras.

Resten av artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) för att skala MRUs.

> [!NOTE]
> För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).
>
> För närvarande kan du inte använda Azure Portal för att hantera andra v3-resurser. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](media-services-apis-overview.md#sdks).

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skala Mediereserverade enheter med CLI

Kör `mru`-kommandot.

Följande [az ams-konto mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) kommandot anger Mediereserverade enheter på ”amsaccount”-konto med hjälp av den **antal** och **typ** parametrar.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet vanliga frågor och svar i den [prissättning för Media Services](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

* [Kvoter och begränsningar](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
