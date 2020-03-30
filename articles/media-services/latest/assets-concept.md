---
title: Tillgångar
titleSuffix: Azure Media Services
description: Läs mer om vilka tillgångar som är och hur de används av Azure Media Services.
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
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087914"
---
# <a name="assets-in-azure-media-services-v3"></a>Tillgångar i Azure Media Services v3

I Azure Media Services är en [tillgång](https://docs.microsoft.com/rest/api/media/assets) ett kärnkoncept. Det är där du matar in media (till exempel via uppladdning eller live-mat, utdatamedia (från en jobbutdata) och publicerar media från (för direktuppspelning). 

En tillgång mappas till en blob-behållare i [Azure Storage-kontot](storage-account-concept.md) och filerna i tillgången lagras som blockblobar i den behållaren. Tillgångar innehåller information om digitala filer som lagras i Azure Storage (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text).

Media Services stöder Blob-nivåer när kontot använder GPv2-lagring (General Purpose v2). Med GPv2 kan du flytta filer till [Cool eller Arkiv storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Arkivlagring** är lämplig för arkivering av källfiler när de inte längre behövs (till exempel efter att de har kodats).

**Lagringsnivån arkiv** rekommenderas endast för mycket stora källfiler som redan har kodats och kodningsjobbutdata lades i en utdatablolobbehållare. Blobbar i utdatabehållaren som du vill associera med en tillgång och använda för att strömma eller analysera ditt innehåll måste finnas på en **hot-** eller cool-lagringsnivå. **Cool**

## <a name="naming"></a>Namngivning 

### <a name="assets"></a>Tillgångar

Tillgångens namn måste vara unika. Media Services v3-resursnamn (till exempel Tillgångar, Jobb, Transformeringar) omfattas av namngivningsbegränsningar i Azure Resource Manager. Mer information finns i [Namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobar

Namnen på filer/blobbar i en tillgång måste följa både [blobnamnskraven](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) och [NTFS-namnkraven](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från blob-lagring till en lokal NTFS-disk för bearbetning.

## <a name="next-steps"></a>Nästa steg

[Hantera tillgångar i Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Se även

[Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)
