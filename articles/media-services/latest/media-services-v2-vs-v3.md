---
title: Migrera från Azure Media Services v2 till v3
description: I den här artikeln beskrivs ändringar som introducerades i Azure Media Services v3 och visar skillnader mellan två versioner.
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
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087831"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3

I den här artikeln beskrivs ändringar som introducerades i Azure Media Services v3 och visar skillnader mellan två versioner.

## <a name="general-changes-from-v2"></a>Allmänna ändringar från v2

* För tillgångar som skapats med v3 stöder Media Services endast [lagringskrypteringen på Azure Storage-serversidan](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Du kan använda v3 API:er med resurser som skapats med virtuella API:er som hade [lagringskryptering](../previous/media-services-rest-storage-encryption.md) (AES 256) som tillhandahålls av Media Services.
    * Du kan inte skapa nya resurser med den äldre AES [256-lagringskrypteringen](../previous/media-services-rest-storage-encryption.md) med v3 API:er.
* [Egenskaperna för tillgången](assets-concept.md)i v3 skiljer sig från v2, se hur egenskaperna [kartlägger](#map-v3-asset-properties-to-v2).
* V3 SDK:er är nu frikopplade från Storage SDK, vilket ger dig mer kontroll över den version av Lagring SDK som du vill använda och undviker versionsproblem. 
* I v3 API:erna är alla kodningsbithastigheter i bitar per sekund. Detta skiljer sig från förinställningarna v2 Media Encoder Standard. Till exempel skulle bithastigheten i v2 anges som 128 (kbps), men i v3 skulle det vara 128000 (bitar/sekund). 
* Enheter AssetFiles, AccessPolicies och IngestManifests finns inte i v3.
* Egenskapen IAsset.ParentAssets finns inte i v3.
* ContentKeys är inte längre en entitet, det är nu en egenskap för streaming locator.
* Stöd för Event Grid ersätter NotificationEndpoints.
* Följande entiteter döptes om
    * Jobbutdata ersätter aktivitet och är nu en del av ett jobb.
    * Streaming Locator ersätter Locator.
    * Live Event ersätter Kanal.<br/>Fakturering för livehändelser baseras på mätare på Live Channel. Mer information finns i [fakturering](live-event-states-billing.md) och [prissättning](https://azure.microsoft.com/pricing/details/media-services/).
    * Live Output ersätter Programmet.
* Liveutdata startar när de skapas och avbryts när de tas bort. Program fungerade annorlunda i v2 API: er, de måste startas efter skapandet.
* Om du vill ha information om ett jobb måste du känna till det transformeringsnamn som jobbet skapades under. 
* I v2 genereras [XML-indata-](../previous/media-services-input-metadata-schema.md) och [utdatametadatafiler](../previous/media-services-output-metadata-schema.md) som ett resultat av ett kodningsjobb. I v3 ändrades metadataformatet från XML till JSON. 
* I Media Services v2 kan initieringsvektor (IV) anges. I Media Services v3 kan FairPlay IV inte anges. Även om det inte påverkar kunder som använder Media Services för både paketering och licensleverans, kan det vara ett problem när du använder ett DRM-system från tredje part för att leverera FairPlay-licenserna (hybridläge). I så fall är det viktigt att veta att FairPlay IV härleds från cbcs nyckel-ID och kan hämtas med den här formeln:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    med

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Mer information finns i [Azure Functions C#-koden för Media Services v3 i hybridläge för både Live- och VOD-åtgärder](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Granska namngivningskonventionerna som tillämpas på [mediatjänster v3-resurser](media-services-apis-overview.md#naming-conventions). Granska även [namngivningsblobar](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funktionsluckor jämfört med v2 API:er

V3 API har följande funktionsluckor med avseende på v2 API. Att täppa till luckorna pågår.

* [Premium-kodaren](../previous/media-services-premium-workflow-encoder-formats.md) och de äldre [medieanalysprocessorerna](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview, Face Redactor, etc.) är inte tillgängliga via v3.<br/>Kunder som vill migrera från förhandsversionen av Media Indexer 1 eller 2 kan omedelbart använda förinställningen AudioAnalyzer i v3-API:et.  Den här nya förinställningen innehåller fler funktioner än den äldre Media Indexer 1 eller 2. 
* Många av de [avancerade funktionerna i Media Encoder Standard i v2](../previous/media-services-advanced-encoding-with-mes.md) API:er är för närvarande inte tillgängliga i v3, till exempel:
  
    * Sömnad av tillgångar
    * Överlägg
    * Beskärning
    * Miniatyr sprites
    * Infoga ett tyst ljudspår när ingången inte har något ljud
    * Infoga ett videospår när inmatningen inte har någon video
* Live-händelser med omkodning stöder för närvarande inte Slate-insättning mellanströms- och annonsmarkörinfogning via API-anrop. 
 
## <a name="asset-specific-changes"></a>Tillgångsspecifika ändringar

### <a name="map-v3-asset-properties-to-v2"></a>Mappa v3 tillgångsegenskaper till v2

Följande tabell visar hur [tillgångens](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)egenskaper i v3 mappas till Tillgångens egenskaper i v2.

|v3-egenskaper|v2-egenskaper|
|---|---|
|`id`- (unik) den fullständiga Azure Resource Manager-sökvägen, se exempel i [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (unik) se [Namngivningskonventioner](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (unikt) värde `nb:cid:UUID:` börjar med prefixet.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(skapande alternativ)|
|`type`||

### <a name="storage-side-encryption"></a>Kryptering på lagringssidan

För att skydda dina tillgångar i vila bör tillgångarna krypteras av krypteringen på lagringssidan. Följande tabell visar hur kryptering på lagringssidan fungerar i Media Services:

|Krypteringsalternativ|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Kryptering av lagring av Media Services|AES-256 kryptering, nyckel hanteras av Media Services.|Stöds<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering av lagringstjänster för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden.|Stöds|Stöds|
|[Kryptering på klientsidan för lagring](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Kryptering på klientsidan som erbjuds av Azure-lagring, nyckel som hanteras av kunden i Key Vault.|Stöds inte|Stöds inte|

<sup>1</sup> Medan Media Services stöder hantering av innehåll i klar/utan någon form av kryptering, rekommenderas inte detta.

<sup>2</sup> I Media Services v3 stöds lagringskryptering (AES-256-kryptering) endast för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär v3 fungerar med befintliga lagring krypterade tillgångar men kommer inte att tillåta skapandet av nya.

## <a name="code-differences"></a>Kodskillnader

I följande tabell visas kodskillnaderna mellan v2 och v3 för vanliga scenarier.

|Scenario|V2-API|V3-API|
|---|---|---|
|Skapa en tillgång och ladda upp en fil |[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Skicka ett jobb|[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Visar hur du först skapar en transformering och skickar sedan ett jobb.|
|Publicera en tillgång med AES-kryptering |1. Skapa ContentKeyAuthorizationPolicyOptionOption<br/>2. Skapa ContentKeyAuthorizationPolicy<br/>3. Skapa AssetDeliveryPolicy<br/>4. Skapa tillgång och ladda upp innehåll eller skicka jobb och använda utdata<br/>5. Associera TillgångDeliveryPolicy med tillgång<br/>6. Skapa ContentKey<br/>7. Bifoga ContentKey till tillgången<br/>8. Skapa AccessPolicy<br/>9. Skapa locator<br/><br/>[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Skapa princip för innehållsnyckel<br/>2. Skapa tillgång<br/>3. Ladda upp innehåll eller använd Tillgång som JobOutput<br/>4. Skapa streaming locator<br/><br/>[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Hämta jobbinformation och hantera jobb |[Hantera jobb med v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Hantera jobb med v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Vänligen bokmärk den här artikeln och hålla sökandet efter uppdateringar.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

[Migreringsvägledning för flyttning från Media Services v2 till v3](migrate-from-v2-to-v3.md)
