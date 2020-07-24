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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6c9f69a39f725b082771b66959a219581c281ed5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043512"
---
# <a name="assets-in-azure-media-services-v3"></a>Till gångar i Azure Media Services v3

I Azure Media Services är en [till gång](/rest/api/media/assets) ett Core-begrepp. Det är här du matar in media (till exempel via överföring eller Live-inmatning), utdata-medium (från ett jobb) och publicerar media från (för strömning). 

En till gång mappas till en BLOB-behållare i [Azure Storage-kontot](storage-account-concept.md) och filerna i till gången lagras som block-blobbar i den behållaren. Till gångar innehåller information om digitala filer som lagras i Azure Storage (inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning).

Media Services stöder BLOB-nivåer när kontot använder General-Purpose v2-lagring (GPv2). Med GPv2 kan du flytta filer till låg frekvent [lagring eller Arkiv lag](../../storage/blobs/storage-blob-storage-tiers.md)ring. **Arkiv** lag ring är lämpligt för att arkivera källfiler när de inte längre behövs (till exempel efter att de har kodats).

**Arkiv** lag rings nivån rekommenderas endast för mycket stora källfiler som redan har kodats och kodnings jobbets utdata lades till i en utgående BLOB-behållare. De blobbar i behållaren för utdata som du vill associera med en till gång och som används för att **strömma eller analysera** innehållet måste finnas på en frekvent eller låg **frekvent lagrings** nivå.

## <a name="naming"></a>Namngivning 

### <a name="assets"></a>Tillgångar

Till gångens namn måste vara unika. Media Services v3-resurs namn (till exempel till gångar, jobb, transformeringar) lyder under Azure Resource Manager namngivnings begränsningar. Mer information finns i [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobar

Namnen på filer/blobbar i en till gång måste följa både BLOB- [namnets krav](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) och [kraven för NTFS-namn](/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från Blob Storage till en lokal NTFS-disk för bearbetning.

## <a name="next-steps"></a>Nästa steg

[Hantera till gångar i Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Se även

[Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)
