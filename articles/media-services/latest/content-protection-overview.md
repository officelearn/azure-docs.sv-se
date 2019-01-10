---
title: Skydda ditt innehåll med Media Services – Azure | Microsoft Docs
description: Den här artikeln ger en översikt över innehållsskydd med Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2a5383952f64edb08717b1a100c74352c9b4246f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187985"
---
# <a name="content-protection-overview"></a>Content protection-översikt

Du kan använda Azure Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda innehåll](./media/content-protection/content-protection.png)

&#42;*dynamisk kryptering stöder AES-128 ”clear key”, CBCS och CENC. Mer information finns i supportmatrisen [här](#streaming-protocols-and-encryption-types).*

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services. Artikeln innehåller också de [vanliga frågor och svar](#faq) avsnittet och innehåller länkar till artiklar som visar hur du skyddar innehållet. 

## <a name="main-components-of-the-content-protection-system"></a>Huvudkomponenterna i systemet innehållsskydd

För att slutföra utformningen ”content protection” system/program, måste du helt förstå omfattningen för arbetet. I följande lista ger en översikt av tre delar som skulle du behöva implementera. 

1. Azure Media Services-kod
  
  * Licens mallar för PlayReady, Widevine och FairPlay. Mallarna kan du konfigurera rättigheter och behörigheter för var och en av de använda DRM: er
  * Auktorisering för leverans av licens, att ange logik för auktorisering baserat på anspråk i JWT
  * Innehållsnycklar, strömningsprotokoll och motsvarande DRMs tillämpas, definierar DRM-kryptering

  > [!NOTE]
  > Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera.
  
  Följande artiklar visar stegen för att kryptera innehåll med AES och/eller DRM: 
  
  * [Skydda med AES-kryptering](protect-with-aes128.md)
  * [Skydda med DRM](protect-with-drm.md)

2. Spelare med AES eller DRM-klienten. En videospelare app baserat på en spelare SDK (intern eller webbläsarbaserade) måste uppfylla följande krav:
  * Player SDK stöder de nödvändiga DRM-klienterna
  * Player SDK stöder de nödvändiga protokollen med direktuppspelning: Smooth, DASH eller HLS
  * Player SDK behöver kunna hantera Skicka en JWT-token i begäran för licens-förvärv
  
    Du kan skapa en spelare med hjälp av den [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Använd den [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) att ange vilka DRM-teknik för att använda på olika DRM-plattformar.

    För testning AES eller CENC (Widevine och/eller PlayReady) krypterat innehåll, du kan använda [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Kontrollera att du klickar på ”Avancerat” och kontrollera dina alternativ för kryptering.

    Om du vill testa FairPlay krypterat innehåll, använda [det här testet player](https://aka.ms/amtest). Media player stöder Widevine, PlayReady, och FairPlay DRM: er samt AES-128 Rensa nyckelkryptering. Du måste välja rätt webbläsaren om du vill testa olika DRM: er: Chrome/Opera/Firefox för Widevine, Microsoft Edge/IE11 för PlayReady, Safari på macOS för FairPlay.

3. Skydda säkerhetstokentjänst (STS), som utfärdar JSON Web Token (JWT) som åtkomsttoken för åtkomst till serverdelen. Du kan använda AMS-licensleveranstjänster som backend-resurs. En STS har du definiera följande:

  * Utfärdare och målgrupp (eller omfång)
  * Anspråk som är beroende av affärskrav i innehållsskydd
  * Symmetriskt eller asymmetriskt verifiering för signaturverifiering
  * Stöd för nyckelförnyelse (vid behov)

    Du kan använda [STS verktyget](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) till testet STS, som har stöd för alla 3 typer av verifieringsnyckel: symmetrisk, asymmetrisk, eller AAD med nyckelförnyelse. 

> [!NOTE]
> Vi rekommenderar starkt att fokusera och fullständigt Testa varje del (beskrivs ovan) innan du fortsätter till nästa del. Använd de verktyg som anges i listan ovan om du vill testa systemet ”content protection”.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll för liveuppspelning och krypteringstyper

Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med Klartextnyckel eller DRM-kryptering med PlayReady, Widevine och FairPlay. För närvarande kan kryptera du HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Varje protokoll som stöder följande krypteringsmetoder:

|Protokoll|Behållare-format|Krypteringsschemat|
|---|---|---|---|
|MPEG-DASH|Alla|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Alla|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Dynamisk kryptering

I Media Services v3, en innehållsnyckel är associerad med StreamingLocator (se [det här exemplet](protect-with-aes128.md)). Om du använder Media Services-nyckelleveranstjänst, bör du automatiskt generera innehållsnyckeln. Du bör skapa innehållsnyckeln själv om du använder du egna nyckelleveranstjänst, eller om du behöver hantera ett scenario med hög tillgänglighet, där du måste ha samma innehållsnyckeln i två datacenter.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med klartextnyckel för AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services nyckelleveranstjänst eller nyckelleveranstjänst som du har angett. När du beslutar om användaren har behörighet att hämta nyckel måste utvärderar tjänsten innehåll viktiga principen som du angav för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>Rensa viktiga jämfört med AES-128 DRM

Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den viktigaste skillnaden mellan de två systemen är det att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en nätverksspårning på klienten i oformaterad text. Rensa key AES-128-kryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom företaget kan ses av anställda).

Rensa nyckelkryptering PlayReady, Widevine och FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln skickas i ett krypterat format. Dessutom hanteras dekrypteringen i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta säkerhetsnivån.

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services v3:

|Krypteringsalternativet|Beskrivning|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering| AES-256-kryptering, viktiga hanteras av Media Services|Stöds inte<sup>(1)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|

<sup>1</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar för video-on-demand

Media Services tillhandahåller en nyckelleveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay)-licenser och AES-nycklar till auktoriserade klienter. Du kan använda REST-API eller ett Media Services-klientbibliotek för att konfigurera principer för autentisering och auktorisering för dina licenser och nycklar.

## <a name="control-content-access"></a>Åtkomst till innehåll-kontroll

Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera principen för content-nyckel. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera innehåll viktiga principen. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Innehåll viktiga innehållsnyckeln kan ha **öppna** eller **token** begränsning. 

Med en tokenbegränsade innehåll viktiga princip skickas innehållsnyckeln endast till en klient som anger en giltig JSON Web Token (JWT) eller simple webbtoken (SWT) i nyckel/licens-begäran. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda nyckel/licensen till klienten om token är giltig och att anspråken i token matchar de som konfigurerats för nyckel/licens.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Publik, vilket ibland kallas omfattning, beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>Vanliga frågor och svar

### <a name="question"></a>Fråga

Hur du implementerar multi-DRM (PlayReady, Widevine och FairPlay) system med hjälp av Azure Media Services (AMS) v3 och Använd AMS licensnyckel/delivery service?

### <a name="answer"></a>Svar

Slutpunkt till slutpunkt-scenariot finns det [följande kodexempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs). 

I exempel visas hur du:

1. Skapa och konfigurera ContentKeyPolicies.

  Exemplet innehåller funktioner som konfigurerar [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), och [FairPlay](fairplay-license-overview.md) licenser.

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. Skapa en StreamingLocator som är konfigurerad för att strömma en krypterad tillgång. 

  Du kan till exempel ange StreamingLocator.StreamingPolicyName till principen ”Predefined_MultiDrmCencStreaming”. Den här principen indikerar att du vill att två innehållsnycklar (kuvert och CENC) ska skapas och ställas in för positioneraren. Krypteringarna för kuvert, PlayReady och Widevine tillämpas därför (nyckeln levereras till uppspelningsklienten utifrån de konfigurerade DRM-licenserna). Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”.

3. Skapa en test-token.

  Den **GetTokenAsync** metoden visar hur du skapar ett test-token.
  
4. Skapa strömnings-URL.

  Den **GetDASHStreamingUrlAsync** metoden visar hur du skapar strömnings-URL. I det här fallet, URL-strömmar på **DASH** innehåll.

### <a name="question"></a>Fråga

Hur och var du vill hämta JWT-token innan det att begäran licens eller nyckel?

### <a name="answer"></a>Svar

1. Du måste ha en säker Token tjänster (STS) (webbtjänst) som utfärdar JWT-token på en HTTPS-begäran för produktion. För testning kan du använda koden som visas i **GetTokenAsync** metod som definieras i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player behöver du göra en begäran när en användare autentiseras till STS för sådana en token och tilldela den som värde för token. Du kan använda den [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Ett exempel för att köra STS, med symmetriska och asymmetrisk nyckel finns i [ http://aka.ms/jwt ](https://aka.ms/jwt). 
* Ett exempel på en spelare baserat på Azure Media Player med hjälp av sådana JWT-token finns i [ http://aka.ms/amtest ](https://aka.ms/amtest) (expandera ”player_settings”-länk om du vill se token indata).

### <a name="question"></a>Fråga

Hur du för att godkänna begäranden att strömma videor med AES-kryptering?

### <a name="answer"></a>Svar

Det korrekta sättet är att använda STS (Secure Token Service):

I STS, beroende på användarprofil, lägger du till olika anspråk (till exempel ”Premium-användare”, ”grundläggande användare”, ”kostnadsfri utvärdering användare”). Användaren kan se olika innehållet med olika anspråk i en JWT. Naturligtvis för annat innehåll/tillgången har ContentKeyPolicyRestriction motsvarande RequiredClaims.

Använd Azure Media Services API: er för att konfigurera/licensnyckel leverans och krypterar dina tillgångar (enligt [i det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs).

## <a name="next-steps"></a>Nästa steg

Ta en titt i följande artiklar:

  * [Skydda med AES-kryptering](protect-with-aes128.md)
  * [Skydda med DRM](protect-with-drm.md)

Mer information finns i [utforma multi-drm innehållsskydd system med åtkomstkontroll](design-multi-drm-system-with-access-control.md)


