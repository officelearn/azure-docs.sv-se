---
title: Tillgångar i Azure Media Services | Microsoft Docs
description: Den här artikeln ger en förklaring av tillgångar är och hur de används i Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Tillgångar

En **tillgången** innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När digitala filer överförs till en tillgång, kan de användas i Media Services encoding och direktöverföring av arbetsflöden.

En tillgång som är mappad till en blobbbehållare i den [Azure Storage-konto](storage-account-concept.md) och filerna i tillgången lagras som blockblobar i behållaren. Du kan interagera med tillgångsfiler i behållare med lagring SDK-klienter.

Azure Media Services stöder Blob nivåerna när kontot använder allmänna v2 (GPv2) lagring. Med GPv2, kan du flytta filer att kyla ner eller kall lagringsplats. Kalla lagring är lämplig för arkivering mezzanine filer när de inte längre behövs (till exempel när de har kodats.)

I Media Services v3 skapas indata jobb från tillgångar eller från http (s)-URL: er. Om du vill skapa en tillgång som kan användas som indata för jobbet finns [skapar ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).

Läs om [storage-konton i Media Services](storage-account-concept.md) och [transformeringar och jobb](transform-concept.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Överföra en fil](stream-files-dotnet-quickstart.md)
