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
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155727"
---
# <a name="content-key-policies"></a>Viktiga innehållsprinciper

Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.

Om du vill ange alternativ för kryptering på din stream, måste du skapa en [Streaming princip](streaming-policy-concept.md) och associera det med dina [Strömningspositionerare](streaming-locators-concept.md). Du måste skapa en [innehåll nyckel princip](https://docs.microsoft.com/rest/api/media/contentkeypolicies) att konfigurera hur innehållsnyckeln (som ger säker åtkomst till din [tillgångar](assets-concept.md)) levereras om du vill avsluta klienter. Den **innehåll nyckel princip** är även associerat med din **Strömningspositionerare**. Du måste ange krav (begränsningar) på innehåll nyckel principen som måste uppfyllas för att nycklar med den angivna konfigurationen som ska levereras till klienterna. 

Vi rekommenderar att Media Services för att skapa nycklar. Du skulle normalt en standardlagringen av långlivade nyckel och kontrollera om principer med **hämta**. Om du vill hämta nyckel, måste du anropa en separat åtgärd-metod för att hämta hemligheter eller autentiseringsuppgifter, finns i följande exempel.

**Innehålls-principer för nycklar** kan uppdateras. Du kanske exempelvis vill uppdatera principen om du behöver göra en rotation av. Du kan uppdatera den primära Verifieringsnyckeln och listan över alternativa verifieringsnycklar i den befintliga principen. Det kan ta upp till 15 minuter för leverans av nyckel-cacheminne för att uppdatera och hämta den uppdaterade policyn. 

> [!IMPORTANT]
> * Egenskaper för **Innehållsprinciper nyckel** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="example"></a>Exempel

Gå till nyckeln genom att använda **GetPolicyPropertiesWithSecretsAsync**, vilket visas i exemplet nedan.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst](protect-with-aes128.md)
* [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
