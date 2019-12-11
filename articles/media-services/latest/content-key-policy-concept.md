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
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969912"
---
# <a name="content-key-policies"></a>Viktiga innehållsprinciper

Med medietjänster kan du leverera live och på begäran innehåll dynamiskt krypterad med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

Om du vill ange krypterings alternativ för data strömmen måste du skapa en [strömmande princip](streaming-policy-concept.md) och koppla den till din [strömmande positionerare](streaming-locators-concept.md). Du skapar en [innehålls nyckel princip](https://docs.microsoft.com/rest/api/media/contentkeypolicies) för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till [till gångar](assets-concept.md)) levereras till slut klienter. Du måste ange kraven (begränsningar) för den innehålls nyckel princip som måste uppfyllas för att nycklar med den angivna konfigurationen ska skickas till klienter. Innehålls nyckel principen behövs inte för att rensa strömma eller ladda ned. 

Vanligt vis associerar du din innehålls nyckel princip med din [streaming Locator](streaming-locators-concept.md). Du kan också ange innehålls nyckel principen i en [strömmande princip](streaming-policy-concept.md) (när du skapar en anpassad strömnings princip för avancerade scenarier). 

## <a name="best-practices-and-considerations"></a>Metod tips och överväganden

> [!IMPORTANT]
> Kontrol lera följande rekommendationer.

* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina strömmande positionerare när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).
* Innehålls nyckel principer kan uppdateras. Det kan ta upp till 15 minuter för nyckel leverans-cachen att uppdatera och hämta den uppdaterade principen. 

   Genom att uppdatera principen skriver du över din befintliga CDN-cache som kan orsaka uppspelnings problem för kunder som använder cachelagrat innehåll.  
* Vi rekommenderar att du inte skapar en ny innehålls nyckel princip för varje till gång. De största fördelarna med att dela samma innehålls nyckel princip mellan till gångar som behöver samma princip alternativ är:
   
   * Det är enklare att hantera ett litet antal principer.
   * Om du behöver göra uppdateringar av innehålls nyckel principen, kommer ändringarna att börja gälla på alla nya licens förfrågningar nästan direkt.
* Om du behöver skapa en ny princip måste du skapa en ny plats för direkt uppspelning för till gången.
* Vi rekommenderar att Media Services automatiskt genererar innehålls nyckeln. 

   Normalt använder du en lång livs längds nyckel och kontrollerar om det finns en innehålls nyckel princip med [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). För att hämta nyckeln måste du anropa en separat åtgärds metod för att få hemligheter eller autentiseringsuppgifter, se exemplet nedan.

## <a name="example"></a>Exempel

Använd `GetPolicyPropertiesWithSecretsAsync`, som du ser i [Hämta en signerings nyckel från den befintliga princip](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) exemplet, för att komma till nyckeln.

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="additional-notes"></a>Ytterligare information

* Egenskaperna för de innehålls nyckel principer som är av `Datetime` typen är alltid i UTC-format.
* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

* [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
