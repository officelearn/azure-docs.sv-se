---
title: Skydda ditt innehåll med Media Services dynamisk kryptering – Azure | Microsoft Docs
description: Den här artikeln ger en översikt över innehålls skydd med dynamisk kryptering. Den pratar även om strömmande protokoll och krypterings typer.
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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310303"
---
# <a name="content-protection-with-dynamic-encryption"></a>Innehålls skydd med dynamisk kryptering

Du kan använda Azure Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

I Media Services v3 är en innehålls nyckel kopplad till streaming Locator (se [det här exemplet](protect-with-aes128.md)). Om du använder Media Services Key Delivery Service kan du låta Azure Media Services generera innehålls nyckeln åt dig. Du bör generera innehålls nyckeln själv om du använder din egen nyckel leverans tjänst, eller om du behöver hantera ett scenario med hög tillgänglighet där du behöver samma innehålls nyckel i två Data Center.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med klartextnyckel för AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services nyckelleveranstjänst eller nyckelleveranstjänst som du har angett. När du beslutar om användaren har behörighet att hämta nyckel måste utvärderar tjänsten innehåll viktiga principen som du angav för nyckeln.

Du kan använda REST-API eller ett Media Services-klientbibliotek för att konfigurera principer för autentisering och auktorisering för dina licenser och nycklar.

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda innehåll](./media/content-protection/content-protection.svg)

&#42;*dynamisk kryptering stöder AES-128 ”clear key”, CBCS och CENC. Mer information finns i supportmatrisen [här](#streaming-protocols-and-encryption-types).*

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Huvud komponenter i ett innehålls skydds system

För att slutföra utformningen ”content protection” system/program, måste du helt förstå omfattningen för arbetet. I följande lista ger en översikt av tre delar som skulle du behöva implementera. 

1. Azure Media Services-kod
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) -exemplet visar hur du implementerar multi-DRM-system med Media Services v3 med hjälp av .net. Den visar också hur du använder Media Services licens-/nyckel leverans tjänst. Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera.
  
   I exempel visas hur du:

   1. Skapa och konfigurera en [innehålls nyckel principer](content-key-policy-concept.md). Du skapar en **innehålls nyckel princip** för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till till gångar) levereras till slut klienter.    

      * Definiera licens leverans behörighet, och ange logiken för behörighets kontroll baserat på anspråk i JWT.
      * Konfigurera [PlayReady](playready-license-template-overview.md)-, [Widevine](widevine-license-template-overview.md)-och/eller [Fairplay](fairplay-license-overview.md) -licenser. Med mallarna kan du konfigurera rättigheter och behörigheter för var och en av de använda DRM: er.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Skapa en [strömmande positionerare](streaming-locators-concept.md) som är konfigurerad för att strömma den krypterade till gången. 
  
      Den **strömmande lokaliseraren** måste kopplas till en [strömmande princip](streaming-policy-concept.md). I exemplet ställer vi in StreamingLocator. StreamingPolicyName på principen "Predefined_MultiDrmCencStreaming". PlayReady-och Widevine-krypteringarna används, nyckeln levereras till uppspelnings klienten baserat på de konfigurerade DRM-licenserna. Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”.
      
      Den strömmande lokaliseraren är också kopplad till den **innehålls nyckel princip** som definierats.
    
   3. Skapa en test-token.

      Den **GetTokenAsync** metoden visar hur du skapar ett test-token.
   4. Skapa strömnings-URL.

      Den **GetDASHStreamingUrlAsync** metoden visar hur du skapar strömnings-URL. I det här fallet, URL-strömmar på **DASH** innehåll.

2. Spelare med AES eller DRM-klienten. En videospelare app baserat på en spelare SDK (intern eller webbläsarbaserade) måste uppfylla följande krav:
   * Player SDK stöder de nödvändiga DRM-klienterna
   * Media Player SDK stöder de protokoll som krävs för strömning: Utjämna, streck och/eller HLS
   * Player SDK behöver kunna hantera Skicka en JWT-token i begäran för licens-förvärv
  
     Du kan skapa en spelare med hjälp av den [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd den [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) att ange vilka DRM-teknik för att använda på olika DRM-plattformar.

     För testning AES eller CENC (Widevine och/eller PlayReady) krypterat innehåll, du kan använda [Azure Media Player](https://aka.ms/azuremediaplayer). Kontrollera att du klickar på ”Avancerat” och kontrollera dina alternativ för kryptering.

     Om du vill testa FairPlay krypterat innehåll, använda [det här testet player](https://aka.ms/amtest). Media player stöder Widevine, PlayReady, och FairPlay DRM: er samt AES-128 Rensa nyckelkryptering. 
    
     Du måste välja rätt webbläsare för att testa olika DRM: er: Chrome/Opera/Firefox för Widevine, Microsoft Edge/IE11 för PlayReady, Safari på macOS för FairPlay.

3. Skydda säkerhetstokentjänst (STS), som utfärdar JSON Web Token (JWT) som åtkomsttoken för åtkomst till serverdelen. Du kan använda AMS-licensleveranstjänster som backend-resurs. En STS har du definiera följande:

   * Utfärdare och målgrupp (eller omfång)
   * Anspråk som är beroende av affärskrav i innehållsskydd
   * Symmetriskt eller asymmetriskt verifiering för signaturverifiering
   * Stöd för nyckelförnyelse (vid behov)

     Du kan använda [det här STS-verktyget](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) för att testa STS, som har stöd för alla tre typerna av verifierings nyckel: symmetrisk, asymmetrisk eller Azure AD med nyckel förnyelse. 

> [!NOTE]
> Vi rekommenderar starkt att fokusera och fullständigt Testa varje del (beskrivs ovan) innan du fortsätter till nästa del. Använd de verktyg som anges i listan ovan om du vill testa systemet ”content protection”.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll för liveuppspelning och krypteringstyper

Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med Klartextnyckel eller DRM-kryptering med PlayReady, Widevine och FairPlay. För närvarande kan kryptera du HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Varje protokoll som stöder följande krypteringsmetoder:

### <a name="hls"></a>HLS

HLS-protokollet stöder följande behållar format och krypterings scheman.

|Behållare-format|Krypteringsschemat|URL-exempel|
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inklusive HEVC/H. 265) stöds på följande enheter:

* iOS-V11 eller högre 
* iPhone 8 eller senare
* MacOS, hög Sierra med Intel sjunde gen CPU

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-streck-protokollet stöder följande behållar format och krypterings scheman.

|Behållare-format|Krypteringsschemat|URL-exempel
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming-protokollet stöder följande behållar format och krypterings scheman.

|Protocol|Behållare-format|Krypteringsschemat|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Webbläsare

Vanliga webbläsare stöder följande DRM-klienter:

|Browser|Kryptering|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Åtkomst till innehåll-kontroll

Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera principen för content-nyckel. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera innehåll viktiga principen. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Innehåll viktiga innehållsnyckeln kan ha **öppna** eller **token** begränsning. 

Med en tokenbegränsade innehåll viktiga princip skickas innehållsnyckeln endast till en klient som anger en giltig JSON Web Token (JWT) eller simple webbtoken (SWT) i nyckel/licens-begäran. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda nyckel/licensen till klienten om token är giltig och att anspråken i token matchar de som konfigurerats för nyckel/licens.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Publik, vilket ibland kallas omfattning, beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

Kunderna använder ofta en anpassad STS för att inkludera anpassade anspråk i token för att välja mellan olika ContentKeyPolicyOptions med olika parametrar för DRM-licenser (en prenumerations licens jämfört med en hyres licens) eller för att inkludera ett anspråk som representerar innehålls nyckeln identifierare för den nyckel som token beviljar åtkomst till.

### <a name="token-replay-prevention"></a>Förhindra repetition av token

Funktionen för att *förhindra repetition* av token tillåter Media Services kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Kunden kan lägga till ett anspråk av typen `urn:microsoft:azure:mediaservices:maxuses` i token, där värdet är antalet gånger som token kan användas för att hämta en licens eller nyckel. Alla efterföljande förfrågningar med samma token till nyckel leverans returnerar ett obehörigt svar. Se hur du lägger till anspråk i [DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Överväganden

* Kunder måste ha kontroll över genereringen av token. Anspråket måste placeras i själva token.
* När du använder den här funktionen avvisas begär Anden med token vars utgångs tid är mer än en timme från den tidpunkt då begäran tas emot avvisas med ett obehörigt svar.
* Tokens identifieras unikt av signaturen. Alla ändringar i nytto lasten (till exempel uppdatering till förfallo tiden eller anspråket) ändrar signaturen för token och det räknas som en ny token som nyckel leveransen inte har påträffat tidigare.
* Uppspelningen Miss lyckas om token har `maxuses` överskridit värdet som anges av kunden.
* Den här funktionen kan användas för allt befintligt skyddat innehåll (bara token som utfärdats måste ändras).
* Den här funktionen fungerar med både JWT och SWT.

## <a name="custom-key-and-license-acquisition-url"></a>URL för anpassad nyckel och licens hämtning

Använd följande mallar om du vill ange en annan nyckel och licens leverans tjänst (inte Media Services). Det finns två utbytbara fält i mallarna så att du kan dela din strömmande princip över många till gångar i stället för att skapa en strömmande princip per till gång. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate – mall för URL: en för den anpassade tjänsten som levererar nycklar till slut användar spelarna. Krävs inte när du använder Azure Media Services för att utfärda nycklar. Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med värdet som är kopplat till begäran.  De token-värden som stöds för närvarande är {AlternativeMediaId}, som ersätts med värdet för StreamingLocatorId. AlternativeMediaId och {ContentKeyId}, som ersätts med värdet för identifieraren för den nyckel som begärs.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate – mall för URL: en för den anpassade tjänsten som levererar licenser till slut användar spelarna. Krävs inte när du använder Azure Media Services för utfärdande av licenser. Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med värdet som är kopplat till begäran. De token-värden som stöds för närvarande är {AlternativeMediaId}, som ersätts med värdet för StreamingLocatorId. AlternativeMediaId och {ContentKeyId}, som ersätts med värdet för identifieraren för den nyckel som begärs. 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate – samma som ovan, endast för Widevine. 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate – samma som ovan, endast för FairPlay.  

Exempel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Har ett värde för nyckeln som begärs `AlternativeMediaId` och kan användas om du vill mappa begäran till en entitet på din sida. `ContentKeyId` Till exempel `AlternativeMediaId` kan du använda för att söka efter behörigheter.

För REST-exempel som använder anpassade nyckel-och licens hämtnings-URL: er, se [strömmande principer – skapa](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>Felsöka

Om du får `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` felet, se till att du anger lämplig strömmande princip.

Om du får fel som slutar med `_NOT_SPECIFIED_IN_URL`ser du till att du anger krypterings formatet i URL: en. Till exempel `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Se [strömmande protokoll och krypterings typer](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Skydda med AES-kryptering](protect-with-aes128.md)
* [Skydda med DRM](protect-with-drm.md)
* [Designa multi-DRM innehålls skydds system med åtkomst kontroll](design-multi-drm-system-with-access-control.md)
* [Kryptering på lagrings Sidan](storage-account-concept.md#storage-side-encryption)
* [Vanliga frågor och svar](frequently-asked-questions.md)

