---
title: Skydda ditt innehåll med Azure Media Services | Microsoft-dokument
description: Den här artikeln innehåller en översikt över innehållsskydd med Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460967"
---
# <a name="content-protection-overview"></a>Översikt över innehållsskydd 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Du kan använda Azure Media Services för att skydda dina media från det att datorn lämnas via lagring, bearbetning och leverans. Med Media Services kan du leverera ditt live- och on-demand-innehåll krypterat dynamiskt med Advanced Encryption Standard (AES-128) eller något av de tre stora DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för innehållsskydd för Media Services: 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

I den här artikeln beskrivs begrepp och terminologi som är relevanta för att förstå innehållsskydd med Medietjänster. Artikeln innehåller också länkar till artiklar som diskuterar hur du skyddar innehåll. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Du kan använda Media Services för att leverera ditt innehåll krypterat dynamiskt med AES-clear key eller DRM-kryptering med PlayReady, Widevine eller FairPlay. Om innehåll krypteras med en AES-klarnyckel och skickas via HTTPS är det inte klart förrän det når klienten. 

Varje krypteringsmetod stöder följande direktuppspelningsprotokoll:
 
- AES: MPEG-DASH, Smooth Streaming och HLS
- PlayReady: MPEG-DASH, Smooth Streaming och HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Kryptering vid progressiva nedladdningar stöds inte. 

Om du vill kryptera en tillgång måste du associera en krypteringsinnehållsnyckel med din tillgång och även konfigurera en auktoriseringsprincip för nyckeln. Innehållsnycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera tillgångens leveransprincip. Om du vill strömma en lagringskrypterad tillgång kontrollerar du hur du vill leverera den genom att konfigurera leveransprincipen för tillgångar.

När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES-clear key eller DRM-kryptering. För att dekryptera strömmen begär spelaren nyckeln från Media Services nyckelleveranstjänst. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de auktoriseringsprinciper som du har angett för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 klar nyckel kontra DRM
Kunderna undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den primära skillnaden mellan de två systemen är att med AES-kryptering överförs innehållsnyckeln till klienten i ett okrypterat format ("i det tydliga"). Därför kan nyckeln som används för att kryptera innehållet visas i en nätverksspårning på klienten i oformaterad text. AES-128 tydlig nyckel kryptering är lämplig för användningsfall där betraktaren är en betrodd part (till exempel kryptera företagsvideor distribueras inom ett företag som ska ses av anställda).

PlayReady, Widevine och FairPlay ger alla en högre krypteringsnivå jämfört med AES-128-kryptering med tydlig nyckel. Innehållsnyckeln överförs i ett krypterat format. Dessutom hanteras dekryptering i en säker miljö på operativsystemsnivå, där det är svårare för en obehörig användare att attackera. DRM rekommenderas för användningsfall där visningsprogrammet kanske inte är en betrodd part och du behöver den högsta säkerhetsnivån.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagringskryptering för att kryptera ditt rensa innehåll lokalt med hjälp av AES 256-bitars kryptering. Du kan sedan ladda upp den till Azure Storage, där den lagras krypterad i vila. Tillgångar som skyddas med lagringskryptering okrypteras automatiskt och placeras i ett krypterat filsystem före kodning. Tillgångarna krypteras eventuellt om innan de överförs tillbaka som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina högkvalitativa indatamediefiler med stark kryptering i vila på disken.

Om du vill leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas dekrypterar och strömmar direktuppspelningsservern ditt innehåll med hjälp av den angivna leveransprincipen (till exempel AES, vanlig kryptering eller ingen kryptering).

## <a name="types-of-encryption"></a>Typer av kryptering
PlayReady och Widevine använder vanlig kryptering (AES CTR-läge). FairPlay använder AES CBC-läge kryptering. AES-128 clear key kryptering använder kuvert kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Leverans av licenser och nycklar
Media Services tillhandahåller en viktig leveranstjänst för leverans av DRM-licenser (PlayReady, Widevine, FairPlay) och AES-nycklar till auktoriserade klienter. Du kan använda [Azure-portalen,](media-services-portal-protect-content.md)REST API eller Media Services SDK för .NET för att konfigurera auktoriserings- och autentiseringsprinciper för dina licenser och nycklar.

## <a name="control-content-access"></a>Kontrollera åtkomst till innehåll
Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera behörighetsprincipen för innehållsnyckeln. Behörighetsprincipen för innehållsnyckel stöder antingen öppen eller tokenbegränsning.

### <a name="open-authorization"></a>Öppna auktorisering
Med en öppen auktoriseringsprincip skickas innehållsnyckeln till valfri klient (ingen begränsning).

### <a name="token-authorization"></a>Tokenauktorisering
Med en tokenbegränsad auktoriseringsprincip skickas innehållsnyckeln endast till en klient som presenterar en giltig JSON Web Token (JWT) eller enkel webbtoken (SWT) i nyckel-/licensbegäran. Den här token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som har signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen. Media Services-nyckelleveranstjänsten returnerar den begärda nyckeln/licensen till klienten om token är giltig och anspråken i token matchar de som konfigurerats för nyckeln/licensen.

När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med. Utfärdaren är den säkra tokentjänsten som utfärdar token. Målgruppen, som ibland kallas scope, beskriver avsikten med token eller resursen som token auktoriserar åtkomst till. Medietjänstnyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

### <a name="token-replay-prevention"></a>Förebyggande av tokenrepris

Funktionen *Token Replay Prevention* gör det möjligt för Media Services-kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Kunden kan lägga till `urn:microsoft:azure:mediaservices:maxuses` ett anspråk av typen i token, där värdet är antalet gånger token kan användas för att förvärva en licens eller nyckel. Alla efterföljande begäranden med samma token till nyckelleverans returnerar ett obehörigt svar. Se hur du lägger till anspråket i [DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Överväganden

* Kunder måste ha kontroll över tokengenerering. Anspråket måste placeras i själva token.
* När du använder den här funktionen avvisas begäranden med token vars utgångstid är mer än en timme från den tidpunkt då begäran tas emot med ett obehörigt svar.
* Tokens identifieras unikt av deras signatur. Alla ändringar av nyttolasten (till exempel uppdatering av utgångstiden eller anspråket) ändrar signaturen för token och det räknas som en ny token som nyckelleverans inte har stött på tidigare.
* Uppspelningen misslyckas om token `maxuses` har överskridit det värde som angetts av kunden.
* Den här funktionen kan användas för allt befintligt skyddat innehåll (endast den utfärdade token behöver ändras).
* Den här funktionen fungerar med både JWT och SWT.

## <a name="streaming-urls"></a>Url:er för direktuppspelning
Om din tillgång krypterades med mer än en DRM använder du en krypteringstagg i direktuppspelnings-URL:en: (format='m3u8-aapl', encryption='xxx').

Följande gäller:

* Det går inte att ange mer än en krypteringstyp.
* Krypteringstyp behöver inte anges i URL:en om bara en kryptering har tillämpats på tillgången.
* Krypteringstypen är skiftlägesokänslig.
* Följande krypteringstyper kan anges:

  * **cenc**: För PlayReady eller Widevine (vanlig kryptering)
  * **cbcs-aapl**: För FairPlay (AES CBC-kryptering)
  * **cbc**: För AES-kuvertkryptering

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskrivs nästa steg som hjälper dig att komma igång med innehållsskydd:

* [Skydda med lagringskryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Relaterade länkar

* [JWT-tokenautentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrera Azure Media Services OWIN MVC-baserad app med Azure Active Directory och begränsa leverans av innehållsnyckel baserat på JWT-anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
