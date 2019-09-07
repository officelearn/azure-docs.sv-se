---
title: Till gångar i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad tillgångar är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3dc1866a3c0339bca0c27fb53894a14581e88490
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390506"
---
# <a name="assets"></a>Tillgångar

I Azure Media Services innehåller en [till gång](https://docs.microsoft.com/rest/api/media/assets) information om digitala filer som lagras i Azure Storage (inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning). 

En till gång mappas till en BLOB-behållare i [Azure Storage-kontot](storage-account-concept.md) och filerna i till gången lagras som block-blobbar i den behållaren. Media Services stöder BLOB-nivåer när kontot använder General-Purpose v2-lagring (GPv2). Med GPv2 kan du flytta filer till låg frekvent [lagring eller Arkiv lag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)ring. **Arkiv** lag ring är lämpligt för att arkivera källfiler när de inte längre behövs (till exempel efter att de har kodats).

**Arkiv** lag rings nivån rekommenderas endast för mycket stora källfiler som redan har kodats och kodnings jobbets utdata lades till i en utgående BLOB-behållare. Blobarna i behållaren för utdata som du vill associera med en till gång och använder för att **strömma eller analysera** innehållet måste finnas på en frekvent eller låg frekvent **lagrings** nivå.

### <a name="naming-blobs"></a>Namnge blobbar

Namnen på filer/blobbar i en till gång måste följa både BLOB- [namnets krav](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) och [kraven för NTFS-namn](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från Blob Storage till en lokal NTFS-disk för bearbetning.

## <a name="upload-digital-files-into-assets"></a>Överför digitala filer till till gångar

När de digitala filerna har överförts till lagring och associerats med en till gång, kan de användas i Media Services kodning, strömning, analys av innehålls arbets flöden. En av vanliga Media Services arbets flöden är att ladda upp, koda och strömma en fil. Det här avsnittet beskriver de allmänna stegen.

> [!TIP]
> Innan du börjar utveckla bör du läsa [utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om hur du kommer åt API: er, namngivnings konventioner osv.)

1. Använd Media Services v3 API för att skapa en ny ”indataresurs”. Den här åtgärden skapar en container i det lagringskonto som associeras med ditt Media Services-konto. API: et returnerar behållarens namn (till exempel `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Om du redan har en blobcontainer som du vill associera med en tillgång kan du ange containerns namn när du skapar tillgången. Media Services stöder för närvarande endast blobar i containerroten och inte med sökvägar i filnamnet. Därmed fungerar en container med namnet ”input.mp4”. En container med filnamnet ”videos/inputs/input.mp4” fungerar dock inte.

    Du kan använda Azure CLI för att ladda upp direkt till valfritt lagringskonto och container som du har rättigheter till i din prenumeration. <br/>Containerns namn måste vara unikt och följa riktlinjerna för namngivning för lagring. Namnet måste inte följa formateringen för Media Services-tillgångscontainerns namn (tillgångs-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Hämta en SAS-URL med läs- och skrivbehörigheter som används för att ladda upp digitala filer till tillgångscontainern. Du kan använda Media Services API för att [lista URL:er för tillgångscontainern](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Använd Azure Storage-API: er eller SDK: er (till exempel [lagrings REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) för att ladda upp filer till till gångs behållaren. 
4. Använd Media Services v3-API:er för att skapa en transformering och ett jobb för att bearbeta din ”indatatillgång”. Mer information finns i [Transformeringar och jobb](transform-concept.md).
5. Strömma innehållet från "output"-till gången.

För ett fullständigt .NET-exempel som visar hur du skapar till gången, hämtar en skrivbar SAS-URL till till gångens behållare i Storage, laddar upp filen till behållaren i lagring med SAS-URL: en, se [skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Skapa en ny till gång

> [!NOTE]
> Till gångens egenskaper för datetime-typen är alltid i UTC-format.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Ett REST-exempel finns i exemplet [skapa en till gång med rest](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) .

Exemplet visar hur du skapar **begärandetexten**, där du kan ange användbar information såsom beskrivning, containernamn, lagringskonto och annat.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Ett fullständigt exempel finns i [skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md). I Media Services v3 kan du också skapa ett jobbs inmatare från HTTPS-URL: er (se [skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mappa v3 till gångs egenskaper till v2

Följande tabell visar hur [till gångens](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)egenskaper i v3 mappar till till gångens egenskaper i v2.

|v3-egenskaper|v2-egenskaper|
|---|---|
|ID – (unik) fullständig Azure Resource Manager Sök väg, se exempel i [till gång](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|namn – (unik) se [namn konventioner](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|ID – (unikt) värde börjar med `nb:cid:UUID:` prefixet.|
|skapad|Skapad|
|description|Name|
|lastModified|senast ändrad|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| Alternativ (alternativ för att skapa)|
|type||

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services:

|Krypteringsalternativet|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering|AES-256-kryptering, viktiga hanteras av Media Services|Stöd för<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services har stöd för hantering av innehållet i klartext/utan någon form av kryptering, göra så rekommenderas inte.

<sup>2</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
* [Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)
