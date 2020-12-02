---
title: Migrera från Azure Media Services v2 till v3
description: Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 14544f58bcda56a55cef33de8fe0a70d5859b589
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510955"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner.

## <a name="general-changes-from-v2"></a>Allmänna ändringar från v2

* För till gångs relaterade ändringar, se avsnittet [till gångs ändringar](#asset-specific-changes) som följer.
* V3 SDK: er är nu frikopplade från Storage SDK, vilket ger dig mer kontroll över vilken version av Storage SDK som du vill använda och undviker versions problem. 
* I v3-API: erna är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från inställningarna för v2-Media Encoder Standard. Bit hastigheten i v2 skulle till exempel anges som 128 (kbps), men i v3 skulle den bli 128000 (bitar/sekund). 
* Entiteterna AssetFiles, AccessPolicies och IngestManifests finns inte i v3.
* ContentKeys är inte längre en entitet, det är nu en egenskap för den strömmande lokaliseraren.
* Event Grid-support ersätter NotificationEndpoints.
* Följande entiteter har bytt namn:

   * v3-JobOutput ersätter v2-aktivitet och ingår nu i ett jobb. Indata och utdata finns nu på jobb nivå. Mer information finns i [skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md). 

       Om du vill hämta jobbets förlopp kan du lyssna på EventGrid-händelserna. Mer information finns i [hantera Event Grid händelser](reacting-to-media-services-events.md).
    * Streaming Locator ersätter Locator.
    * Live Event ersätter kanal.<br/>Faktureringen av Live-händelser baseras på Live Channel-mätare. Mer information finns i [fakturering](live-event-states-billing.md) och [priser](https://azure.microsoft.com/pricing/details/media-services/).
    * Live output ersätter program.
* Liveutdata startar när de skapas och avbryts när de tas bort. Program fungerade annorlunda i v2-API: er, de var igång när de har skapats.
* Om du vill hämta information om ett jobb måste du känna till Transformations namnet som jobbet skapades under. 
* I v2 skapas filer för XML- [indata](../previous/media-services-input-metadata-schema.md) och [utdata](../previous/media-services-output-metadata-schema.md) som genereras som ett resultat av ett kodnings jobb. I v3 har metadata-formatet ändrats från XML till JSON. 
* I Media Services v2 kan du ange initierings vektor (IV). Det går inte att ange FairPlay IV i Media Services v3. Även om det inte påverkar kunder som använder Media Services för både paketering och licens leverans, kan det vara ett problem när du använder ett DRM-system från tredje part för att leverera FairPlay-licenser (hybrid läge). I så fall är det viktigt att veta att FairPlay IV härleds från CBCS-nyckel-ID: t och kan hämtas med följande formel:

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

    Mer information finns i [Azure Functions C#-koden för Media Services v3 i hybrid läge för både Live-och VOD-åtgärder](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Granska de namngivnings konventioner som tillämpas på [Media Services v3-resurser](media-services-apis-overview.md#naming-conventions). Granska också [namngivning av blobbar](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funktionsluckor jämfört med v2 API:er

V3-API: et har följande funktions luckor i relation till v2-API: et. Att stänga luckorna är pågående arbete.

* [Premium-kodaren](../previous/media-services-encode-asset.md) och de äldre [Media Analytics-processorerna](../previous/legacy-components.md) (Azure Media Services indexerare 2 för hands version, ansikts bortredigering osv.) är inte tillgängliga via v3.<br/>Kunder som vill migrera från Media Indexer 1 eller 2 för hands versionen kan omedelbart använda AudioAnalyzer-förvalet i v3-API: et.  Den här nya för inställningen innehåller fler funktioner än den äldre Media Indexer 1 eller 2. 
* Många av de [avancerade funktionerna i Media Encoder Standard i v2](../previous/media-services-advanced-encoding-with-mes.md) API: er är för närvarande inte tillgängliga i v3, till exempel:
  
    * Häftning av till gångar
    * Överlägg
    * Beskärning
    * Infoga ett tyst ljud spår när indata inte har något ljud
    * Infoga ett video spår när inmatningen saknar video
* Live-händelser med omkodning stöder för närvarande inte mellanliggande infogning och infogning av AD-markörer via API-anrop. 
* Se `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` exempel kod för metod tips och mönster angående hur du använder v2-REST API på. NetCore SDK.

## <a name="asset-specific-changes"></a>Till gångs vissa ändringar

* För till gångar som skapats med v3 stöder Media Services endast [lagrings kryptering Azure Storage på Server sidan](../../storage/common/storage-service-encryption.md).
    * Du kan använda v3-API: er med till gångar som skapats med v2-API: er med [lagrings kryptering](../previous/media-services-rest-storage-encryption.md) (AES 256) som tillhandahålls av Media Services.
    * Du kan inte skapa nya till gångar med äldre AES 256- [lagrings kryptering](../previous/media-services-rest-storage-encryption.md) med hjälp av v3-API: er.
* [Till gångens](assets-concept.md)egenskaper i v3 skiljer sig åt från v2, se [hur egenskaper mappas](#map-v3-asset-properties-to-v2).
* Egenskapen IAsset. ParentAssets finns inte i v3.

### <a name="map-v3-asset-properties-to-v2"></a>Mappa v3 till gångs egenskaper till v2

Följande tabell visar hur [till gångens](/rest/api/media/assets/createorupdate#asset)egenskaper i v3 mappar till till gångens egenskaper i v2.

|v3-egenskaper|v2-egenskaper|
|---|---|
|`id` – (unik) fullständig Azure Resource Manager Sök väg, se exempel i [till gång](/rest/api/media/assets/createorupdate)||
|`name` -(unik) se [namn konventioner](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(unikt) värde börjar med `nb:cid:UUID:` prefixet.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (alternativ för att skapa)|
|`type`||

### <a name="storage-side-encryption"></a>Kryptering på lagrings Sidan

För att skydda dina till gångar i vila bör till gångarna krypteras med kryptering på lagrings sidan. Följande tabell visar hur lagrings sidans kryptering fungerar i Media Services:

|Krypterings alternativ|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services lagrings kryptering|AES-256-kryptering, nyckel som hanteras av Media Services.|Stöds<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering för lagringstjänst för vilande data](../../storage/common/storage-service-encryption.md)|Kryptering på Server sidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden.|Stöds|Stöds|
|[Kryptering av lagrings Client-Side](../../storage/common/storage-client-side-encryption.md)|Kryptering på klient sidan som erbjuds av Azure Storage, nyckel som hanteras av kunden i Key Vault.|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services stöder hantering av innehåll i Clear/utan någon form av kryptering, vilket gör att det inte rekommenderas.

<sup>2</sup> i Media Services v3 stöds inte lagrings kryptering (AES-256-kryptering) för bakåtkompatibilitet när dina till gångar skapades med Media Services v2. Det innebär att v3 fungerar med befintliga lagrings krypterade till gångar men tillåter inte att nya skapas.

## <a name="code-differences"></a>Kod skillnader

I följande tabell visas kod skillnaderna mellan v2 och v3 för vanliga scenarier.

|Scenario|v2-API|v3-API|
|---|---|---|
|Skapa en till gång och ladda upp en fil |[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Skicka ett jobb|[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Visar hur du först skapar en transformering och sedan skickar ett jobb.|
|Publicera en till gång med AES-kryptering |1. skapa ContentKeyAuthorizationPolicyOption<br/>2. skapa ContentKeyAuthorizationPolicy<br/>3. skapa AssetDeliveryPolicy<br/>4. Skapa till gång och ladda upp innehåll eller skicka jobb och Använd utgående till gång<br/>5. associera AssetDeliveryPolicy med till gång<br/>6. skapa ContentKey<br/>7. bifoga ContentKey till till gång<br/>8. skapa Access policy<br/>9. skapa lokaliserare<br/><br/>[v2 .NET-exempel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. skapa en princip för innehålls nyckel<br/>2. Skapa till gång<br/>3. Ladda upp innehåll eller använd till gång som JobOutput<br/>4. skapa strömmande Locator<br/><br/>[v3 .NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Hämta jobb information och hantera jobb |[Hantera jobb med v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Hantera jobb med v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Skriv bok märke till den här artikeln och fortsätt att söka efter uppdateringar.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Vägledning för migrering för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md)