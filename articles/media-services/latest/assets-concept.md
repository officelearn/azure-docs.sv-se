---
title: Tillgångar i Media Services – Azure | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad tillgångar är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0fc44bfdb98b81bf218cb2f1824f0f1bb14de4fa
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235677"
---
# <a name="assets"></a>Tillgångar

I Azure Media Services, en [tillgången](https://docs.microsoft.com/rest/api/media/assets) innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När de digitala filerna överförs till en tillgång, kan de användas i Media Services encoding, strömning, analysera innehållet arbetsflöden. Mer information finns i den [ladda upp digitala filer till tillgångar](#upload-digital-files-into-assets) nedan.

En tillgång är mappad till en blobbehållare i den [Azure Storage-konto](storage-account-concept.md) och filer i tillgången lagras som blockblobar i den behållaren. Media Services stöder Blob nivåerna när kontot använder för generell användning v2 (GPv2) lagring. Med GPv2, kan du flytta filer till [lågfrekvent lagring eller Arkivlagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Arkivera** lagring är lämplig för arkivering av källfilerna när det inte längre behövs (till exempel när de har kodats).

Den **Arkiv** lagringsnivå rekommenderas endast för mycket stora källfiler som redan har kodats och kodning jobbutdata placerades i en utdata-blob-behållare. Blobar i behållaren för utdata som du vill associera med en tillgång och Använd för att strömma eller analysera ditt innehåll måste finnas i en **frekvent** eller **lågfrekvent** lagringsnivå.

> [!NOTE]
> Tillgångs-egenskaper av typen Datetime är alltid i UTC-format.

## <a name="upload-digital-files-into-assets"></a>Ladda upp digitala filer till tillgångar

En av de vanliga arbetsflödena för Media Services är att överföra, koda och överföra en fil. Det här avsnittet beskrivs de allmänna stegen.

> [!TIP]
> Innan du börjar utveckla granska [utveckla med API: er för Media Services v3](media-services-apis-overview.md) (innehåller information om hur du använder API: er, namngivningskonventioner, osv.)

1. Använd Media Services v3 API för att skapa en ny ”indataresurs”. Den här åtgärden skapar en container i det lagringskonto som associeras med ditt Media Services-konto. API: et returnerar behållarens namn (till exempel `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Om du redan har en blobcontainer som du vill associera med en tillgång kan du ange containerns namn när du skapar tillgången. Media Services stöder för närvarande endast blobar i containerroten och inte med sökvägar i filnamnet. Därmed fungerar en container med namnet ”input.mp4”. En container med filnamnet ”videos/inputs/input.mp4” fungerar dock inte.

    Du kan använda Azure CLI för att ladda upp direkt till valfritt lagringskonto och container som du har rättigheter till i din prenumeration. <br/>Containerns namn måste vara unikt och följa riktlinjerna för namngivning för lagring. Namnet måste inte följa formateringen för Media Services-tillgångscontainerns namn (tillgångs-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Hämta en SAS-URL med läs- och skrivbehörigheter som används för att ladda upp digitala filer till tillgångscontainern. Du kan använda Media Services API för att [lista URL:er för tillgångscontainern](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Använd Azure Storage-API:er eller -SDK:er (till exempel [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) för att ladda upp filer till tillgångscontainern. 
4. Använd Media Services v3-API:er för att skapa en transformering och ett jobb för att bearbeta din ”indatatillgång”. Mer information finns i [Transformeringar och jobb](transform-concept.md).
5. Stream innehållet från ”utdatatillgången”.

För en fullständig .NET-exempel som visar hur du: skapa tillgången, få en skrivbar SAS-URL till den tillgången behållare i storage, ladda upp filen till behållaren i storage med SAS-Webbadressen, se [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Skapa en ny tillgång

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

En REST-exempel finns i den [skapa en tillgång med REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) exempel.

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

Ett fullständigt exempel se [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md). I Media Services v3, en jobbindata kan även skapa från HTTPS-URL: er (se [skapa en jobbindata från en HTTPS-URL](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services:

|Krypteringsalternativet|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering|AES-256-kryptering, viktiga hanteras av Media Services|Stöd för<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services har stöd för hantering av innehållet i klartext/utan någon form av kryptering, göra så rekommenderas inte.

<sup>2</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
* [Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)
