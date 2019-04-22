---
title: Skydda ditt innehåll med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över innehållsskydd med Media Services.
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
ms.openlocfilehash: 8259b58c7f30b63084e970bd9aed99642a43226f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805745"
---
# <a name="content-protection-overview"></a>Content protection-översikt 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Du kan använda Azure Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services. Artikeln innehåller också länkar till artiklar om hur du skyddar innehållet. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering
 Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med Klartextnyckel eller DRM-kryptering med PlayReady, Widevine och FairPlay. För närvarande kan kryptera du HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Kryptering på den progressiva nedladdningar stöds inte. Varje krypteringsmetod stöder de följande protokollen med direktuppspelning:

- AES: MPEG-DASH, Smooth Streaming och HLS
- PlayReady: MPEG-DASH, Smooth Streaming och HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

För att kryptera en tillgång, måste du associera en innehållskrypteringsnyckel med din tillgång och även konfigurera en auktoriseringsprincip för nyckeln. Nycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera den tillgångsleveransprincip. Om du vill spela en storage-krypterad tillgång, se till att ange hur du vill leverera genom att konfigurera i principen för tillgångsleverans.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med klartextnyckel för AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services nyckelleveranstjänst. När du beslutar om användaren har behörighet att hämta nyckel måste utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>Rensa viktiga jämfört med AES-128 DRM
Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den viktigaste skillnaden mellan de två systemen är det att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en nätverksspårning på klienten i oformaterad text. Rensa key AES-128-kryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom företaget kan ses av anställda).

Rensa nyckelkryptering PlayReady, Widevine och FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln skickas i ett krypterat format. Dessutom hanteras dekrypteringen i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta säkerhetsnivån.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagringskryptering för att kryptera innehållet lokalt med hjälp av AES-256-bitarskryptering. Du kan sedan överföra den till Azure Storage, var den lagras krypterat i vila. Resurser som skyddas med lagringskryptering okrypterade automatiskt och placeras i ett krypterat filsystem före kodning. Tillgångarna är eventuellt omkrypterade innan de överförs tillbaka som en ny utdatatillgång. I första hand för lagringskryptering är när du vill skydda din högkvalitativa inkommande mediefiler med stark kryptering i vila på disk.

För att leverera en storage-krypterad tillgång, måste du konfigurera den tillgångsleveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan du kan strömma din tillgång, kommer servern för strömning dekrypterar och strömmar ditt innehåll med hjälp av den angivna leveransprincipen (exempelvis AES, gemensam kryptering eller Ingen kryptering).

## <a name="types-of-encryption"></a>Typer av kryptering
PlayReady och Widevine använda gemensam kryptering (AES CTR läge). FairPlay använder kryptering med AES-CBC-läge. Rensa key AES-128-kryptering använder kuvert-kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar för video-on-demand
Media Services tillhandahåller en nyckelleveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay)-licenser och AES-nycklar till auktoriserade klienter. Du kan använda [Azure-portalen](media-services-portal-protect-content.md), REST API eller Media Services SDK för .NET för att konfigurera principer för autentisering och auktorisering för dina licenser och nycklar.

## <a name="control-content-access"></a>Åtkomst till innehåll-kontroll
Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera den auktoriseringsprincipen för innehållsnyckeln. Auktoriseringsprincipen för innehållsnyckeln har stöd för öppen eller token begränsning.

### <a name="open-authorization"></a>Öppna auktorisering
Med en öppen auktoriseringsprincip skickas innehållsnyckeln till alla klienter (ingen begränsning).

### <a name="token-authorization"></a>Token auktorisering
Med en tokenbegränsade auktoriseringsprincip skickas innehållsnyckeln endast till en klient som anger en giltig JSON Web Token (JWT) eller simple webbtoken (SWT) i nyckel/licens-begäran. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda nyckel/licensen till klienten om token är giltig och att anspråken i token matchar de som konfigurerats för nyckel/licens.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Publik, vilket ibland kallas omfattning, beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

## <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Om din tillgång har krypterats med mer än en DRM måste använda en tagg för kryptering i strömnings-URL: (format = 'm3u8-aapl' kryptering = ”xxx”).

Följande gäller:

* Du kan ange en krypteringstyp.
* Krypteringstyp behöver inte anges i URL: en om det bara en kryptering har tillämpats på tillgången.
* Krypteringstyp är skiftlägeskänsligt.
* Du kan ange följande krypteringstyper av:
  * **cenc**: För PlayReady eller Widevine (common encryption)
  * **cbcs-aapl**: För FairPlay (AES-CBC kryptering)
  * **cbc**: För kuvert AES-kryptering

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskriver nästa steg för att hjälpa dig att komma igång med content protection:

* [Skydda med lagringskryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Relaterade länkar

* [Autentisering med JWT-token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrera Azure Media Services OWIN MVC-baserade appar med Azure Active Directory och begränsa viktiga innehållsleverans utifrån JWT-anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
