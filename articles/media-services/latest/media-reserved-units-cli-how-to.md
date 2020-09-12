---
title: Använd CLI för att skala medium reserverade enheter – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder CLI för att skala medie bearbetning med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 85013ccc5c82cf1b9d1d40a4e10450838d5d3195
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289417"
---
# <a name="scaling-media-processing"></a>Bearbetning av skalningsmedia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera mediereserverade enheter (MRU:er). MRUs avgör hastigheten som dina medie bearbetnings uppgifter bearbetas med. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. 

Förutom att ange typ av reserverad enhet kan du ange för att etablera ditt konto med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medie uppgifter samtidigt så länge det finns aktiviteter som ska bearbetas. Återstående aktiviteter väntar i kön och hämtas för bearbetning i turordning när en aktivitet som körs slutförs. Om ett konto inte har några reserverade enheter, kommer uppgifter att hämtas sekventiellt. I det här fallet beror vänte tiden mellan en aktivitet och en start beroende på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhets typer

I följande tabell får du hjälp att fatta ett beslut när du väljer mellan olika kodnings hastigheter. Det innehåller också några benchmark-fall på [en video som du kan ladda ned](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) för att utföra egna tester:

|RU-typ|Scenario|Exempel resultat för den [7: a minimala 1080p-videon](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodning med enkel bit hastighet. <br/>Filer vid SD eller under upplösning, inte tids känsliga, låga kostnader.|Kodning till enkel bit hastighet för SD-fil med enkel bit hastighet med "H264, enkel bit hastighet SD 16x9" tar cirka 7 minuter.|
| **S2**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Normal användning för både SD-och HD-kodning.|Kodning med för inställningen "H264, enskild bit hastighet" tar cirka 6 minuter.<br/><br/>Kodning med för inställningen "H264, Multiple bit hastighet" tar cirka 12 minuter.|
| **S3**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Högupplösta HD-och 4K-lösningar. Tids känslig, snabbare svars tids kodning.|Kodning med en för inställning för "H264, enkel bit hastighet" tar cirka 3 minuter.<br/><br/>Encoding med för inställningen "H264, Multiplable bit/1080p" tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden

* För ljud analys-och video analys jobb som utlöses av Media Services v3 eller Video Indexer rekommenderas S3-enhets typ starkt.
* Om du använder den delade poolen, det vill säga utan reserverade enheter, har dina kodade aktiviteter samma prestanda som i S1-ru: er. Det finns dock ingen övre gräns för den tid som dina aktiviteter kan spendera i köade tillstånd, och vid en och samma tidpunkt är det bara bara en aktivitet som kommer att köras.

Resten av artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) för att skala MRUs.

> [!NOTE]
> För ljudanalys- och videoanalysjobb som utlöses av Media Services v3 eller Video Indexer rekommenderar vi starkt att du etablerar ditt konto med 10 S3-MRU:er. Om du behöver fler än 10 S3-MRUs öppnar du ett support ärende med hjälp av [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav 

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skala medie reserverade enheter med CLI

Kör kommandot `mru`.

Följande [AZ AMS konto MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) -kommando anger medie reserverade enheter på "amsaccount"-kontot med hjälp av **Count** -och **Type** -parametrarna.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se även

* [Kvoter och begränsningar](limits-quotas-constraints.md)
* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
