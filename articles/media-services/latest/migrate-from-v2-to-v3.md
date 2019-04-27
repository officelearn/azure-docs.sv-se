---
title: Migrera från Azure Media Services v2 till v3 | Microsoft Docs
description: Den här artikeln redogörs för ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner. Artikeln innehåller även riktlinjer för att flytta från Media Services v2 till v3.
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
ms.date: 03/27/2019
ms.author: juliako
ms.openlocfilehash: 70e28377b19b682f2191e0a8fb95792101fa8ec7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326402"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Riktlinjer för att flytta från Media Services v2 till v3

Den här artikeln redogörs för ändringar som introducerades i Azure Media Services v3, visar skillnaderna mellan två versioner och ger vägledning för migrering.

Om du har en videotjänst som har utvecklats i dag ovanpå den [äldre Media Services v2 API: er](../previous/media-services-overview.md), bör du granska följande riktlinjer och att tänka på innan du migrerar till v3-API: er. Det finns många fördelar och nya funktioner i v3-API som förbättrar utvecklarupplevelse och funktionerna i Media Services. Dock som kallas ut i den [kända problem](#known-issues) avsnitt av den här artikeln, det finns även vissa begränsningar på grund av ändringar mellan API-versioner. Den här sidan bevaras som Media Services-teamet gör fortsatt förbättringar till v3-API: er och bemöta luckor mellan versionerna. 

> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](developers-guide.md).

## <a name="benefits-of-media-services-v3"></a>Fördelarna med Media Services v3
  
### <a name="api-is-more-approachable"></a>API: et är mer programmera

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. Azure Resource Manager-mallar kan användas för att skapa och distribuera transformeringar, -slutpunkter för direktuppspelning, Live-händelser med mera.
* [Öppna API (även kallat Swagger) specifikationen](https://aka.ms/ams-v3-rest-sdk) dokumentet.
    Visar schemat för alla tjänstkomponenter, inklusive filbaserade kodning.
* SDK: er som är tillgängliga för [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Gå](https://aka.ms/ams-v3-go-ref), och Ruby.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) -integrering för stöd för enkla skript.

### <a name="new-features"></a>Nya funktioner

* Du kan använda en HTTP (S)-URL för filbaserade jobb bearbetning, som indata.<br/>Du behöver inte ha innehåll som redan lagras i Azure, inte heller att skapa tillgångar.
* Introducerar begreppet [omvandlar](transforms-jobs-concept.md) för filbaserade Jobbearbetning. En transformering kan användas för att skapa återanvändbara konfigurationer för att skapa Azure Resource Manager-mallar och isolera bearbetning av inställningar mellan flera kunder eller klienter.
* En tillgång kan ha flera [positionerare för direktuppspelning](streaming-locators-concept.md) var och en med olika [dynamisk paketering](dynamic-packaging-overview.md) och inställningar för dynamisk kryptering.
* [Innehållsskydd](content-key-policy-concept.md) har stöd för flera viktiga funktioner.
* Du kan strömma Live-händelser som är upp till 24 timmar lång när använder Media Services för transkodning ett bidrag, enkel bithastighet, skicka till en utdataström som har flera olika bithastigheter.
* Nya med låg latens live direktuppspelning support för Live-händelser. Mer information finns i [svarstid](live-event-latency.md).
* Live-händelse förhandsversionen stöder [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering. Detta gör det möjligt för innehållsskydd på förhandsversion som DASH och HLS paketering.
* Live utdata är enklare att använda än programmet entiteten i v2-API: er. 
* Förbättrad RTMP support (ökad stabilitet och mer käll-kodare stöder).
* RTMPS säker mata in.<br/>När du skapar en direktsänd händelse kan du få 4 mata in URL: er. 4 mata in URL: er är nästan identiska, har samma strömmande token (AppId), bara den numeriska delen port är olika. Två av de URL: er är primär och sekundär för RTMPS.   
* Du har rollbaserad åtkomstkontroll (RBAC) över dina entiteter. 

## <a name="changes-from-v2"></a>Ändringar från v2

* För tillgångar som har skapats med v3, Media Services stöder endast den [lagringskryptering för Azure Storage serversidan](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Du kan använda v3-API: er med tillgångar som skapas med v2 API: er som hade [lagringskryptering](../previous/media-services-rest-storage-encryption.md) (AES-256) tillhandahålls av Media Services.
    * Du kan inte skapa nya tillgångar med äldre AES-256 [lagringskryptering](../previous/media-services-rest-storage-encryption.md) med v3-API: er.
* SDK: er för v3 är nu fristående från Storage SDK, som ger dig större kontroll över versionen av Storage SDK du vill använda och på så sätt undviker problem med versionshantering. 
* I API: er med v3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig från v2 förinställningar för Media Encoder Standard. Till exempel bithastigheten i v2 skulle anges som 128 (kbit/s), men i v3 det vore 128000 (bitar per sekund). 
* Entiteter AssetFiles och AccessPolicies IngestManifests finns inte i v3.
* Egenskapen IAsset.ParentAssets finns inte i v3.
* ContentKeys är inte längre en entitet, är det nu en egenskap för den Strömningspositionerare.
* Event Grid stöd ersätter NotificationEndpoints.
* Följande enheter har bytt namn
    * Jobbutdata ersätter aktiviteten och ingår nu i ett jobb.
    * Positionerare för direktuppspelning ersätter lokaliserare.
    * Live-händelse ersätter kanal.<br/>Live-händelser som fakturering baseras på livekanal mätare. Mer information finns i [fakturering](live-event-states-billing.md) och [priser](https://azure.microsoft.com/pricing/details/media-services/).
    * Live utdata ersätter programmet.
* Live utdata behöver inte uttryckligen startas, de börjar vid skapandet och avbryts när tas bort. Program som fungerade inte på samma sätt i v2-API: er som de hade startas när du har skapat.
*  Om du vill ha information om ett jobb som du behöver veta Transform-namnet som har skapats för jobbet. 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funktionen avstånd till v2 API: er

V3-API: et har följande funktion-avstånd till v2 API: et. De kunskapsluckor är pågående arbete.

* Den [Premium-kodare](../previous/media-services-premium-workflow-encoder-formats.md) och äldre [mediebearbetare för analys](../previous/media-services-analytics-overview.md) (förhandsversion av Azure Media Services Indexer 2, Ansiktsredigering osv.) är inte tillgängliga via v3.<br/>Kunder som vill migrera från Media Indexer 1 eller 2 preview kan omedelbart använda AudioAnalyzer förinställda i v3-API: et.  Den nya förinställningen innehåller fler funktioner än äldre Media Indexer 1 eller 2. 
* Många av de [avancerade funktioner för Media Encoder Standard i v2](../previous/media-services-advanced-encoding-with-mes.md) API: er är för närvarande inte tillgängliga i v3, till exempel:
    * Avklippta (för på begäran och live-scenario)
    * Gå till tillgångar
    * Överlägg
    * Beskärning
    * Miniatyr spriter
    * Infoga en tyst ljudspår när indata har inget ljud
    * Lägga till en video spåra när indata har ingen bild
* Direktsända händelser med transkodning för närvarande stöder inte bakgrundsbild infogning mitten stream och ad insättning via API-anrop. 

> [!NOTE]
> Bokmärk den här artikeln och hålla sökning efter uppdateringar.
 
## <a name="code-differences"></a>Kodskillnaderna

I följande tabell visas kodskillnaderna mellan v2 och v3 för vanliga scenarier.

|Scenario|V2 API|V3 API|
|---|---|---|
|Skapa en tillgång och överföra en fil |[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Skicka ett jobb|[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Visar hur du först skapa en transformering och sedan skicka ett jobb.|
|Publicera en tillgång med AES-kryptering |1. Create ContentKeyAuthorizationPolicyOption<br/>2. Create ContentKeyAuthorizationPolicy<br/>3. Create AssetDeliveryPolicy<br/>4. Skapa tillgång och överföra innehåll eller skicka jobb och Använd utdatatillgången<br/>5. Associera AssetDeliveryPolicy med tillgången<br/>6. Skapa ContentKey<br/>7. Koppla ContentKey till tillgången<br/>8. Skapa AccessPolicy<br/>9. Skapa positionerare<br/><br/>[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Skapa innehåll viktiga princip<br/>2. Skapa tillgång<br/>3. Ladda upp innehåll eller använder tillgången som JobOutput<br/>4. Skapa positionerare för direktuppspelning<br/><br/>[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Hämta jobbinformation och hantera jobb |[Hantera jobb med v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Hantera jobb med v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Kända problem

* För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd den [REST API](https://aka.ms/ams-v3-rest-sdk), CLI, eller någon av de stödda SDK: erna.
* Du måste etablera Mediereserverade enheter (MRUs) i ditt konto om du vill styra samtidighet och prestanda för dina jobb, särskilt de som som involverar Video eller ljudanalys. Mer information finns i [Skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). Du kan hantera MRUs med [CLI 2.0 för Media Services v3](media-reserved-units-cli-how-to.md)med hjälp av den [Azure-portalen](../previous/media-services-portal-scale-media-processing.md), eller med hjälp av den [v2 API: er](../previous/media-services-dotnet-encoding-units.md). Du måste etablera MRUs, oavsett om du använder Media Services v2 eller v3 API: er.
* Media Services-entiteter som skapats med v3 API inte kan hanteras av v2 API: et.  
* Det rekommenderas inte att hantera enheter som har skapats med v2 API: er v3-API: er. Följande är exempel på skillnaderna gör entiteterna i två versioner inkompatibla:   
    * Jobb och aktiviteter som skapats i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att växla till v3-transformeringar och jobb. Det blir en relativt kort tidsperiod för att övervaka den tillhörande v2 behöver jobb under övergången.
    * Kanaler och program som skapats med v2 (som är mappade till Live-händelser och Live-utdata i v3) kan inte fortsätta att hanteras med v3. Rekommendationen är att växla till v3 Live-händelser och Live utdata vid en lämplig kanal stoppa.<br/>För närvarande kan migrera du inte kanaler som körs kontinuerligt.  

> [!NOTE]
> Den här sidan bevaras som Media Services-teamet gör fortsatt förbättringar till v3-API: er och bemöta luckor mellan versionerna.

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

