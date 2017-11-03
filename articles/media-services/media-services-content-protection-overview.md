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
ms.openlocfilehash: 64be4ea104bd11b8e191e2c8d4170a2de88acb47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-content-overview"></a>Skydda innehåll-översikt
Med Microsoft Azure Media Services kan du skydda dina mediefiler från att filerna lämnar din dator och medan de lagras, bearbetas och levereras. Media Services kan du leverera ditt innehåll för live och på begäran dynamiskt krypterad med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar) eller någon av de huvudsakliga DRMs: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild visar innehållsskydd arbetsflöden som har stöd för AMS. 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Det här avsnittet beskriver [begrepp och termer](media-services-content-protection-overview.md) relevanta för att förstå innehållsskydd med AMS. Avsnittet innehåller också [länkar](media-services-content-protection-overview.md#common-scenarios) till avsnitt som visar hur du uppnår innehållsskydd uppgifter. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering
Microsoft Azure Media Services kan du leverera ditt innehåll dynamiskt krypterad med AES Rensa nyckel eller DRM-kryptering: Microsoft PlayReady och Google Widevine Apple FairPlay.

För närvarande kan du kryptera följande strömningsformat: HLS MPEG DASH och Smooth Streaming. Det går inte att kryptera progressiv hämtning.

Om du vill använda för Media Services att kryptera en tillgång, måste du associera en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med din tillgång och även konfigurera auktoriseringsprinciper för nyckeln.

Du måste också konfigurera den tillgångsleveransprincip. Om du vill strömma en krypterad tillgång för lagring, se till att ange hur du vill leverera genom att konfigurera principen för tillgångsleverans.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt ditt innehåll med hjälp av AES Rensa nyckel eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.


## <a name="storage-encryption"></a>Lagringskryptering
Använd kryptering för att kryptera innehållet lokalt med hjälp av AES 256-bitarskryptering och överföra den till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina hög kvalitet inkommande mediefiler med stark kryptering i vila på disk.

För att kunna leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip så Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas strömmande server tar du bort krypteringen lagring och strömmar ditt innehåll med hjälp av angivna leveransprincipen (till exempel AES, vanliga kryptering eller Ingen kryptering).

## <a name="common-encryption-cenc"></a>Vanliga kryptering (CENC)
Vanliga kryptering används när du krypterar ditt innehåll med PlayReady eller / och Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Med cbcs aapl kryptering
Cbcs aapl används för att kryptera innehållet med FairPlay.

## <a name="envelope-encryption"></a>Kuvert kryptering
Använd det här alternativet om du vill skydda ditt innehåll med AES-128 klartextnyckel. Om du vill att ett säkrare alternativ kan du välja en av DRMs som anges i det här avsnittet. 

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar leverans av tjänsten
Media Services tillhandahåller en tjänst för att leverera DRM (PlayReady Widevine, FairPlay) licenser och AES avmarkera nycklar till auktoriserade klienter. Du kan använda [Azure-portalen](media-services-portal-protect-content.md), REST-API eller Media Services SDK för .NET för att konfigurera autentiseringen och auktoriseringen av principer för dina licenser och nycklar.

## <a name="token-restriction"></a>Tokenbegränsningar
Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formatet Simple Web Tokens (SWT) och JSON-Webbtoken (JWT)-format. Media Services tillhandahåller inte Secure Token tjänster. Du kan skapa en anpassad STS eller använda Microsoft Azure ACS problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returneras den begärda (eller licensinformation) till klienten om token är giltig och anspråk i token matchar de som konfigurerats för nyckeln (eller licens).

När du konfigurerar token begränsad princip, måste du ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar. Primära Verifieringsnyckeln innehåller den nyckel som token som signerats med, utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

## <a name="streaming-urls"></a>Strömmande URL: er
Om din tillgång har krypterats med flera DRM, bör du använda en kryptering-tagg i strömnings-URL: (format = 'm3u8 aapl', kryptering = ”xxx”).

Följande gäller:

* Endast noll eller en typ av enhetskryptering kan anges.
* Krypteringstyp behöver inte anges i URL: en om bara en kryptering har tillämpats på tillgången.
* Krypteringstyp är skiftlägeskänsligt.
* Följande krypteringstyper av kan anges:  
  * **cenc**: Common encryption (Playready eller Widevine)
  * **cbcs aapl**: Fairplay
  * **CBC**: AES envelope kryptering.

## <a name="common-scenarios"></a>Vanliga scenarier
Följande avsnitt visar hur du kan skydda lagrat innehåll, leverera dynamiskt krypterade strömmande medier, använder AMS-nyckel/licensleveranstjänst

* [Skydda med AES](media-services-protect-with-aes128.md) 
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-drm.md)
* [Strömma ditt innehåll skyddade HLS med Apple FairPlay/PlayReady eller](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Fler scenarier
* [Integrera Azure PlayReady Licenstjänsten med din egen krypterare/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
* [Med hjälp av castLabs för att leverera DRM-licenser till Azure Media Services](media-services-castlabs-integration.md)

>[!NOTE]
>Ett scenario där du använder ett externt DRM server(technology) och ström från AMS stöds inte för närvarande.


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Om PlayReady som en tjänst och AES dynamisk kryptering med Azure Media Services](http://mingfeiy.com/playready)

[Azure Media Services PlayReady licens leverans priser förklaras](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Felsöka för AES krypterad dataström i Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT-token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrera Azure Media Services OWIN MVC baserat app med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Använd Azure ACS problemet tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
