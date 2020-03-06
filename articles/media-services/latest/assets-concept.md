---
title: Tillgångar
titleSuffix: Azure Media Services
description: Lär dig mer om vilka till gångar som är och hur de används av Azure Media Services.
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
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303621"
---
# <a name="assets-in-azure-media-services"></a>Till gångar i Azure Media Services

I Azure Media Services är en [till gång](https://docs.microsoft.com/rest/api/media/assets) ett Core-begrepp. Det är här du matar in media (till exempel via överföring eller Live-inmatning), utdata-medium (från ett jobb) och publicerar media från (för strömning). 

En till gång mappas till en BLOB-behållare i [Azure Storage-kontot](storage-account-concept.md) och filerna i till gången lagras som block-blobbar i den behållaren. Till gångar innehåller information om digitala filer som lagras i Azure Storage (inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning).

Media Services stöder BLOB-nivåer när kontot använder General-Purpose v2-lagring (GPv2). Med GPv2 kan du flytta filer till låg frekvent [lagring eller Arkiv lag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)ring. **Arkiv** lag ring är lämpligt för att arkivera källfiler när de inte längre behövs (till exempel efter att de har kodats).

**Arkiv** lag rings nivån rekommenderas endast för mycket stora källfiler som redan har kodats och kodnings jobbets utdata lades till i en utgående BLOB-behållare. De blobbar i behållaren för utdata som du vill associera med en till gång och som används för att **strömma eller analysera** innehållet måste finnas på en frekvent eller låg **frekvent lagrings** nivå.

## <a name="naming"></a>Namngivning 

### <a name="assets"></a>Tillgångar

Till gångens namn måste vara unika. Media Services v3-resurs namn (till exempel till gångar, jobb, transformeringar) lyder under Azure Resource Manager namngivnings begränsningar. Mer information finns i [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobar

Namnen på filer/blobbar i en till gång måste följa både BLOB- [namnets krav](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) och [kraven för NTFS-namn](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från Blob Storage till en lokal NTFS-disk för bearbetning.

## <a name="map-v3-asset-properties-to-v2"></a>Mappa v3 till gångs egenskaper till v2

Följande tabell visar hur [till gångens](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)egenskaper i v3 mappar till till gångens egenskaper i v2.

|v3-egenskaper|v2-egenskaper|
|---|---|
|`id` – (unik) fullständig Azure Resource Manager sökväg, se exempel i [till gång](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` – (unik) se [namngivnings konventioner](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-(unikt) värde börjar med prefixet `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (alternativ för att skapa)|
|`type`||

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services:

|Krypteringsalternativet|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering|AES-256-kryptering, nyckel som hanteras av Media Services.|Stöds<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering för lagringstjänst för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på Server sidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden.|Stöds|Stöds|
|[Kryptering av lagring på klient Sidan](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Kryptering på klient sidan som erbjuds av Azure Storage, nyckel som hanteras av kunden i Key Vault.|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services stöder hantering av innehåll i Clear/utan någon form av kryptering, vilket gör att det inte rekommenderas.

<sup>2</sup> i Media Services v3 stöds inte lagrings kryptering (AES-256-kryptering) för bakåtkompatibilitet när dina till gångar skapades med Media Services v2. Det innebär att v3 fungerar med befintliga lagrings krypterade till gångar men tillåter inte att nya skapas.

## <a name="next-steps"></a>Nästa steg

[Hantera till gångar i Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Se även

[Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)
