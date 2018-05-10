---
title: Konfigurera en princip för auktorisering av innehållsnyckel med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 33b958b97a5883d585bbfda167db35107c0c5997
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurera en princip för auktorisering av innehållsnyckel
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera MPEG DASH, Smooth Streaming och HTTP Live Streaming (HLS) dataströmmar som skyddas med Advanced Encryption Standard (AES) med hjälp av krypteringsnycklar för 128-bitars eller [(DRM)PlayReadydigitalrightsmanagement](https://www.microsoft.com/playready/overview/). Med Media Services kan leverera du också DASH krypterat med Widevine DRM-dataströmmar. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också ett nyckel/Licensleveranstjänst som klienter kan hämta AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Den här artikeln visar hur du använder Azure-portalen för att konfigurera principen för auktorisering av innehållsnyckel. Nyckeln kan senare användas för att kryptera dynamiskt innehåll. För närvarande kan du kryptera HLS, MPEG DASH och Smooth Streaming format. Det går inte att kryptera progressiv hämtning.

När en spelare begär en ström som har angetts ska vara dynamiskt krypterad, använder Media Services konfigurerade nyckel för att kryptera dynamiskt innehåll med hjälp av AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. För att avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Om du planerar att ha flera innehåll nycklar eller vill ange en key-licens leverans URL än Media Services viktiga tjänsten Använd Media Services .NET SDK eller REST API: er. Mer information finns i:

* [Konfigurera en princip för auktorisering av innehållsnyckel med hjälp av Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurera en princip för auktorisering av innehållsnyckel med hjälp av Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vissa förutsättningar gäller
* När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta strömning ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste strömmande slutpunkten vara i tillståndet ”körs”. 
* Din tillgång måste innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer. Mer information finns i [koda en tillgång](media-services-encode-asset.md).
* Tjänsten key cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (alternativ och begränsningar) i 15 minuter. Du kan skapa en ContentKeyAuthorizationPolicy och ange om du vill använda en token begränsning, testa den och uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen.
* Ett Media Services strömningsslutpunkt anger värdet för huvudet CORS Access Control-Tillåt-ursprung preflight-svar som jokertecknet ”\*”. Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player Roku och JWPlayer och andra. Dock vissa spelare som använder dash.js fungerar inte eftersom med autentiseringsuppgifter inställd på ”innehåller”, XMLHttpRequest i sina dash.js inte tillåter jokertecknet ”\*” som värde för Access Control-Tillåt-ursprung. Som en tillfällig lösning till den här begränsningen i dash.js om du är värd för din klient från en enda domän, ange Media Services domänen i svarshuvudet Preflight. Öppna ett supportärende via Azure portal om du behöver hjälp.

## <a name="configure-the-key-authorization-policy"></a>Konfigurera innehållsnyckelns auktoriseringsprincip
Om du vill konfigurera innehållsnyckelns auktoriseringsprincip, Välj den **INNEHÅLLSSKYDD** sidan.

Media Services stöder flera olika sätt att autentisera användare som begär nycklar. Principen för auktorisering av innehållsnyckel kan ha öppna, token eller begränsningar för IP-auktorisering. (IP kan konfigureras med REST eller .NET SDK.)

### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsning innebär systemet levererar nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Tokenbegränsningar
Välj den tokenbegränsade principen genom att markera den **TOKEN** knappen.

Den tokenbegränsade principen måste åtföljas av en token som utfärdas av en säkerhetstokentjänst (STS). Media Services stöder token i simple web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) och JSON-Webbtoken (JWT)-format. Mer information finns i [JWT autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services ger inte STS. Du kan skapa en anpassad STS eller använda Azure Access Control Service problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Om token är giltig och anspråk i token som matchar de som konfigurerats för att innehållsnyckeln, returnerar Media Services viktiga tjänsten krypteringsnyckeln till klienten. Mer information finns i [Använd Azure åtkomstkontrolltjänsten problemet tokens](http://mingfeiy.com/acs-with-key-services).

Du måste ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar när du konfigurerar begränsad token-principen. Primär Verifieringsnyckeln innehåller den nyckel som token som signerats med. Utfärdaren är STS som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

### <a name="playready"></a>PlayReady
När du skyddar ditt innehåll med PlayReady är en av de saker som du måste ange i principen för auktorisering en XML-sträng som definierar mallen PlayReady-licens. Som standard anges följande princip:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Du kan välja den **Importera princip-xml** knappen och ange en annan XML som överensstämmer med det XML-schema som definierats i den [Media Services PlayReady licens mall översikt](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

