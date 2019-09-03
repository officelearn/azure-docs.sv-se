---
title: Migrera från Azure Media Services v2 till v3 | Microsoft Docs
description: Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner. Artikeln innehåller även vägledning för migrering för att flytta från Media Services v2 till v3.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 901542e2a69d2c7880825d76c1d69d3795713ed2
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231167"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Vägledning för migrering för att flytta från Media Services v2 till v3

Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3, Visar skillnader mellan två versioner och ger vägledning om migrering.

Om du har en video tjänst som utvecklats idag ovanpå de [äldre Media Services v2-API: erna](../previous/media-services-overview.md)bör du läsa följande rikt linjer och överväganden innan du migrerar till v3-API: erna. Det finns många fördelar och nya funktioner i v3-API: et som förbättrar utvecklings upplevelsen och funktionerna i Media Services. Som du ser i avsnittet [kända problem](#known-issues) i den här artikeln finns det också vissa begränsningar på grund av ändringar mellan API-versionerna. Den här sidan kommer att behållas när Media Servicess teamet gör fortsatta förbättringar av v3-API: erna och åtgärdar luckorna mellan versionerna. 

> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>Fördelar med Media Services v3
  
### <a name="api-is-more-approachable"></a>API är mer lättare att nå

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. Azure Resource Manager mallar kan användas för att skapa och distribuera transformeringar, slut punkter för direkt uppspelning, Live-händelser och mycket annat.
* [Openapi-specifikation (tidigare kallat Swagger)-](https://aka.ms/ams-v3-rest-sdk) dokument.
    Exponerar schemat för alla tjänst komponenter, inklusive filbaserad kodning.
* SDK: er är tillgängliga för [.net](https://aka.ms/ams-v3-dotnet-ref), .net Core, [Node. js](https://aka.ms/ams-v3-nodejs-ref), [python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)och Ruby.
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

## <a name="changes-from-v2"></a>Ändringar från v2

* För till gångar som skapats med v3 stöder Media Services endast [lagrings kryptering Azure Storage på Server sidan](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Du kan använda v3-API: er med till gångar som skapats med v2-API: er med [lagrings kryptering](../previous/media-services-rest-storage-encryption.md) (AES 256) som tillhandahålls av Media Services.
    * Du kan inte skapa nya till gångar med äldre AES 256- [lagrings kryptering](../previous/media-services-rest-storage-encryption.md) med hjälp av v3-API: er.
* [Till gångens](assets-concept.md)egenskaper i v3 skiljer sig åt från v2, se [hur egenskaper mappas](assets-concept.md#map-v3-asset-properties-to-v2).
* V3 SDK: er är nu frikopplade från Storage SDK, vilket ger dig mer kontroll över vilken version av Storage SDK som du vill använda och undviker versions problem. 
* I v3-API: erna är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från inställningarna för v2-Media Encoder Standard. Bit hastigheten i v2 skulle till exempel anges som 128 (kbps), men i v3 skulle den bli 128000 (bitar/sekund). 
* Entiteterna AssetFiles, AccessPolicies och IngestManifests finns inte i v3.
* Egenskapen IAsset. ParentAssets finns inte i v3.
* ContentKeys är inte längre en entitet, det är nu en egenskap för den strömmande lokaliseraren.
* Event Grid-support ersätter NotificationEndpoints.
* Följande entiteter har bytt namn
    * Jobbets utdata ersätter uppgift och ingår nu i ett jobb.
    * Streaming Locator ersätter Locator.
    * Live Event ersätter kanal.<br/>Faktureringen av Live-händelser baseras på Live Channel-mätare. Mer information finns i [fakturering](live-event-states-billing.md) och [priser](https://azure.microsoft.com/pricing/details/media-services/).
    * Live output ersätter program.
* Liveutdata startar när de skapas och avbryts när de tas bort. Program fungerade annorlunda i v2-API: er, de var igång när de har skapats.
*  Om du vill hämta information om ett jobb måste du känna till Transformations namnet som jobbet skapades under. 

> [!NOTE]
> Granska de namngivnings konventioner som tillämpas på [Media Services v3-resurser](media-services-apis-overview.md#naming-conventions). Granska också [namngivning av blobbar](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funktions luckor med avseende på v2-API: er

V3-API: et har följande funktions luckor i relation till v2-API: et. Att stänga luckorna är pågående arbete.

* [Premium](../previous/media-services-premium-workflow-encoder-formats.md) -kodaren och de äldre [Media Analytics](../previous/media-services-analytics-overview.md) -processorerna (Azure Media Services indexerare 2 för hands version, ansikts bortredigering osv.) är inte tillgängliga via v3.<br/>Kunder som vill migrera från Media Indexer 1 eller 2 för hands versionen kan omedelbart använda AudioAnalyzer-förvalet i v3-API: et.  Den här nya för inställningen innehåller fler funktioner än den äldre Media Indexer 1 eller 2. 
* Många av de [avancerade funktionerna i Media Encoder Standard i v2](../previous/media-services-advanced-encoding-with-mes.md) API: er är för närvarande inte tillgängliga i v3, till exempel:
  
    * Häftning av till gångar
    * Överlägg
    * Beskärning
    * Miniatyr sprit
    * Infoga ett tyst ljud spår när indata inte har något ljud
    * Infoga ett video spår när inmatningen saknar video
* Live-händelser med omkodning stöder för närvarande inte mellanliggande infogning och infogning av AD-markörer via API-anrop. 

> [!NOTE]
> Skriv bok märke till den här artikeln och fortsätt att söka efter uppdateringar.
 
## <a name="code-differences"></a>Kod skillnader

I följande tabell visas kod skillnaderna mellan v2 och v3 för vanliga scenarier.

|Scenario|V2-API|V3-API|
|---|---|---|
|Skapa en till gång och ladda upp en fil |[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Skicka ett jobb|[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Visar hur du först skapar en transformering och sedan skickar ett jobb.|
|Publicera en till gång med AES-kryptering |1. Create ContentKeyAuthorizationPolicyOption<br/>2. Skapa ContentKeyAuthorizationPolicy<br/>3. Skapa AssetDeliveryPolicy<br/>4. Skapa till gång och ladda upp innehåll eller skicka jobb och Använd utgående till gång<br/>5. Koppla AssetDeliveryPolicy till till gång<br/>6. Skapa ContentKey<br/>7. Bifoga ContentKey till till gång<br/>8. Skapa Access policy<br/>9. Skapa lokaliserare<br/><br/>[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Skapa princip för innehålls nyckel<br/>2. Skapa till gång<br/>3. Ladda upp innehåll eller använd till gång som JobOutput<br/>4. Skapa strömmande Locator<br/><br/>[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Hämta jobb information och hantera jobb |[Hantera jobb med v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Hantera jobb med v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Kända problem

* För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-sdk), CLI eller någon av de SDK: er som stöds.
* Du måste etablera medie reserverade enheter (MRUs) i ditt konto för att kunna styra samtidigheten och prestandan för dina jobb, särskilt för video-eller ljud analys. Mer information finns i [Skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). Du kan hantera MRUs med [CLI 2,0 för Media Services v3](media-reserved-units-cli-how-to.md), med hjälp av [Azure Portal](../previous/media-services-portal-scale-media-processing.md)eller [v2-API: er](../previous/media-services-dotnet-encoding-units.md). Du måste etablera MRUs, oavsett om du använder Media Services v2 eller v3-API: er.
* Media Services entiteter som har skapats med v3-API: t kan inte hanteras av v2-API: et.  
* Vi rekommenderar inte att du hanterar entiteter som har skapats med v2-API: er via v3-API: erna. Följande är exempel på skillnader som gör att entiteterna i två versioner inte är kompatibla:   
    * Jobb och uppgifter som skapas i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att växla till v3-transformeringar och-jobb. Det kommer att finnas en relativt kort tids period för att övervaka synlighetssekvensnummer v2-jobben under växlingen.
    * Kanaler och program som skapats med v2 (som mappas till Live-händelser och Live-utdata i v3) kan inte fortsätta hanteras med v3. Rekommendationen är att byta till v3 Live-händelser och direktsända utdata i ett bekvämt kanal avbrott.<br/>För närvarande kan du inte migrera kanaler som körs kontinuerligt.  

> [!NOTE]
> Den här sidan kommer att behållas när Media Servicess teamet gör fortsatta förbättringar av v3-API: erna och åtgärdar luckorna mellan versionerna.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

