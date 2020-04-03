---
title: Principer för innehållsnyckel i Media Services – Azure | Microsoft-dokument
description: Den här artikeln ger en förklaring av vad innehållsnyckelprinciper är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 85a9cad80156dc6ac40e78610c91805d485ff3df
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585994"
---
# <a name="content-key-policies"></a>Viktiga innehållsprinciper

Med Media Services kan du leverera ditt live- och on-demand-innehåll krypterat dynamiskt med Advanced Encryption Standard (AES-128) eller något av de tre stora DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. 

Om du vill ange krypteringsalternativ på flödet måste du skapa en [streamingprincip](streaming-policy-concept.md) och associera den med din [streamingpositionerare](streaming-locators-concept.md). Du skapar [innehållsnyckelprincipen](https://docs.microsoft.com/rest/api/media/contentkeypolicies) för att konfigurera hur innehållsnyckeln (som ger säker åtkomst till dina [tillgångar)](assets-concept.md)levereras till slutklienter. Du måste ange kraven (begränsningarna) för innehållsnyckelprincipen som måste uppfyllas för att nycklar med den angivna konfigurationen ska levereras till klienter. Innehållsnyckelprincipen behövs inte för tydlig direktuppspelning eller nedladdning. 

Vanligtvis associerar du din innehållsnyckelprincip med din [streamingpositionerare](streaming-locators-concept.md). Du kan också ange innehållsnyckelprincipen i en [direktuppspelningsprincip](streaming-policy-concept.md) (när du skapar en anpassad direktuppspelningsprincip för avancerade scenarier). 

## <a name="best-practices-and-considerations"></a>Bästa praxis och överväganden

> [!IMPORTANT]
> Läs igenom följande rekommendationer.

* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina streamingpositionerare när samma alternativ behövs. Mer information finns i [Kvoter och begränsningar](limits-quotas-constraints.md).
* Viktiga principer för innehåll kan uppdateras. Det kan ta upp till 15 minuter innan nyckelleveranscachen uppdaterar och hämtar den uppdaterade principen. 

   Genom att uppdatera principen skriver du över din befintliga CDN-cache, vilket kan orsaka uppspelningsproblem för kunder som använder cachelagrat innehåll.  
* Vi rekommenderar att du inte skapar en ny innehållsnyckelprincip för varje tillgång. De största fördelarna med att dela samma innehållsnyckelpolicy mellan tillgångar som behöver samma policyalternativ är:
   
   * Det är lättare att hantera ett litet antal politikområden.
   * Om du behöver göra uppdateringar av innehållsnyckelprincipen träder ändringarna i kraft på alla nya licensbegäranden nästan direkt.
* Om du behöver skapa en ny princip måste du skapa en ny streaming locator för tillgången.
* Vi rekommenderar att du låter Innehållsnyckeln skapa innehållsnyckeln automatiskt. 

   Vanligtvis använder du en långlivad nyckel och kontrollerar om det finns en princip för innehållsnyckel med [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). För att få nyckeln måste du anropa en separat åtgärdsmetod för att hämta hemligheter eller autentiseringsuppgifter, se exemplet som följer.

## <a name="example"></a>Exempel

Om du vill komma `GetPolicyPropertiesWithSecretsAsync`till nyckeln använder du , som visas i [hämta en signeringsnyckel från det befintliga principexemplet.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtrering, beställning, växling

Se [Filtrering, beställning, växling av Media Services-entiteter](entities-overview.md).

## <a name="additional-notes"></a>Ytterligare information

* Egenskaper för de innehållsnyckelprinciper som är av typen `Datetime` är alltid i UTC-format.
* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

* [Använd dynamisk AES-128-kryptering och nyckelleveranstjänsten](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
* [KodaHTTPOchPublishAESEnkrypterad](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
