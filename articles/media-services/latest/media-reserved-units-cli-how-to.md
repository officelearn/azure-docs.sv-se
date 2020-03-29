---
title: Använd CLI för att skala mediereserverade enheter – Azure | Microsoft-dokument
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970788"
---
# <a name="scaling-media-processing"></a>Bearbetning av skalningsmedia

Med Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera mediereserverade enheter (MRU:er). MRUs bestämma hur snabbt dina mediebearbetningsuppgifter bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. 

Förutom att ange den reserverade enhetstypen kan du ange att ditt konto ska etableras med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medieuppgifter samtidigt så länge det finns uppgifter som ska bearbetas. De återstående uppgifterna väntar i kön och hämtas för bearbetning sekventiellt när en aktivitet som körs slutförs. Om ett konto inte har några etablerade reserverade enheter hämtas aktiviteter sekventiellt. I det här fallet beror väntetiden mellan en aktivitetsavbehandling och nästa start på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhetstyper

Följande tabell hjälper dig att fatta ett beslut när du väljer mellan olika kodningshastigheter. Det ger också några benchmark fall på [en video som du kan ladda ner](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) för att utföra dina egna tester:

|RU-typ|Scenario|Exempel resultat för [7 min 1080p video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Enkel bithastighetskodning. <br/>Filer på SD eller lägre upplösningar, inte tidskänsliga, låga kostnader.|Kodning till en enda bithastighet SD-upplösning MP4-fil med "H264 Single Bitrate SD 16x9" tar cirka 7 minuter.|
| **S2**|Enkel bithastighet och flera bithastighetskodning.<br/>Normal användning för både SD- och HD-kodning.|Kodning med "H264 Single Bitrate 720p" förinställda tar cirka 6 minuter.<br/><br/>Kodning med "H264 Multiple Bitrate 720p" förinställda tar cirka 12 minuter.|
| **S3**|Enkel bithastighet och flera bithastighetskodning.<br/>Full HD och 4K-upplösning videor. Tidskänslig, snabbare turnaround kodning.|Kodning med "H264 Single Bitrate 1080p" förinställda tar cirka 3 minuter.<br/><br/>Kodning med "H264 Multiple Bitrate 1080p" förinställda tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden

* För jobben Ljudanalys och videoanalys som utlöses av Media Services v3 eller Video Indexer rekommenderas S3-enhetstypen starkt.
* Om du använder den delade poolen, det villa om att utan några reserverade enheter, har kodningsuppgifterna samma prestanda som med S1 Ru:er. Det finns dock ingen övre gräns för den tid som dina aktiviteter kan spendera i köläge, och vid varje given tidpunkt körs bara en aktivitet.

Resten av artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) för att skala MRUs.

> [!NOTE]
> För ljudanalys- och videoanalysjobb som utlöses av Media Services v3 eller Video Indexer rekommenderar vi starkt att du etablerar ditt konto med 10 S3-MRU:er. Om du behöver mer än 10 S3 MRUs öppnar du en supportbiljett med [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skala mediereserverade enheter med CLI

Kör `mru`-kommandot.

Följande [az ams-konto mru-kommandot](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) anger mediereserverade enheter på kontot "amsaccount" med hjälp av parametrar för **antal** och **typ.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de mediereserverade enheterna har etablerats i ditt konto. Detta sker oberoende av om det finns några jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på prissidan för [Media Services.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se även

* [Kvoter och begränsningar](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
