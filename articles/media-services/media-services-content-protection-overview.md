---
title: "Skydda ditt innehåll med Azure Media Services | Microsoft Docs"
description: "Den här artikeln ger en översikt över innehållsskydd med Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Skydda innehåll-översikt
Med Microsoft Azure Media Services kan du skydda dina mediefiler från att filerna lämnar din dator och medan de lagras, bearbetas och levereras. Media Services kan du leverera ditt innehåll för live och på begäran dynamiskt krypterad med Advanced Encryption Standard (AES-128) eller någon av de tre huvudsakliga DRM system: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för innehållsskydd Azure Media Services. 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med AMS. Artikeln innehåller också länkar till artiklar som handlar om hur du skyddar innehåll. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering
Azure Media Services kan du leverera ditt innehåll dynamiskt krypterad med AES Rensa nyckel eller DRM-kryptering: Microsoft PlayReady och Google Widevine Apple FairPlay. För närvarande kan du kryptera följande strömningsformat: HLS MPEG DASH och Smooth Streaming. Kryptering på progressiv hämtning stöds inte. Varje krypteringsmetod stöder följande protokoll för dataströmmar:
- AES: MPEG DASH, Smooth Streaming och HLS
- PlayReady: MPEG DASH, Smooth Streaming och HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

För att kryptera en tillgång, måste du associera en krypteringsnyckel för innehåll med din tillgång och även konfigurera en auktoriseringsprincip för nyckeln. Innehåll nycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera den tillgångsleveransprincip. Om du vill att strömma en krypterad tillgång för lagring, se till att ange hur du vill leverera genom att konfigurera i principen för tillgångsleverans.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt ditt innehåll med hjälp av AES Rensa nyckel eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från AMS viktiga delivery service. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Klartextnyckeln vs DRM
Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den främsta skillnaden mellan att använda AES-kryptering och DRM-system är att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en spårning i nätverket på klienten i klartext. AES-128 klartextnyckeln är lämplig för användningsfall där visningsprogrammet är en betrodd part (t.ex: kryptera företagets videor distribueras inom ett företag för att ses av anställda).

Avmarkera nyckelkryptering PlayReady och Widevine FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln överförs i krypterat format. Dessutom dekryptering är referensen i en säker miljö på nivån operativsystemet där är mycket svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kan vara en betrodd part och du behöver högsta möjliga säkerhetsnivå.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagringskryptering för att kryptera innehållet lokalt med hjälp av AES 256 bitarskryptering och överföra den till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. När du vill skydda filer mediet hög kvalitet med stark kryptering i vila på disk är det primära användningsfallet för kryptering.

För att kunna leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip så Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas strömmande servern dekrypterar och strömmar ditt innehåll med hjälp av angivna leveransprincipen (till exempel AES, vanliga kryptering eller Ingen kryptering).

## <a name="types-of-encryption"></a>Typer av kryptering
PlayReady och Widevine använda Common Encryption (AES CTR läge). FairPlay använder AES CBC-läge kryptering. AES-128 Rensa nyckelkryptering använder kuvert kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar leverans av tjänsten
Media Services tillhandahåller en nyckel leveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay) licenser och AES-nycklar till auktoriserade klienter. Du kan använda [Azure-portalen](media-services-portal-protect-content.md), REST-API eller Media Services SDK för .NET för att konfigurera autentiseringen och auktoriseringen av principer för dina licenser och nycklar.

## <a name="control-content-access"></a>Behörighet för innehåll
Du kan styra vem som har tillgång till ditt innehåll genom att konfigurera principen för auktorisering av innehållsnyckel. Principen för auktorisering av innehållsnyckel stöder öppen eller token begränsning.

### <a name="open-authorization"></a>Öppna auktorisering
Med en öppen auktoriseringsprincip skickas innehållsnyckeln till alla klienter (ingen begränsning).

### <a name="token-authorization"></a>Token auktorisering
Med en begränsad token auktoriseringsprincip ska innehållsnyckeln skicka till en klient som anger ett giltigt JSON-Webbtoken (JWT) eller en enkel Web Token (SWT) i begäran nyckel-licens. Denna token måste vara utfärdat av en säker säkerhetstokentjänst (STS). Du kan använda Microsoft Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returneras begärda nyckel/licensen till klienten om token är giltig och anspråk i token som matchar de som konfigurerats för den nyckel/licensen.

När du konfigurerar token begränsad princip, måste du ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar. Primära Verifieringsnyckeln innehåller den nyckel som token som signerats med, utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen kallas ibland omfång, beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

## <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Om din tillgång har krypterats med flera DRM, bör du använda en kryptering-tagg i strömnings-URL: (format = 'm3u8 aapl', kryptering = ”xxx”).

Följande gäller:
* Kan inte anges mer än en krypteringstyp.
* Krypteringstyp behöver inte anges i URL: en om bara en kryptering har tillämpats på tillgången.
* Krypteringstyp är skiftlägeskänsligt.
* Följande krypteringstyper av kan anges:  
  * **cenc**: för PlayReady eller Widevine (Common Encryption)
  * **cbcs aapl**: för FairPlay (CBC AES-kryptering)
  * **CBC**: för AES envelope kryptering (Envelope kryptering)

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskriver nästa steg för att komma igång med innehållsskydd:
* [Skydda med lagringskryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services PlayReady licens leverans priser förklaras](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Felsöka för AES krypterad dataström i Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT-token autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrera Azure Media Services OWIN MVC-baserade app med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
