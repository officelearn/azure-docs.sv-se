---
title: Översikt över skalning av medie bearbetning | Microsoft Docs
description: Det här avsnittet innehåller en översikt över skalning av medie bearbetning med Azure Media Services.
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
ms.openlocfilehash: 1d2ef02ea77ad2bca37f1e397b784d06481538fa
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264019"
---
# <a name="scaling-media-processing-overview"></a>Översikt över bearbetning av skalningsmedia 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här sidan ger en översikt över hur och varför medie bearbetningen skalas. 

## <a name="overview"></a>Översikt
Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Mer information finns i [reserverade enhets typer](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Förutom att ange typ av reserverad enhet kan du ange för att etablera ditt konto med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medie uppgifter samtidigt så länge det finns aktiviteter som ska bearbetas. Återstående aktiviteter väntar i kön och hämtas för bearbetning i turordning när en aktivitet som körs slutförs. Om ett konto inte har några reserverade enheter, kommer uppgifter att hämtas sekventiellt. I det här fallet beror vänte tiden mellan en aktivitet och en start beroende på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhets typer
I följande tabell får du hjälp att fatta ett beslut när du väljer mellan olika kodnings hastigheter. Det innehåller också några benchmark-fall på [en video som du kan ladda ned](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) för att utföra egna tester:

|RU-typ|Scenario|Exempel resultat för den [7: a minimala 1080p-videon](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodning med enkel bit hastighet. <br/>Filer vid SD eller under upplösning, inte tids känsliga, låga kostnader.|Kodning till enkel bit hastighet för SD-fil med enkel bit hastighet med "H264, enkel bit hastighet SD 16x9" tar cirka 7 minuter.|
| **S2**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Normal användning för både SD-och HD-kodning.|Kodning med för inställningen "H264, enskild bit hastighet" tar cirka 6 minuter.<br/><br/>Kodning med för inställningen "H264, Multiple bit hastighet" tar cirka 12 minuter.|
| **S3**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Högupplösta HD-och 4K-lösningar. Tids känslig, snabbare svars tids kodning.|Kodning med en för inställning för "H264, enkel bit hastighet" tar cirka 3 minuter.<br/><br/>Encoding med för inställningen "H264, Multiplable bit/1080p" tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden
> [!IMPORTANT]
> Läs överväganden som beskrivs i det här avsnittet.  
> 
> 

* För ljud analys-och video analys jobb som utlöses av Media Services v3 eller Video Indexer rekommenderas S3-enhets typ starkt.
* Om du använder den delade poolen, det vill säga utan reserverade enheter, har dina kodade aktiviteter samma prestanda som i S1-ru: er. Det finns dock ingen övre gräns för den tid som dina aktiviteter kan spendera i köade tillstånd, och vid en och samma tidpunkt är det bara bara en aktivitet som kommer att köras.

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="quotas-and-limitations"></a>Kvoter och begränsningar
Information om kvoter och begränsningar och hur du öppnar ett support ärende finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Nästa steg
Uppnå den skalnings bara medie bearbetnings uppgiften med någon av dessa tekniker: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Information om hur du hämtar den senaste versionen av Java SDK och börjar utveckla med Java finns i [Komma igång med Java-klient-SDK för Media Services](./media-services-java-how-to-use.md). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowAzure-paketet i [Packagist-databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
