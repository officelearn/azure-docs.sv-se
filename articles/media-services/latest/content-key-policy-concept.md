---
title: Innehålls-principer för nycklar i Media Services – Azure | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad Innehållsprinciper nyckel är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733053"
---
# <a name="content-key-policies"></a>Viktiga innehållsprinciper

Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.

Om du vill ange alternativ för kryptering på din stream, måste du skapa den [innehåll nyckel princip](https://docs.microsoft.com/rest/api/media/contentkeypolicies) och associera det med dina **Strömningspositionerare**. Den **innehåll nyckel princip** konfigurerar hur innehållsnyckeln levereras om du vill avsluta klienter via komponenten nyckel leverans av Media Services. Du kan låta Media Services för att skapa innehållsnyckeln. Du skulle normalt en standardlagringen av långlivade nyckel och kontrollera om principer med Get. Om du vill hämta nyckel, måste du anropa en separat åtgärd-metod för att hämta hemligheter eller autentiseringsuppgifter, finns i följande exempel.

**Innehålls-principer för nycklar** kan uppdateras. Du kanske exempelvis vill uppdatera principen om du behöver göra en rotation av. Du kan uppdatera den primära Verifieringsnyckeln och listan över alternativa verifieringsnycklar i den befintliga principen. Det kan ta upp till 15 minuter för leverans av nyckel-cacheminne för att uppdatera och hämta den uppdaterade policyn. 

> [!IMPORTANT]
> * Egenskaper för **Innehållsprinciper nyckel** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. 

## <a name="example"></a>Exempel

Gå till nyckeln genom att använda **GetPolicyPropertiesWithSecretsAsync**, vilket visas i exemplet nedan.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
