---
title: Skydda ditt innehåll med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över innehålls skydd med Azure Media Services v2.
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
ms.openlocfilehash: b0d71a7b010e91776a28330cfc32278c7060aab6
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901255"
---
# <a name="content-protection-overview"></a>Content protection-översikt 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Du kan använda Azure Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med medietjänster kan du leverera live och på begäran innehåll dynamiskt krypterad med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services. Artikeln innehåller också länkar till artiklar som diskuterar hur du skyddar innehåll. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering
 Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med Klartextnyckel eller DRM-kryptering med PlayReady, Widevine och FairPlay. För närvarande kan kryptera du HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Kryptering av Progressive nedladdning stöds inte. Varje krypterings metod stöder följande strömnings protokoll:

- AES: MPEG-streck, Smooth Streaming och HLS
- PlayReady: MPEG-streck, Smooth Streaming och HLS
- Widevine: MPEG-streck
- FairPlay: HLS

Om du vill kryptera en till gång måste du associera en krypterings innehålls nyckel med din till gång och även konfigurera en auktoriseringsprincip för nyckeln. Innehålls nycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera till gångens leverans princip. Om du vill strömma en lagrings krypterad till gång ska du se till att ange hur du vill leverera den genom att konfigurera till gångs leverans principen.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med klartextnyckel för AES eller DRM-kryptering. För att dekryptera data strömmen begär spelaren nyckeln från Media Services Key Delivery Service. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de Auktoriseringsprinciper som du har angett för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-rensad nyckel eller DRM
Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den viktigaste skillnaden mellan de två systemen är det att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en nätverksspårning på klienten i oformaterad text. Rensa key AES-128-kryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom företaget kan ses av anställda).

Rensa nyckelkryptering PlayReady, Widevine och FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln skickas i ett krypterat format. Dessutom hanteras dekrypteringen i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta säkerhetsnivån.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagrings kryptering för att kryptera ditt rensade innehåll lokalt med hjälp av AES 256-bitars kryptering. Du kan sedan överföra den till Azure Storage, där den är lagrad som krypterad i vila. Till gångar som skyddas med lagrings kryptering krypteras automatiskt och placeras i ett krypterat fil system före kodning. Till gångarna krypteras om innan du laddar upp igen som en ny utgående till gång. Det primära användnings fallet för lagrings kryptering är när du vill skydda dina mediefiler med hög kvalitet med stark kryptering i vila på disk.

För att leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip så att Media Services vet hur du vill leverera ditt innehåll. Innan din till gång kan strömmas avkrypterar och strömmar ditt innehåll med hjälp av den angivna leverans principen (till exempel AES, gemensam kryptering eller ingen kryptering).

## <a name="types-of-encryption"></a>Krypterings typer
PlayReady och Widevine använder gemensam kryptering (AES-läge). FairPlay använder sig av AES-CBC kryptering. Med AES-128 rensad nyckel kryptering används kuvert kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar för video-on-demand
Media Services tillhandahåller en nyckelleveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay)-licenser och AES-nycklar till auktoriserade klienter. Du kan använda [Azure Portal](media-services-portal-protect-content.md), REST API eller Media Services SDK för .net för att konfigurera principer för auktorisering och autentisering för dina licenser och nycklar.

## <a name="control-content-access"></a>Åtkomst till innehåll-kontroll
Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera en auktoriseringsprincip för innehålls nyckeln. Auktoriseringsprincipen för innehålls nycklar stöder antingen öppen eller token-begränsning.

### <a name="open-authorization"></a>Öppen auktorisering
Med en öppen auktoriseringsprincip skickas innehålls nyckeln till alla klienter (ingen begränsning).

### <a name="token-authorization"></a>Token-auktorisering
Med en princip för begränsad auktorisering skickas innehålls nyckeln endast till en klient som presenterar en giltig JSON Web Token (JWT) eller enkel webbtoken (SWT) i begäran om nyckel/licens. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda nyckel/licensen till klienten om token är giltig och att anspråken i token matchar de som konfigurerats för nyckel/licens.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Publik, vilket ibland kallas omfattning, beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

## <a name="streaming-urls"></a>Strömmande URL: er
Om din till gång har krypterats med fler än ett DRM, använder du en krypterings tagg i direkt uppspelnings-URL: (format = ' 3u8-AAPL ', Encryption = ' xxx ').

Följande gäller:

* Det går inte att ange fler än en krypterings typ.
* Krypterings typen behöver inte anges i URL: en om bara en kryptering tillämpades på till gången.
* Krypterings typen är Skift läges okänslig.
* Följande krypterings typer kan anges:
  * **Cenc**: för PlayReady eller Widevine (common Encryption)
  * **CBCS – AAPL**: för FAIRPLAY (AES CBC-kryptering)
  * **CBC**: för kryptering med AES-kuvert

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskrivs nästa steg som hjälper dig att komma igång med innehålls skydd:

* [Skydda med lagrings kryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Tillhörande länkar

* [JWT-token-autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrera Azure Media Services OWIN MVC-baserade appar med Azure Active Directory och begränsa leverans av innehålls nycklar baserat på JWT-anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
