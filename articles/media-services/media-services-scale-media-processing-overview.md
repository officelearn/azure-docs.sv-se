---
title: "Skalning Media bearbetning översikt | Microsoft Docs"
description: "Det här avsnittet är en översikt över skalning Media bearbetning med Azure Media Services."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: a82481c4995bfb078e88d7096dff37b52312a296
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="scaling-media-processing-overview"></a>Skalning bearbetning av Media: översikt
Den här sidan ger en översikt över hur och varför ska man skala media bearbetning. 

## <a name="overview"></a>Översikt
Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Mer information finns i [reserverade enhetstyper](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Ange vilken enhet, kan du ange att tillhandahålla ditt konto med reserverade enheter. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Till exempel om ditt konto har fem reserverade enheter fem media aktiviteter körs samtidigt så länge som det finns aktiviteter som ska bearbetas. De återstående uppgifterna kommer att vänta i kön och ska hämta tas upp för bearbetning i tur och ordning när en aktivitet är klar. Om ett konto inte har några reserverade enheter som har etablerats sedan hanteras uppgifter sekventiellt. I det här fallet beror väntetiden mellan en aktivitet avslutas och nästa start på tillgängligheten för resurser i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Att välja mellan olika reserverade enhetstyper
Tabellen nedan hjälper dig att fatta beslut om att välja mellan olika kodning hastigheter. Den innehåller några benchmark fall och innehåller SAS-URL: er som du kan använda för att hämta videor som du kan utföra egna test:

| Scenarier | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Avsedda användningsfall |Enkel bithastighet kodning. <br/>Filer på SD eller under lösningar, tid inte känsliga, låg kostnad. |Enkel bithastighet och flera bithastighet kodning.<br/>Normal användning för både SD och HD kodning. |Enkel bithastighet och flera bithastighet kodning.<br/>Videor för fullständig HD och 4K lösning. Tid som känsliga och snabbare tidsåtgången kodning. |
| Prestandamått |[Indatafilen: 5 minuter långa 640x360p på 29,97 bildrutor per sekund](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Kodning för en enda bithastighet MP4-fil med samma upplösning tar ungefär 11 minuter. |[Indatafilen: 5 minuter långa 1280x720p på 29,97 bildrutor per sekund](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Kodningen med ”H264 enkel bithastighet 720p” förinställda tar cirka 5 minuter.<br/><br/>Kodningen med ”H264 Multibithastighet 720p” förinställda tar ungefär 11,5 minuter. |[Indatafilen: 5 minuter långa 1920x1080p på 29,97 bildrutor per sekund](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Kodningen med ”H264 enkel bithastighet 1080p” förinställda tar cirka 2.7 minuter.<br/><br/>Kodningen med ”H264 Multibithastighet 1080p” förinställda tar ungefär 5.7 minuter. |

## <a name="considerations"></a>Överväganden
> [!IMPORTANT]
> Gå igenom överväganden som beskrivs i det här avsnittet.  
> 
> 

* Reserverade enheter fungerar för parallelizing all bearbetning av media, inklusive indexering jobb med hjälp av Azure Media Indexer.  Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.
* Om du använder den delade poolen, det vill säga har utan några reserverade enheter sedan koda aktiviteterna samma prestanda som med S1 RUs. Men det finns ingen övre gräns till dess att aktiviteterna kan spendera i köade tillstånd och samtidigt, högst endast en aktivitet körs.
* Följande Datacenter erbjuder inte den **S2** reserverade enhetstyp: södra och västra Indien.
* Följande Datacenter erbjuder inte den **S3** reserverade enhetstyp: Indien, västra.

## <a name="billing"></a>Fakturering

Du debiteras efter faktiska använda minuter av mediereserverade enheter. Se avsnittet vanliga frågor och svar i en detaljerad förklaring av [Media Services priser](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="quotas-and-limitations"></a>Kvoter och begränsningar
Information om kvoter och begränsningar och hur du skapar ett supportärende finns [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Nästa steg
Uppnå skalning media bearbetning aktiviteten med någon av dessa tekniker: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Om du vill hämta den senaste versionen av Java SDK och börja utveckla med Java Se [komma igång med Java-klient-SDK för Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowAzure paketet i den [Packagist databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

