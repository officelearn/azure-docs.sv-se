---
title: Skydda ditt innehåll med dynamisk kryptering av Media Services v3
titleSuffix: Azure Media Services
description: Lär dig mer om innehållsskydd med dynamisk kryptering, direktuppspelningsprotokoll och krypteringstyper i Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461120"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Skydda ditt innehåll med dynamisk kryptering av Media Services

Använd Azure Media Services för att skydda dina media från det att datorn lämnas hela vägen genom lagring, bearbetning och leverans. Med Media Services kan du leverera ditt live- och on-demand-innehåll krypterat dynamiskt med Advanced Encryption Standard (AES-128) eller något av de tre stora DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. Om innehåll krypteras med en AES-klarnyckel och skickas via HTTPS är det inte klart förrän det når klienten. 

I Media Services v3 associeras en innehållsnyckel med streamingpositionerare (se [det här exemplet).](protect-with-aes128.md) Om du använder tjänsten för nyckelleverans för Media Services kan du låta Azure Media Services generera innehållsnyckeln åt dig. Innehållsnyckeln ska genereras själv om du använder din egen nyckelleveranstjänst, eller om du behöver hantera ett scenario med hög tillgänglighet där du måste ha samma innehållsnyckel i två datacenter.

När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES-clear key eller DRM-kryptering. För att dekryptera strömmen begär spelaren nyckeln från Media Services nyckelleveranstjänst eller den nyckelleveranstjänst du angav. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten den innehållsnyckelprincip som du angav för nyckeln.

Du kan använda REST API eller ett Media Services-klientbibliotek för att konfigurera auktoriserings- och autentiseringsprinciper för dina licenser och nycklar.

Följande bild illustrerar arbetsflödet för innehållsskydd för Media Services:

![Innehållsskydd för Arbetsflöde för Media Services](./media/content-protection/content-protection.svg)
  
&#42; Dynamisk *kryptering stöder AES-128 clear key, CBCS och CENC. Mer information finns i [supportmatrisen](#streaming-protocols-and-encryption-types).*

I den här artikeln beskrivs begrepp och terminologi som hjälper dig att förstå innehållsskydd med Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Huvudkomponenter i ett innehållsskyddssystem

För att kunna slutföra ditt innehållsskyddssystem måste du till fullo förstå omfattningen av ansträngningen. I följande avsnitt ges en översikt över tre delar som du behöver implementera.

> [!NOTE]
> Vi rekommenderar starkt att du fokuserar och helt testa varje del i följande avsnitt innan du går vidare till nästa del. Om du vill testa innehållsskyddssystemet använder du de verktyg som anges i avsnitten.

### <a name="media-services-code"></a>Kod för mediatjänster
  
[DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) visar hur du implementerar ett MULTI-DRM-system med Media Services v3 med hjälp av .NET. Den visar också hur du använder Media Services-licens-/nyckelleveranstjänsten.
  
Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). Mer om du vill se vad som är meningsfullt att kombinera finns [i Direktuppspelningsprotokoll och krypteringstyper](#streaming-protocols-and-encryption-types).

Exemplet visar hur du:

1. Skapa och konfigurera en [princip för innehållsnyckel](content-key-policy-concept.md).

   Du skapar en innehållsnyckelprincip för att konfigurera hur innehållsnyckeln (som ger säker åtkomst till dina tillgångar) levereras till slutklienter:  

   * Definiera licensleveransauktorisering. Ange logiken för auktoriseringskontrollen baserat på anspråk i JSON Web Token (JWT).
   * Konfigurera [PlayReady-,](playready-license-template-overview.md) [Widevine-](widevine-license-template-overview.md)och/eller [FairPlay-licenser.](fairplay-license-overview.md) Med mallarna kan du konfigurera rättigheter och behörigheter för var och en av DRM:erna.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Skapa en [streaming locator](streaming-locators-concept.md) som är konfigurerad för att strömma den krypterade tillgången.
  
   Streaming locator måste associeras med en [streamingprincip](streaming-policy-concept.md). I exemplet ställer `StreamingLocator.StreamingPolicyName` vi in på principen "Predefined_MultiDrmCencStreaming".

   PlayReady- och Widevine-krypteringarna tillämpas och nyckeln levereras till uppspelningsklienten baserat på de konfigurerade DRM-licenserna. Om du också vill kryptera din stream med CBCS (FairPlay) använder du policyn "Predefined_MultiDrmStreaming".

   Direktuppspelningspositioneraren är också associerad med den innehållsnyckelprincip som du har definierat.

3. Skapa en testtoken.

   Metoden `GetTokenAsync` visar hur du skapar en testtoken.
4. Skapa webbadressen för direktuppspelning.

   Metoden `GetDASHStreamingUrlAsync` visar hur du skapar webbadressen för direktuppspelning. I det här fallet strömmar URL:en DASH-innehåll.

### <a name="player-with-an-aes-or-drm-client"></a>Spelare med en AES- eller DRM-klient

En videospelarapp baserad på en spelare SDK (antingen inbyggt eller webbläsarbaserat) måste uppfylla följande krav:

* Spelaren SDK stöder de nödvändiga DRM-klienterna.
* Spelaren SDK stöder de strömmande protokoll som krävs: Smooth, DASH och/eller HTTP Live Streaming (HLS).
* Spelaren SDK kan hantera passerar en JWT token i en begäran om licensförvärv.

Du kan skapa en spelare med hjälp av [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) för att ange vilken DRM-teknik som ska användas på olika DRM-plattformar.

Om du testar AES- eller CENC-krypterat innehåll (Widevine och/eller PlayReady) kan du använda [Azure Media Player](https://aka.ms/azuremediaplayer). Se till att du väljer **Avancerade alternativ** och kontrollera krypteringsalternativen.

Om du vill testa FairPlay krypterat innehåll använder du [den här testspelaren](https://aka.ms/amtest). Spelaren stöder Widevine, PlayReady och FairPlay DRMs, tillsammans med AES-128 tydlig nyckelkryptering.

Välj rätt webbläsare för att testa olika DRM:er:

* Chrome, Opera eller Firefox för Widevine.
* Microsoft Edge eller Internet Explorer 11 för PlayReady.
* Safari på macOS för FairPlay.

### <a name="security-token-service"></a>Tjänst för säkerhetstoken

En STS-tjänst (Security Token Service) utfärdar JWT som åtkomsttoken för resursåtkomst för backend.A security token service (STS) issues JWT as the access token for back-end resource access. Du kan använda Azure Media Services-licens-/nyckelleveranstjänsten som backend-resurs. En STS måste definiera följande saker:

* Emittent och publik (eller omfattning).
* Anspråk, som är beroende av affärskrav i innehållsskydd.
* Symmetrisk eller asymmetrisk verifiering för signaturverifiering.
* Stöd för nyckelvälpning (om det behövs).

Du kan använda [detta STS-verktyg](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) för att testa STS. Den stöder alla tre typer av verifieringsnycklar: symmetrisk, asymmetrisk eller Azure Active Directory (Azure AD) med nyckelöverrullning.

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll och krypteringstyper för direktuppspelning

Du kan använda Media Services för att leverera ditt innehåll krypterat dynamiskt med AES-clear key eller DRM-kryptering med PlayReady, Widevine eller FairPlay. För närvarande kan du kryptera formaten HLS, MPEG DASH och Smooth Streaming. Varje protokoll stöder följande krypteringsmetoder.

### <a name="hls"></a>HLS

HLS-protokollet stöder följande behållarformat och krypteringsscheman:

|Behållare format|Krypteringsschema|EXEMPEL PÅ URL|
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inklusive HEVC/H.265) stöds på följande enheter:

* iOS 11 eller senare.
* iPhone 8 eller senare.
* MacOS High Sierra med Intels 7:e generationens CPU.

### <a name="mpeg-dash"></a>MPEG-STRECK

MPEG-DASH-protokollet stöder följande behållarformat och krypteringsscheman:

|Behållare format|Krypteringsschema|EXEMPEL PÅ WEBBADRESS
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming-protokollet stöder följande behållarformat och krypteringsscheman.

|Protokoll|Behållare format|Krypteringsschema|
|---|---|---|
|fMP4 (på andra sätt)|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 (på andra sätt) | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Webbläsare

Vanliga webbläsare stöder följande DRM-klienter:

|Webbläsare|Kryptering|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Styra åtkomst till innehåll

Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera innehållsnyckelprincipen. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Innehållsnyckelprincipen kan ha *öppen* eller *tokenbegränsning.*

En princip med öppen begränsad innehållsnyckel kan användas när du vill utfärda licens till vem som helst utan tillstånd. Om dina intäkter till exempel är annonsbaserade och inte prenumerationsbaserade.  

Med en tokenbegränsad innehållsnyckelprincip skickas innehållsnyckeln endast till en klient som presenterar en giltig JWT-token eller en enkel webbtoken (SWT) i licens-/nyckelbegäran. Denna token måste utfärdas av en STS.

Du kan använda Azure AD som sts eller distribuera en [anpassad STS](#using-a-custom-sts). STS måste konfigureras för att skapa en token som har signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen. Media Services-licens-/nyckelleveranstjänsten returnerar den begärda licensen eller nyckeln till klienten om båda dessa villkor finns:

* Token är giltig.
* Anspråken i token matchar de som konfigurerats för licensen eller nyckeln.

När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med. Utfärdaren är STS som utfärdar token. Målgruppen, som ibland kallas scope, beskriver avsikten med token eller resursen som token auktoriserar åtkomst till. Media Services-licens-/nyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

### <a name="token-replay-prevention"></a>Förebyggande av tokenrepris

Funktionen *Token Replay Prevention* gör det möjligt för Media Services-kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Kunden kan lägga till `urn:microsoft:azure:mediaservices:maxuses` ett anspråk av typen i token, där värdet är antalet gånger token kan användas för att förvärva en licens eller nyckel. Alla efterföljande begäranden med samma token till nyckelleverans returnerar ett obehörigt svar. Se hur du lägger till anspråket i [DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Överväganden

* Kunder måste ha kontroll över tokengenerering. Anspråket måste placeras i själva token.
* När du använder den här funktionen avvisas begäranden med token vars utgångstid är mer än en timme från den tidpunkt då begäran tas emot med ett obehörigt svar.
* Tokens identifieras unikt av deras signatur. Alla ändringar av nyttolasten (till exempel uppdatering av utgångstiden eller anspråket) ändrar signaturen för token och det räknas som en ny token som nyckelleverans inte har stött på tidigare.
* Uppspelningen misslyckas om token `maxuses` har överskridit det värde som angetts av kunden.
* Den här funktionen kan användas för allt befintligt skyddat innehåll (endast den utfärdade token behöver ändras).
* Den här funktionen fungerar med både JWT och SWT.

## <a name="using-a-custom-sts"></a>Använda en anpassad STS

En kund kan välja att använda en anpassad STS för att tillhandahålla token. Orsaker är:

* Identitetsprovidern (IDP) som används av kunden stöder inte STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöva mer flexibel eller hårdare kontroll för att integrera STS med kundens prenumerant faktureringssystem.

   En [OTT-tjänstoperatör](https://en.wikipedia.org/wiki/Over-the-top_media_services) kan till exempel erbjuda flera prenumerationspaket, till exempel premium, grundläggande och sport. Operatören kanske vill matcha anspråken i en token med en prenumerant paket så att endast innehållet i ett visst paket görs tillgängligt. I det här fallet ger en anpassad STS den flexibilitet och kontroll som behövs.

* Om du vill inkludera anpassade anspråk i token för att välja mellan olika ContentKeyPolicyOptions med olika DRM-licensparametrar (en prenumerationslicens jämfört med en hyrlicens).
* Om du vill inkludera ett anspråk som representerar innehållsnyckelidentifieraren för nyckeln som token ger åtkomst till.

När du använder en anpassad STS måste två ändringar göras:

* När du konfigurerar licensleveranstjänst för en tillgång måste du ange säkerhetsnyckeln som används för verifiering av den anpassade STS i stället för den aktuella nyckeln från Azure AD.
* När en JTW-token genereras anges en säkerhetsnyckel i stället för den privata nyckeln för det aktuella X509-certifikatet i Azure AD.

Det finns två typer av säkerhetsnycklar:

* Symmetrisk nyckel: Samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: Ett offentligt-privat nyckelpar i ett X509-certifikat används med en privat nyckel för att kryptera/generera en JWT och med den offentliga nyckeln för att verifiera token.

Om du använder .NET Framework/C# som utvecklingsplattform måste X509-certifikatet som används för en asymmetrisk säkerhetsnyckel ha en nyckellängd på minst 2048. Den här nyckellängden är ett krav för klassen System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework. Annars genereras följande undantag: IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey' för signering kan inte vara mindre än 2048-bitar.

## <a name="custom-key-and-license-acquisition-url"></a>Url för anpassad nyckel- och licensanskaffning

Använd följande mallar om du vill ange en annan licens-/nyckelleveranstjänst (inte Media Services). De två utbytbara fälten i mallarna finns där så att du kan dela din streamingprincip över många tillgångar i stället för att skapa en direktuppspelningsprincip per tillgång. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Mall för webbadressen till den anpassade tjänsten som levererar nycklar till slutanvändare. Det krävs inte när du använder Azure Media Services för att utfärda nycklar. 

   Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med det värde som är specifikt för begäran.  Tokenvärdena som stöds stöds är:
   * `{AlternativeMediaId}`, som ersätts med värdet av StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, som ersätts med värdet för identifieraren för den begärda nyckeln.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Mall för url:en för den anpassade tjänsten som levererar licenser till slutanvändare. Det krävs inte när du använder Azure Media Services för att utfärda licenser.

   Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med det värde som är specifikt för begäran. Tokenvärdena som stöds stöds är:  
   * `{AlternativeMediaId}`, som ersätts med värdet av StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, som ersätts med värdet för identifieraren för den begärda nyckeln. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Samma som den tidigare mallen, bara för Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Samma som den tidigare mallen, endast för FairPlay.  

Ett exempel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`har värdet på den begärda nyckeln. Du kan `AlternativeMediaId` använda om du vill mappa begäran till en entitet på din sida. Kan till `AlternativeMediaId` exempel användas för att hjälpa dig att slå upp behörigheter.

För REST-exempel som använder anpassade url:er för licens-/nyckelförvärv finns i [Streamingprinciper - Skapa](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="troubleshoot"></a>Felsöka

Om du `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` får felet kontrollerar du att du anger rätt direktuppspelningsprincip.

Om du får fel `_NOT_SPECIFIED_IN_URL`som slutar med kontrollerar du att du anger krypteringsformatet i URL:en. Ett exempel är `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Se [Strömmande protokoll och krypteringstyper](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

* [Skydda med AES-kryptering](protect-with-aes128.md)
* [Skydda med DRM](protect-with-drm.md)
* [Designa innehållsskyddssystem med flera DRM-innehåll med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
* [Kryptering på lagringssidan](storage-account-concept.md#storage-side-encryption)
* [Vanliga frågor och svar](frequently-asked-questions.md)
* [JSON-webbtokenhanterare](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
