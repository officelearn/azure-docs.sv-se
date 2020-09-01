---
title: Migrera från Azure Media Services v2 till v3 | Microsoft Docs
description: Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner. Artikeln innehåller även vägledning för migrering för att flytta från Media Services v2 till v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e692eef45f8a0b4a8918e7255fa1ac85d4ad064c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265838"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Vägledning för migrering för att flytta från Media Services v2 till v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` i din RSS-feed läsare.

Den här artikeln innehåller en vägledning för migrering från Media Services v2 till v3.

Om du har en video tjänst som utvecklats idag ovanpå de [äldre Media Services v2-API: erna](../previous/media-services-overview.md)bör du läsa följande rikt linjer och överväganden innan du migrerar till v3-API: erna. Det finns många fördelar och nya funktioner i v3-API: et som förbättrar utvecklings upplevelsen och funktionerna i Media Services. Som du ser i avsnittet [kända problem](#known-issues) i den här artikeln finns det också vissa begränsningar på grund av ändringar mellan API-versionerna. Den här sidan kommer att behållas när Media Servicess teamet gör fortsatta förbättringar av v3-API: erna och åtgärdar luckorna mellan versionerna. 

## <a name="prerequisites"></a>Förutsättningar

* Granska [Media Services v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Fördelar med Media Services v3
  
### <a name="api-is-more-approachable"></a>API är mer lättare att nå

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. Azure Resource Manager mallar kan användas för att skapa och distribuera transformeringar, slut punkter för direkt uppspelning, Live-händelser och mycket annat.
* [Openapi-specifikation (tidigare kallat Swagger)-](https://aka.ms/ams-v3-rest-sdk) dokument.
    Exponerar schemat för alla tjänst komponenter, inklusive filbaserad kodning.
* SDK: er är tillgängliga för [.net](https://aka.ms/ams-v3-dotnet-ref), .net Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)och Ruby.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) -integrering för enkel skript support.

### <a name="new-features"></a>Nya funktioner

* För filbaserad jobb bearbetning kan du använda en HTTP (S)-URL som indata.<br/>Du behöver inte ha något innehåll som redan är lagrat i Azure, eller så behöver du inte skapa till gångar.
* Introducerar begreppet [transformeringar](transforms-jobs-concept.md) för filbaserad jobb bearbetning. En transformering kan användas för att skapa återanvändbara konfigurationer, skapa Azure Resource Manager mallar och isolera bearbetnings inställningar mellan flera kunder eller klienter.
* En till gång kan ha flera [positioner för strömning](streaming-locators-concept.md) med olika inställningar för [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering.
* [Innehålls skydd](content-key-policy-concept.md) stöder flera viktiga funktioner.
* Du kan strömma direktsända händelser som är upp till 24 timmar långt när du använder Media Services för att avkoda en enskild bit i ett bidrags flöde för en bit hastighet till en utdataström som har flera bit hastigheter.
* Ny support för Live-direktuppspelning med låg latens för Live-händelser. Mer information finns i [svars tid](live-event-latency.md).
* För hands versionen av live event stöder [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering. Detta aktiverar innehålls skydd för för hands versionen samt streck-och HLS-paket.
* Live-utdata är enklare att använda än entiteten program i v2-API: erna. 
* Förbättrat stöd för RTMP (ökad stabilitet och fler stöd för käll kodare).
* RTMPs säker inmatning.<br/>När du skapar en Live-händelse får du 4 inmatnings-URL: er. De 4 inmatnings-URL: erna är nästan identiska, har samma strömnings-token (AppId), men endast port nummer delen är annorlunda. Två av URL: erna är primära och säkerhets kopiering för RTMP.   
* Du har rollbaserad åtkomst kontroll (RBAC) över dina entiteter. 

## <a name="known-issues"></a>Kända problem

*  För närvarande kan du använda [Azure Portal](https://portal.azure.com/) för att:

    * Hantera Media Services v3 [Live-händelser](live-events-outputs-concept.md), 
    * Visa (inte hantera) v3- [till gångar](assets-concept.md), 
    * [Hämta information om att komma åt API: er](./access-api-howto.md). 

    För alla andra hanterings uppgifter (t. ex. [transformationer och jobb](transforms-jobs-concept.md) och [innehålls skydd](content-protection-overview.md)) använder du [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.
* Du måste etablera medie reserverade enheter (MRUs) i ditt konto för att kunna styra samtidigheten och prestandan för dina jobb, särskilt för video-eller ljud analys. Mer information finns i [Skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). Du kan hantera MRUs med [CLI 2,0 för Media Services v3](media-reserved-units-cli-how-to.md), med hjälp av [Azure Portal](../previous/media-services-portal-scale-media-processing.md)eller [v2-API: er](../previous/media-services-dotnet-encoding-units.md). Du måste etablera MRUs, oavsett om du använder Media Services v2 eller v3-API: er.
* Media Services entiteter som har skapats med v3-API: t kan inte hanteras av v2-API: et.  
* Alla entiteter i v2-API: t visas inte automatiskt i v3-API: et.  Följande är exempel på entiteter i de två versioner som är inkompatibla:  
    * Jobb och uppgifter som skapas i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att växla till v3-transformeringar och-jobb. Det kommer att finnas en relativt kort tids period för att övervaka synlighetssekvensnummer v2-jobben under växlingen.
    * Kanaler och program som skapats med v2 (som mappas till Live-händelser och Live-utdata i v3) kan inte fortsätta hanteras med v3. Rekommendationen är att byta till v3 Live-händelser och direktsända utdata i ett bekvämt kanal avbrott.<br/>För närvarande kan du inte migrera kanaler som körs kontinuerligt.  

> [!NOTE]
> Den här sidan kommer att behållas när Media Servicess teamet gör fortsatta förbättringar av v3-API: erna och åtgärdar luckorna mellan versionerna.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Självstudie: koda en fjärrfil baserat på URL och strömma videon – .NET](stream-files-dotnet-quickstart.md)
