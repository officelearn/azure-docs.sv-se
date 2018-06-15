---
title: Skydda ditt innehåll med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över innehållsskydd med Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788741"
---
# <a name="content-protection-overview"></a>Översikt över innehållsskydd
 Du kan använda Azure Media Services för att skydda mediet från den tidpunkt som den lämnar din dator via lagring, bearbetning och leverans. Du kan leverera live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre huvudsakliga digital rights management (DRM) system med Media Services: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för innehållsskydd Media Services: 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services. Artikeln innehåller också länkar till artiklar som handlar om hur du skyddar innehåll. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering
 Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med AES Rensa nyckel eller DRM-kryptering med PlayReady eller Widevine FairPlay. För närvarande kan du kryptera HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Kryptering på progressiv hämtning stöds inte. Varje krypteringsmetod stöder följande protokoll för dataströmmar:

- AES: MPEG DASH, Smooth Streaming och HLS
- PlayReady: MPEG DASH, Smooth Streaming och HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

För att kryptera en tillgång, måste du associera en krypteringsnyckel för innehåll med din tillgång och även konfigurera en auktoriseringsprincip för nyckeln. Innehåll nycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera den tillgångsleveransprincip. Om du vill att strömma en lagring krypteras tillgång, se till att ange hur du vill leverera genom att konfigurera i principen för tillgångsleverans.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med hjälp av AES klartextnyckeln eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services viktiga delivery service. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>Ta bort nyckeln jämfört med AES-128. DRM
Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den främsta skillnaden mellan de två systemen är att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en spårning i nätverket på klienten i oformaterad text. AES-128 Rensa nyckelkryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom ett företag för att ses av anställda).

Avmarkera nyckelkryptering PlayReady och Widevine FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln överförs i krypterat format. Dessutom hanteras dekryptering i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta möjliga säkerhetsnivå.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagringskryptering för att kryptera innehållet lokalt med hjälp av AES 256-bitars kryptering. Du kan skicka det sedan till Azure Storage, där den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering okrypterad och placeras i ett krypterat filsystem före kodning automatiskt. Tillgångar är eventuellt omkrypterade innan de överförs tillbaka som en ny utdatatillgång. När du vill skydda filer mediet hög kvalitet med stark kryptering i vila på disk är det primära användningsfallet för kryptering.

För att leverera en lagring krypteras tillgång måste du konfigurera den tillgångsleveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas strömmande servern dekrypterar och strömmar ditt innehåll med hjälp av angivna leveransprincipen (till exempel AES, vanliga kryptering eller Ingen kryptering).

## <a name="types-of-encryption"></a>Typer av kryptering
PlayReady och Widevine använda vanliga kryptering (AES CTR läge). FairPlay använder CBC-läge för AES-kryptering. AES-128 Rensa nyckelkryptering använder kuvert kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar leverans av tjänsten
Media Services tillhandahåller en nyckel leveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay) licenser och AES-nycklar till auktoriserade klienter. Du kan använda [Azure-portalen](media-services-portal-protect-content.md), REST-API eller Media Services SDK för .NET för att konfigurera autentiseringen och auktoriseringen av principer för dina licenser och nycklar.

## <a name="control-content-access"></a>Behörighet för innehåll
Du kan styra vem som har tillgång till ditt innehåll genom att konfigurera principen för auktorisering av innehållsnyckel. Principen för auktorisering av innehållsnyckel stöder öppen eller token begränsning.

### <a name="open-authorization"></a>Öppna auktorisering
Med en öppen auktoriseringsprincip skickas innehållsnyckeln till alla klienter (ingen begränsning).

### <a name="token-authorization"></a>Token auktorisering
Med en begränsad token auktoriseringsprincip skickas innehållsnyckeln endast till en klient som anger en giltig JSON-Webbtoken (JWT) eller simple web token (SWT) i begäran nyckel-licens. Denna token måste vara utfärdat av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returnerar begärda nyckel/licensen till klienten om token är giltig och anspråk i token som matchar de som konfigurerats för den nyckel/licensen.

När du konfigurerar den tokenbegränsade principen måste du ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar. Primär Verifieringsnyckeln innehåller den nyckel som token som signerats med. Utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen kallas ibland omfång, beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

## <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Om din tillgång har krypterats med flera DRM använder kryptering taggen i strömnings-URL: (format = 'm3u8 aapl', kryptering = ”xxx”).

Följande gäller:

* Kan inte anges mer än en krypteringstyp.
* Krypteringstyp behöver inte anges i URL: en om bara en kryptering har tillämpats på tillgången.
* Krypteringstyp är skiftlägeskänsligt.
* Följande krypteringstyper av kan anges:
  * **cenc**: för PlayReady eller Widevine (common encryption)
  * **cbcs aapl**: för FairPlay (CBC AES-kryptering)
  * **CBC**: för AES envelope kryptering

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskriver nästa steg för att hjälpa dig komma igång med innehållsskydd:

* [Skydda med lagringskryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Relaterade länkar

* [Azure Media Services PlayReady licens leverans priser förklaras](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [Felsöka för AES krypterad dataström i Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [JWT-token autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrera Azure Media Services OWIN MVC-baserade app med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
