---
title: Konfigurera en auktoriseringsprincip för innehållsnyckeln med hjälp av Azure-portalen | Microsoft Docs
description: Lär dig mer om att konfigurera en auktoriseringsprincip för en innehållsnyckel.
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
ms.openlocfilehash: b046ce5a8647abe601a6327667241d98445ce1e4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259898"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurera en auktoriseringsprincip för innehållsnyckeln
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera MPEG-DASH, Smooth Streaming och HTTP Live Streaming (HLS) dataströmmar som skyddas med Advanced Encryption Standard (AES) med hjälp av nycklar i 128 bitar eller [PlayReady digital rights management (DRM)](https://www.microsoft.com/playready/overview/). Med medietjänster kan leverera du också DASH krypterat med Widevine DRM-strömmar. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en tjänst för leverans av nyckel/licens som klienter kan hämta nycklar för AES eller PlayReady/Widevine-licenser för att spela upp krypterat innehåll.

Den här artikeln visar hur du använder Azure-portalen för att konfigurera den auktoriseringsprincipen för innehållsnyckeln. Nyckeln kan användas senare att dynamiskt kryptera ditt innehåll. För närvarande kan kryptera du HLS, MPEG-DASH och Smooth Streaming-format. Du kan inte krypteras progressiva nedladdningar.

När en spelare begär en dataström som ska vara dynamiskt krypterad, använder Media Services den konfigurerade för att dynamiskt kryptera ditt innehåll med hjälp av AES eller DRM-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. Tjänsten utvärderar auktoriseringsprinciper som du angav för nyckeln för att avgöra om användaren har behörighet att hämta nyckel.

Om du planerar att ha flera nycklar eller om du vill ange en nyckel/licens delivery service URL än nyckelleveranstjänst för Media Services kan använda Media Services .NET SDK eller REST API: er. Mer information finns i:

* [Konfigurera en auktoriseringsprincip för innehållsnyckeln med hjälp av Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurera en auktoriseringsprincip för innehållsnyckeln med hjälp av Media Services REST-API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vissa förutsättningar gäller
* När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste måste slutpunkten för direktuppspelning vara i tillståndet ”körs”. 
* Din tillgång måste innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer. Mer information finns i [koda en tillgång](media-services-encode-asset.md).
* Nyckelleveranstjänst cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (alternativ och begränsningar) i 15 minuter. Du kan skapa en ContentKeyAuthorizationPolicy och ange om du vill använda en tokenbegränsningar, testa den och sedan uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen.
* Ett Media Services som slutpunkt för direktuppspelning anger värdet för rubriken CORS Access-Control-Allow-Origin preflight-svar som jokertecknet ”\*”. Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player, Roku och JWPlayer och andra. Men vissa spelare med dash.js fungerar inte eftersom med autentiseringsuppgifter inställd på ”inkluderar”, XMLHttpRequest i sina dash.js inte tillåter jokertecknet ”\*” som värde för Access-Control-Allow-Origin. Som en lösning på den här begränsningen i dash.js om du agerar värd för din klient från en enda domän och ange Media Services domänen i preflight-svarshuvudet. Öppna ett supportärende via Azure portal om du behöver hjälp.

## <a name="configure-the-key-authorization-policy"></a>Konfigurera innehållsnyckelns auktoriseringsprincip
Om du vill konfigurera innehållsnyckelns auktoriseringsprincip, Välj den **CONTENT PROTECTION** sidan.

Media Services stöder flera olika sätt att autentisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha öppen, token eller IP-restriktioner för auktorisering. (IP kan konfigureras med REST eller .NET SDK.)

### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsningen innebär att systemet levererar nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Tokenbegränsningar
Välj den tokenbegränsade principen genom att välja den **TOKEN** knappen.

Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säkerhetstokentjänst (STS). Media Services stöder token i simple webbtoken ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) och JSON Web Token (JWT)-format. Mer information finns i [JWT autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services tillhandahåller inte STS. Du kan skapa en anpassad STS för att utfärda token. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Om token är giltig och anspråken i token matchar de som konfigurerats för innehållsnyckeln, returnerar Media Services-nyckelleveranstjänst krypteringsnyckeln till klienten.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är STS som utfärdar en token. Målgruppen (kallas ibland för omfång) beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

### <a name="playready"></a>PlayReady
När du skyddar ditt innehåll med PlayReady är en av de saker som du måste ange i din auktoriseringsprincip en XML-sträng som definierar PlayReady-licensmall. Som standard anges följande princip:

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

Du kan välja den **Importera princip-xml** knappen och ange en annan XML som överensstämmer med det XML-schema som definierats i den [översikt över Media Services PlayReady-licensmallen](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

