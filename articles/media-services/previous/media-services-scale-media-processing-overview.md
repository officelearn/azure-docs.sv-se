---
title: Översikt över bearbetning av mediebearbetning | Microsoft-dokument
description: Det här avsnittet är en översikt över hur mediebearbetning med Azure Media Services skalas med Azure Media Services.
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
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102915"
---
# <a name="scaling-media-processing-overview"></a>Översikt över bearbetning av skalningsmedia 
Den här sidan ger en översikt över hur och varför mediebearbetning ska skalas. 

## <a name="overview"></a>Översikt
Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Mer information finns i [reserverade enhetstyper](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Förutom att ange den reserverade enhetstypen kan du ange att ditt konto ska etableras med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medieuppgifter samtidigt så länge det finns uppgifter som ska bearbetas. De återstående uppgifterna väntar i kön och hämtas för bearbetning sekventiellt när en aktivitet som körs slutförs. Om ett konto inte har några etablerade reserverade enheter hämtas aktiviteter sekventiellt. I det här fallet beror väntetiden mellan en aktivitetsavbehandling och nästa start på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhetstyper
Följande tabell hjälper dig att fatta ett beslut när du väljer mellan olika kodningshastigheter. Det ger också några benchmark fall på [en video som du kan ladda ner](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) för att utföra dina egna tester:

|RU-typ|Scenario|Exempel resultat för [7 min 1080p video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Enkel bithastighetskodning. <br/>Filer på SD eller lägre upplösningar, inte tidskänsliga, låga kostnader.|Kodning till en enda bithastighet SD-upplösning MP4-fil med "H264 Single Bitrate SD 16x9" tar cirka 7 minuter.|
| **S2**|Enkel bithastighet och flera bithastighetskodning.<br/>Normal användning för både SD- och HD-kodning.|Kodning med "H264 Single Bitrate 720p" förinställda tar cirka 6 minuter.<br/><br/>Kodning med "H264 Multiple Bitrate 720p" förinställda tar cirka 12 minuter.|
| **S3**|Enkel bithastighet och flera bithastighetskodning.<br/>Full HD och 4K-upplösning videor. Tidskänslig, snabbare turnaround kodning.|Kodning med "H264 Single Bitrate 1080p" förinställda tar cirka 3 minuter.<br/><br/>Kodning med "H264 Multiple Bitrate 1080p" förinställda tar cirka 8 minuter.|

## <a name="considerations"></a>Överväganden
> [!IMPORTANT]
> Granska överväganden som beskrivs i det här avsnittet.  
> 
> 

* För jobben Ljudanalys och videoanalys som utlöses av Media Services v3 eller Video Indexer rekommenderas S3-enhetstypen starkt.
* Om du använder den delade poolen, det villa om att utan några reserverade enheter, har kodningsuppgifterna samma prestanda som med S1 Ru:er. Det finns dock ingen övre gräns för den tid som dina aktiviteter kan spendera i köläge, och vid varje given tidpunkt körs bara en aktivitet.

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de mediereserverade enheterna har etablerats i ditt konto. Detta sker oberoende av om det finns några jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på prissidan för [Media Services.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="quotas-and-limitations"></a>Kvoter och begränsningar
Information om kvoter och begränsningar och hur du öppnar en supportbiljett finns i [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Nästa steg
Uppnå bearbetningsuppgiften för skalning av media med någon av dessa tekniker: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Resten](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Information om hur du hämtar den senaste versionen av Java SDK och börjar utveckla med Java finns i [Komma igång med Java-klient-SDK för Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowAzure-paketet i [Packagist-databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

