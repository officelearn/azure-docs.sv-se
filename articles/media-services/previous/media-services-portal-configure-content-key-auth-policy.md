---
title: Konfigurera en behörighetsprincip för innehållsnyckel med hjälp av Azure-portalen | Microsoft-dokument
description: Den här artikeln visar hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968807"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurera en behörighetsprincip för innehållsnyckel
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera strömmar av MPEG-DASH, Smooth Streaming och HTTP Live Streaming (HLS) som skyddas med Advanced Encryption Standard (AES) med hjälp av 128-bitars krypteringsnycklar eller [Drm (PlayReady digital rights management).](https://www.microsoft.com/playready/overview/) Med Media Services kan du även leverera DASH-strömmar krypterade med Widevine DRM. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en nyckel/licensleveranstjänst från vilken klienter kan få AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Den här artikeln visar hur du använder Azure-portalen för att konfigurera behörighetsprincipen för innehållsnyckeln. Nyckeln kan senare användas för att dynamiskt kryptera ditt innehåll. För närvarande kan du kryptera HLS-, MPEG-DASH- och Smooth Streaming-format. Du kan inte kryptera progressiva nedladdningar.

När en spelare begär en ström som är inställd på att krypteras dynamiskt använder Media Services den konfigurerade nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES- eller DRM-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de auktoriseringsprinciper som du har angett för nyckeln.

Om du planerar att ha flera innehållsnycklar eller vill ange en annan url för nyckel-/licensleveranstjänsten än medietjänsternas nyckelleveranstjänst använder du API:erna för Media Services .NET SDK eller REST. Mer information finns i:

* [Konfigurera en behörighetsprincip för innehållsnyckel med hjälp av Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurera en behörighetsprincip för innehållsnyckel med hjälp av REST-API:et för Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vissa överväganden gäller
* När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill börja strömma ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning vara i tillståndet "Köra". 
* Din tillgång måste innehålla en uppsättning adaptiva bitrate MP4s eller adaptiv bitrate Smooth Streaming-filer. Mer information finns i [Koda en tillgång](media-services-encode-asset.md).
* Nyckelleveranstjänsten cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (principalternativ och begränsningar) i 15 minuter. Du kan skapa en ContentKeyAuthorizationPolicy och ange att använda en tokenbegränsning, testa den och sedan uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen.
* En slutpunkt för direktuppspelning av Media Services anger värdet för CORS Access-Control-Allow-Origin-huvudet i preflight-svar som jokertecken "\*". Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player, Roku och JWPlayer och andra. Vissa spelare som använder dash.js fungerar dock inte eftersom XMLHttpRequest i deras dash.js inte tillåter jokertecken\*" " som värdet för Access-Control-Allow-Origin. Som en lösning på den här begränsningen i dash.js kan Media Services ange domänen i svarshuvudet för preflight. Om du behöver hjälp kan du öppna en supportbiljett via Azure-portalen.

## <a name="configure-the-key-authorization-policy"></a>Konfigurera nyckelauktoriseringsprincipen
Om du vill konfigurera nyckelauktoriseringsprincipen väljer du sidan **INNEHÅLLSSKYDD.**

Media Services stöder flera sätt att autentisera användare som gör viktiga begäranden. Behörighetsprincipen för innehållsnyckel kan ha begränsningar för öppen, token eller IP-auktorisering. (IP kan konfigureras med REST eller .NET SDK.)

### <a name="open-restriction"></a>Öppen begränsning
Den öppna begränsningen innebär att systemet levererar nyckeln till alla som gör en nyckelbegäran. Den här begränsningen kan vara användbar för testning.

![OpenPolicy (OpenPolicy)][open_policy]

### <a name="token-restriction"></a>Token begränsning
Om du vill välja principen **TOKEN** tokenbegränsade väljer du token-knappen.

Principen för tokenbegränsad måste åtföljas av en token som utfärdats av en säkerhetstokentjänst (STS). Media Services stöder token i formatet Enkel webbtoken[(SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) och JSON Web Token (JWT). Mer information finns i [JWT-autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services tillhandahåller inte STS. Du kan skapa en anpassad STS för att utfärda token. STS måste konfigureras för att skapa en token som har signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen. Om token är giltig och anspråken i token matchar de som konfigurerats för innehållsnyckeln returnerar Media Services nyckelleveranstjänst krypteringsnyckeln till klienten.

När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med. Utfärdaren är STS som utfärdar token. Målgruppen (kallas ibland scope) beskriver avsikten med token eller den resurs som token auktoriserar åtkomst till. Medietjänstnyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

### <a name="playready"></a>PlayReady
När du skyddar ditt innehåll med PlayReady är en av de saker du behöver ange i auktoriseringsprincipen en XML-sträng som definierar licensmallen PlayReady. Som standard anges följande princip:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
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

Du kan välja **xml-knappen för importprinciper** och tillhandahålla en annan XML som överensstämmer med XML-schemat som definierats i [licensemallen Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

