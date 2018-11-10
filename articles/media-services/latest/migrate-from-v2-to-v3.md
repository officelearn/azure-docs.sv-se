---
title: Migrera från Azure Media Services v2 till v3 | Microsoft Docs
description: Den här artikeln redogörs för ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner.
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
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 2f5c0ef63ba150fdad4aea1a0c65269611d56815
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247695"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Riktlinjer för att flytta från Media Services v2 till v3

Den här artikeln redogörs för ändringar som introducerades i Azure Media Services v3, visar skillnaderna mellan två versioner och ger vägledning för migrering.

Om du har en videotjänst som har utvecklats i dag ovanpå den [äldre Media Services v2 API: er](../previous/media-services-overview.md), bör du granska följande riktlinjer och att tänka på innan du migrerar till v3-API: er. Det finns många fördelar och nya funktioner i v3-API som förbättrar utvecklarupplevelse och funktionerna i Media Services. Dock som kallas ut i den [kända problem](#known-issues) avsnitt av den här artikeln, det finns även vissa begränsningar på grund av ändringar mellan API-versioner. Den här sidan bevaras som Media Services-teamet gör fortsatt förbättringar till v3-API: er och bemöta luckor mellan versionerna. 

## <a name="benefits-of-media-services-v3"></a>Fördelarna med Media Services v3

### <a name="api-is-more-approachable"></a>API: et är mer programmera

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. Azure Resource Manager-mallar kan användas för att skapa och distribuera transformeringar, -slutpunkter för direktuppspelning, LiveEvents med mera.
* [Öppna API (även kallat Swagger) specifikationen](https://aka.ms/ams-v3-rest-sdk) dokumentet.
    Visar schemat för alla tjänstkomponenter, inklusive filbaserade kodning.
* SDK: er som är tillgängliga för [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Gå](https://aka.ms/ams-v3-go-ref), och Ruby.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) -integrering för stöd för enkla skript.

### <a name="new-features"></a>Nya funktioner

* Du kan använda en HTTP (S)-URL för filbaserade jobb bearbetning, som indata.
    Du behöver inte ha innehåll som redan lagras i Azure, inte heller att skapa tillgångar.
* Introducerar begreppet [omvandlar](transforms-jobs-concept.md) för filbaserade Jobbearbetning. En transformering kan användas för att skapa återanvändbara konfigurationer för att skapa Azure Resource Manager-mallar och isolera bearbetning av inställningar mellan flera kunder eller klienter.
* En tillgång kan ha [flera StreamingLocators](streaming-locators-concept.md) med olika inställningar för dynamisk paketering och dynamisk kryptering.
* [Innehållsskydd](content-key-policy-concept.md) har stöd för flera viktiga funktioner.
* Du kan strömma direktsända händelser som är upp till 24 timmar.
* Ny låg latens live direktuppspelning support på LiveEvents.
* LiveEvent Preview stöder dynamisk paketering och dynamisk kryptering. Detta gör det möjligt för innehållsskydd på förhandsversion som DASH och HLS paketering.
* LiveOutput är enklare att använda än programmet entiteten i v2-API: er. 
* Du har rollbaserad åtkomstkontroll (RBAC) över dina entiteter. 

## <a name="changes-from-v2"></a>Ändringar från v2

* För tillgångar som har skapats med v3, Media Services stöder endast den [lagringskryptering för Azure Storage serversidan](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Du kan använda v3-API: er med tillgångar som skapas med v2 API: er som hade [lagringskryptering](../previous/media-services-rest-storage-encryption.md) (AES-256) tillhandahålls av Media Services.
    * Du kan inte skapa nya tillgångar med äldre AES-256 [lagringskryptering](../previous/media-services-rest-storage-encryption.md) med v3-API: er.
* SDK: er för v3 är nu fristående från Storage SDK, som ger dig större kontroll över versionen av Storage SDK du vill använda och på så sätt undviker problem med versionshantering. 
* I API: er med v3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig från v2 förinställningar för Media Encoder Standard. Till exempel bithastigheten i v2 skulle anges som 128 (kbit/s), men i v3 det vore 128000 (bitar per sekund). 
* Entiteter AssetFiles och AccessPolicies IngestManifests finns inte i v3.
* ContentKeys är inte längre en entitet, är det nu en egenskap för StreamingLocator.
* Event Grid stöd ersätter NotificationEndpoints.
* Följande enheter har bytt namn
    * JobOutput ersätter aktiviteten och ingår nu i ett jobb.
    * StreamingLocator ersätter lokaliserare.
    * LiveEvent ersätter kanal.
        
        LiveEvents fakturering baseras på livekanal mätare. Mer information finns i [Livestreaming översikt](live-streaming-overview.md#billing) och [priser](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput ersätter programmet.
* LiveOutputs behöver inte uttryckligen startas, de börjar vid skapandet och avbryts när tas bort. Program som fungerade inte på samma sätt i v2-API: er som de hade startas när du har skapat.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funktionen avstånd till v2 API: er

V3-API: et har följande funktion-avstånd till v2 API: et. De kunskapsluckor är pågående arbete.

* Den [Premium-kodare](../previous/media-services-premium-workflow-encoder-formats.md) och äldre [mediebearbetare för analys](../previous/media-services-analytics-overview.md) (förhandsversion av Azure Media Services Indexer 2, Ansiktsredigering osv.) är inte tillgängliga via v3.

    Kunder som vill migrera från Media Indexer 1 eller 2 preview kan omedelbart använda AudioAnalyzer förinställda i v3-API: et.  Den nya förinställningen innehåller fler funktioner än äldre Media Indexer 1 eller 2. 

* Många av de avancerade funktionerna i Media Encoder Standard i v2 API: er är för närvarande inte tillgängliga i v3, till exempel:
    * Avklippta (för på begäran och live-scenario)
    * Gå till tillgångar
    * Överlägg
    * Beskärning
    * Miniatyr spriter
* LiveEvents med transkodning för närvarande stöder inte bakgrundsbild infogning mitten stream, anpassade förinställningar eller ad-insättning via API-anrop. 

> [!NOTE]
> Bokmärk den här artikeln och hålla sökning efter uppdateringar.

## <a name="code-differences"></a>Kodskillnaderna

I följande tabell visas kodskillnaderna mellan v2 och v3 för vanliga scenarier.

|Scenario|V2 API|V3-API|
|---|---|---|
|Skapa en tillgång och överföra en fil |[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Skicka ett jobb|[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Visar hur du först skapa en transformering och sedan skicka ett jobb.|
|Publicera en tillgång med AES-kryptering |1. Skapa ContentKeyAuthorizationPolicyOption<br/>2. Skapa ContentKeyAuthorizationPolicy<br/>3. Skapa AssetDeliveryPolicy<br/>4. Skapa tillgång och överföra innehåll eller skicka jobb och Använd utdatatillgången<br/>5. Associera AssetDeliveryPolicy med tillgången<br/>6. Skapa ContentKey<br/>7. Koppla ContentKey till tillgången<br/>8. Skapa AccessPolicy<br/>9. Skapa positionerare<br/><br/>[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Skapa innehåll viktiga princip<br/>2. Skapa tillgång<br/>3. Ladda upp innehåll eller använder tillgången som JobOutput<br/>4. Skapa StreamingLocator<br/><br/>[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Kända problem

* För närvarande kan använda du inte Azure-portalen för att hantera v3-resurser. Använd den [REST API](https://aka.ms/ams-v3-rest-sdk), CLI, eller någon av de stödda SDK: erna.
* Mediereserverade enheter kan idag endast hanteras med hjälp av Media Services v2 API. Mer information finns i [skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md).
* Media Services-entiteter som skapats med v3 API inte kan hanteras av v2 API: et.  
* Det rekommenderas inte att hantera enheter som har skapats med v2 API: er v3-API: er. Följande är exempel på skillnaderna gör entiteterna i två versioner inkompatibla:   
    * Jobb och aktiviteter som skapats i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att växla till v3-transformeringar och jobb. Det blir en relativt kort tidsperiod för att övervaka den tillhörande v2 behöver jobb under övergången.
    * Kanaler och program som skapats med v2 (som är mappade till LiveEvents och LiveOutputs i v3) kan inte fortsätta att hanteras med v3. Rekommendationen är att växla till v3 LiveEvents och LiveOutputs vid en lämplig kanal stoppa.
    
        För närvarande kan migrera du inte kanaler som körs kontinuerligt.  
> [!NOTE]
> Bokmärk den här artikeln och hålla sökning efter uppdateringar.

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

