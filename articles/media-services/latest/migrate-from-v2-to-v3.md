---
title: Migrera från Azure Media Services v2 till v3 | Microsoft-dokument
description: I den här artikeln beskrivs ändringar som introducerades i Azure Media Services v3 och visar skillnader mellan två versioner. Artikeln innehåller också migreringsvägledning för att flytta från Media Services v2 till v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472075"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Migreringsvägledning för flyttning från Media Services v2 till v3

>Få ett meddelande om när du ska gå tillbaka till `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` den här sidan för uppdateringar genom att kopiera och klistra in den här webbadressen: i rss-flödesläsaren.

Den här artikeln innehåller migreringsvägledning från Media Services v2 till v3.

Om du har en videotjänst som utvecklats idag utöver de [äldre Api:erna för Media Services v2](../previous/media-services-overview.md)bör du granska följande riktlinjer och överväganden innan du migrerar till v3-API:erna. Det finns många fördelar och nya funktioner i v3 API som förbättrar utvecklarupplevelsen och funktionerna i Media Services. Men som kallas i avsnittet [Kända problem](#known-issues) i den här artikeln finns det också vissa begränsningar på grund av ändringar mellan API-versionerna. Den här sidan kommer att underhållas när Media Services-teamet gör fortsatta förbättringar av v3 API:er och åtgärdar luckorna mellan versionerna. 

## <a name="prerequisites"></a>Krav

* Granska [Media Services v2 vs v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Fördelar med Media Services v3
  
### <a name="api-is-more-approachable"></a>API är mer lättillgängligt

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. Azure Resource Manager-mallar kan användas för att skapa och distribuera transformeringar, slutpunkter för direktuppspelning, livehändelser med mera.
* [OpenAPI Specification (tidigare kallat Swagger)](https://aka.ms/ams-v3-rest-sdk) dokument.
    Exponerar schemat för alla tjänstkomponenter, inklusive filbaserad kodning.
* SDK:er tillgängliga för [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)och Ruby.
* [Azure CLI-integrering](https://aka.ms/ams-v3-cli-ref) för enkel skriptsupport.

### <a name="new-features"></a>Nya funktioner

* För filbaserad jobbbearbetning kan du använda en HTTP(S)-URL som indata.<br/>Du behöver inte ha innehåll som redan lagrats i Azure, och du behöver inte heller skapa resurser.
* Introducerar begreppet [Transforms](transforms-jobs-concept.md) för filbaserad jobbbearbetning. En transformering kan användas för att skapa återanvändbara konfigurationer, skapa Azure Resource Manager-mallar och isolera bearbetningsinställningar mellan flera kunder eller klienter.
* En tillgång kan ha flera [streamingpositionerare](streaming-locators-concept.md) var och en med olika [inställningar för dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering.
* [Innehållsskydd](content-key-policy-concept.md) stöder funktioner med flera nyckeltal.
* Du kan strömma livehändelser som är upp till 24 timmar långa när du använder Media Services för omkodning av ett enda bithastighetsbidragsflöde till en utdataström som har flera bithastigheter.
* Nytt stöd för livestreaming med låg latens på livehändelser. Mer information finns i [svarstiden](live-event-latency.md).
* Live Event Preview stöder [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering. Detta möjliggör innehållsskydd på förhandsversion samt DASH- och HLS-paket.
* Live-utdata är enklare att använda än programentiteten i v2-API:erna. 
* Förbättrat RTMP-stöd (ökad stabilitet och mer stöd för källkodare).
* RTMPS säkrar intag.<br/>När du skapar en livehändelse får du 4 intjest webbadresser. De 4 intövande webbadresserna är nästan identiska, har samma streamingtoken (AppId), endast portnummerdelen är annorlunda. Två av webbadresserna är primära och säkerhetskopierade för RTMPS.   
* Du har rollbaserad åtkomstkontroll (RBAC) över dina entiteter. 

## <a name="known-issues"></a>Kända problem

*  För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att:

    * hantera Media Services v3 [Live Events](live-events-outputs-concept.md), 
    * visa (inte hantera) [v3-tillgångar](assets-concept.md), 
    * [få information om hur du använder API:er](access-api-portal.md). 

    För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) and [Content protection)](content-protection-overview.md)använder du [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .
* Du måste etablera mediereserverade enheter i ditt konto för att kontrollera samtidigheten och prestandan hos dina jobb, särskilt de som involverar video- eller ljudanalys. Mer information finns i [Skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). Du kan hantera MR:erna med [CLI 2.0 för Media Services v3,](media-reserved-units-cli-how-to.md)använda [Azure-portalen](../previous/media-services-portal-scale-media-processing.md)eller använda [v2 API:erna](../previous/media-services-dotnet-encoding-units.md). Du måste etablera MRUs, oavsett om du använder Media Services v2 eller v3 API: er.
* Media Services-entiteter som skapats med v3-API:et kan inte hanteras av v2-API:et.  
* Alla entiteter i V2 API visas inte automatiskt i V3 API.  Här följer exempel på entiteter i de två versionerna som är inkompatibla:  
    * Jobb och uppgifter som skapas i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att byta till v3 Transforms and Jobs. Det kommer att finnas en relativt kort tidsperiod för att behöva övervaka inflight v2 Jobb under övergången.
    * Kanaler och program som skapats med v2 (som mappas till livehändelser och liveutdata i v3) kan inte fortsätta att hanteras med v3. Rekommendationen är att byta till v3 Live Events och Live Outputs vid ett bekvämt kanalstopp.<br/>För närvarande kan du inte migrera kontinuerligt köra kanaler.  

> [!NOTE]
> Den här sidan kommer att underhållas när Media Services-teamet gör fortsatta förbättringar av v3 API:er och åtgärdar luckorna mellan versionerna.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - .NET](stream-files-dotnet-quickstart.md)
