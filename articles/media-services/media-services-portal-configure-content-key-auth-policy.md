---
title: "Konfigurera Innehållsnyckelns auktoriseringsprincip med Azure-portalen | Microsoft Docs"
description: "Lär dig hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-content-key-authorization-policy"></a>Konfigurera Innehållsnyckelns auktoriseringsprincip
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
Microsoft Azure Media Services kan du leverera MPEG DASH, Smooth Streaming och HTTP Live Streaming (HLS) dataströmmar som skyddas med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar) eller [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS kan du leverera DASH krypterat med Widevine DRM-dataströmmar. Både PlayReady och Widevine krypteras enligt den gemensamma  krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en **nyckel-/ Licensleveranstjänst** från vilka klienterna kan hämta AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Det här avsnittet visar hur du använder Azure-portalen för att konfigurera principen för auktorisering av innehållsnyckel. Nyckeln kan senare användas för att kryptera dynamiskt innehåll. Observera att för närvarande kan du kryptera strömning följande format: HLS MPEG DASH och Smooth Streaming. Det går inte att kryptera progressiv hämtning.

När en spelare begär en ström som har angetts ska vara dynamiskt krypterad, använder Media Services konfigurerade nyckel för att kryptera dynamiskt innehåll med AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Om du planerar att ha flera nycklar som innehåll eller om du vill ange en **nyckel-/ Licensleveranstjänst** URL än Media Services viktiga tjänsten, använder Media Services .NET SDK eller REST API: er.

[Konfigurera Innehållsnyckelns auktoriseringsprincip med Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Konfigurera Innehållsnyckelns auktoriseringsprincip med Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vissa förutsättningar gäller:
* När AMS-kontot skapas en **standard** strömningsslutpunkt har lagts till i ditt konto i den **stoppad** tillstånd. Om du vill starta strömning ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering strömmande slutpunkten måste vara i den **kör** tillstånd. 
* Din tillgång måste innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer. Mer information finns i [koda en tillgång](media-services-encode-asset.md).
* Tjänsten nyckeln cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (alternativ och begränsningar) i 15 minuter.  Om du skapar en ContentKeyAuthorizationPolicy och ange om du vill använda en ”Token” begränsning, testa, och uppdatera principen till ”öppen” begränsningen, det tar ungefär 15 minuter innan principen växlar till ”öppen” versionen av principen.

## <a name="how-to-configure-the-key-authorization-policy"></a>Så här: Konfigurera innehållsnyckelns auktoriseringsprincip
Om du vill konfigurera innehållsnyckelns auktoriseringsprincip, Välj den **INNEHÅLLSSKYDD** sidan.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Principen för auktorisering av innehållsnyckel kan ha **öppna**, **token**, eller **IP** auktoriseringsbegränsningar (**IP** kan konfigureras med REST- eller .NET SDK).

### <a name="open-restriction"></a>Öppna begränsning
Den **öppna** begränsning innebär systemet ger nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Tokenbegränsningar
Välj den tokenbegränsade principen genom att trycka på den **TOKEN** knappen.

Den **token** tokenbegränsade principen måste åtföljas av en token som utfärdas av en **Secure Token Service** (STS). Media Services stöder token i den **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) format och **JSON Web Token** (JWT)-format. Mer information finns i [JWT tokenautentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services tillhandahåller inte **Secure Token Services**. Du kan skapa en anpassad STS eller använda Microsoft Azure ACS problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returneras krypteringsnyckeln till klienten om token är giltig och anspråk i token som matchar de som konfigurerats för innehållsnyckeln. Mer information finns i [Använd Azure ACS problemet tokens](http://mingfeiy.com/acs-with-key-services).

När du konfigurerar den **TOKEN** tokenbegränsade principen måste du ange värden för **Verifieringsnyckeln**, **utfärdaren** och **målgruppen**. Primära Verifieringsnyckeln innehåller den nyckel som token som signerats med, utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

### <a name="playready"></a>PlayReady
När du skyddar ditt innehåll med **PlayReady**, en av de saker som du måste ange i principen för auktorisering är en XML-sträng som definierar mallen PlayReady-licens. Som standard anges följande princip:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates> <PlayReadyLicenseTemplate> <AllowTestDevices>SANT</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>tillåtna</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates></PlayReadyLicenseResponseTemplate>

Du kan klicka på den **Importera princip-xml** knappen och ange en annan XML som uppfyller det XML-schema som definierats [här](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

