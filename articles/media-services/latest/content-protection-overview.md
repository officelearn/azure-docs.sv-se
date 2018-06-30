---
title: Skydda ditt innehåll med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över innehållsskydd med Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 28c10d7c478ea7a9b1d1bfa91da79452eba3a4b6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132990"
---
# <a name="content-protection-overview"></a>Översikt över innehållsskydd

Du kan använda Azure Media Services för att skydda mediet från den tidpunkt som den lämnar din dator via lagring, bearbetning och leverans. Du kan leverera live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre huvudsakliga digital rights management (DRM) system med Media Services: Microsoft PlayReady och Google Widevine Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för innehållsskydd Media Services: 

![Skydda innehåll](./media/content-protection/content-protection.png)

&#42;*dynamisk kryptering stöder AES-128 ”klartextnyckeln”, CBCS och CENC. Mer information finns i supportmatrisen [här](#streaming-protocols-and-encryption-types).*

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services. Artikeln innehåller också länkar till artiklar som handlar om hur du skyddar innehåll. 

## <a name="main-components-of-the-content-protection-system"></a>Huvudkomponenterna i systemets innehållsskydd

För att slutföra utformningen ”innehållsskydd” system-program, måste du förstå fullständigt omfånget för den mängd arbete. I följande lista ger en översikt av tre delar som du behöver implementera. 

1. Azure Media Services-kod
  
  * Licens mallar för PlayReady, Widevine och/eller FairPlay. Mallar kan du konfigurera rättigheter och behörigheter för varje används DRMs
  * Licens leverans auktorisering, ange logiken för auktorisering kontroll baserad på anspråk i JWT
  * Innehåll nycklar, strömningsprotokoll och motsvarande DRMs tillämpas, definiera DRM-kryptering

  > [!NOTE]
  > Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). Se [strömning protokoll och krypteringstyper](#streaming-protocols-and-encryption-types), för att se vad klokt att kombinera.
  
  I följande artikel Visar stegen för att kryptera innehållet med AES: [skydda med AES-kryptering](protect-with-aes128.md)
 
2. Spelaren med AES eller DRM-klienten. En videospelare app baserat på en spelare SDK (intern eller webbläsarbaserad) måste uppfylla följande krav:
  * Windows Media player SDK stöder nödvändiga DRM-klienter
  * SDK stöds krävs strömningsprotokoll: Smooth, DASH eller HLS
  * Windows Media player SDK behöver kunna hantera Skicka en JWT-token i licens förvärv begäran
  
    Du kan skapa en spelare med hjälp av den [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Använd den [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) att ange vilka DRM-teknik som ska användas på olika DRM-plattformar.

    För tester AES eller CENC (Widevine + PlayReady) krypterat innehåll, kan du använda [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Kontrollera att du klickar på ”Avancerat” och kontrollera AES och ange token.

    Om du vill testa FairPlay krypterat innehåll använder [detta test player](http://aka.ms/amtest). Windows Media player stöder Widevine PlayReady och FairPlay DRMs samt AES-128 Rensa nyckelkryptering. Du måste välja rätt webbläsaren om du vill testa olika DRMs: Chrome/Opera/Firefox för Widevine, MS Edge/IE11 för PlayReady, Safari på maOS för FairPlay.

3. Skydda säkerhetstokentjänst (STS), som utfärdar JSON-Webbtoken (JWT) som åtkomst-token för åtkomst till backend-resurser. Du kan använda AMS-licensleveranstjänster som backend-resurs. En STS har du definiera följande:

  * Utfärdaren och målgrupp (eller scope)
  * Anspråk som är beroende av affärsbehov i innehållsskydd
  * Symmetriskt eller asymmetriskt verifiering för signaturverifiering
  * Stöd för nyckelförnyelse (om det behövs)

    Du kan använda [STS verktyget](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) test STS, som har stöd för alla 3 typer av Verifieringsnyckeln: symmetrisk, asymmetrisk eller AAD med nyckelförnyelse. 

> [!NOTE]
> Vi rekommenderar starkt att fokusera och fullständigt Testa varje del (beskrivs ovan) innan du fortsätter till nästa steg. Testa din ”innehållsskydd” system med verktyg som anges i listan ovan.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll för dataströmmar och krypteringstyper

Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med AES Rensa nyckel eller DRM-kryptering med PlayReady eller Widevine FairPlay. För närvarande kan du kryptera HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Varje protokoll som stöder följande krypteringsmetoder:

|Protokoll|Behållares format|Krypteringsschemat|
|---|---|---|---|
|MPEG-DASH|Alla|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Alla|AES|
||MPG2 TS |CBCS (Fairplay) |
||MPG2 TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Media Services v3 en innehållsnyckel är associerad med StreamingLocator (se [det här exemplet](protect-with-aes128.md)). Om du använder Media Services viktiga tjänsten bör du automatiskt generera innehållsnyckeln. Du ska skapa innehållsnyckeln själv om du använder du egen nyckel leverans tjänsten, eller om du behöver hantera ett scenario med hög tillgänglighet, där du måste ha samma innehållsnyckeln i två datacenter.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med hjälp av AES klartextnyckeln eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services viktiga leverans tjänsten eller viktiga tjänsten du angett. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>Ta bort nyckeln jämfört med AES-128. DRM

Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den främsta skillnaden mellan de två systemen är att innehållsnyckeln skickas till klienten i ett okrypterat format (”i klartext”) med AES-kryptering. Därför kan den nyckel som används för att kryptera innehållet visas i en spårning i nätverket på klienten i oformaterad text. AES-128 Rensa nyckelkryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom ett företag för att ses av anställda).

Avmarkera nyckelkryptering PlayReady och Widevine FairPlay får du en högre nivå av kryptering jämfört med att AES-128. Innehållsnyckeln överförs i krypterat format. Dessutom hanteras dekryptering i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta möjliga säkerhetsnivå.

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

För att skydda dina tillgångar vilande ska tillgångarna krypteras med kryptering för lagring på serversidan. Följande tabell visar hur sida lagringskryptering fungerar i Media Services v3:

|Krypteringsalternativ|Beskrivning|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering| AES-256-kryptering, nyckel hanteras av Media Services|Stöds inte<sup>(1)</sup>|
|[Lagringstjänstens kryptering av vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|
|[Kryptering på klientsidan](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Klientsidans kryptering som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|

<sup>1</sup> i Media Services v3 lagringskryptering (AES 256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar har skapats med Media Services v2. Vilket innebär att v3 fungerar med befintlig lagring krypterad tillgångar, men tillåter inte att skapa nya.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar leverans av tjänsten

Media Services tillhandahåller en nyckel leveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay) licenser och AES-nycklar till auktoriserade klienter. Du kan använda REST API eller ett Media Services-klientbibliotek för att konfigurera autentiseringen och auktoriseringen av principer för dina licenser och nycklar.

## <a name="control-content-access"></a>Behörighet för innehåll

Du kan styra vem som har tillgång till ditt innehåll genom att konfigurera principen för innehåll nyckel. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera principen för innehåll nyckel. Klienten (spelaren) måste uppfylla principen innan nyckeln levereras till klienten. Principen för innehåll nyckel kan ha **öppna** eller **token** begränsning. 

Med en token begränsat innehåll viktiga princip skickas innehållsnyckeln endast till en klient som anger en giltig JSON-Webbtoken (JWT) eller simple web token (SWT) i nyckel-licens-begäran. Denna token måste vara utfärdat av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returnerar begärda nyckel/licensen till klienten om token är giltig och anspråk i token som matchar de som konfigurerats för den nyckel/licensen.

När du konfigurerar den tokenbegränsade principen måste du ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar. Primär Verifieringsnyckeln innehåller den nyckel som token som signerats med. Utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen kallas ibland omfång, beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

## <a name="streaming-urls"></a>Direktuppspelnings-URL:er

Om din tillgång har krypterats med flera DRM använder kryptering taggen i strömnings-URL: (format = 'm3u8 aapl', kryptering = ”xxx”).

Följande gäller:

* Krypteringstyp behöver inte anges i URL: en om bara en kryptering har tillämpats på tillgången.
* Krypteringstyp är skiftlägeskänsligt.
* Följande krypteringstyper av kan anges:
  * **cenc**: för PlayReady eller Widevine (common encryption)
  * **cbcs aapl**: för FairPlay (CBC AES-kryptering)
  * **CBC**: för AES envelope kryptering

## <a name="troubleshooting-tips"></a>Felsökningstips

Använd följande felsökningsinformation för hjälp med implementeringsproblem med.

* Utfärdaren URL måste avslutas med ”/”. Målgruppen måste vara player programmet klient-ID. Lägg även till ”/” i slutet av utfärdar-URL.

  ```
  <add key="ida:audience" value="[Application Client ID GUID]" />
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
  ```

* Lägg till behörigheter till programmet i Azure AD på den **konfigurera** för programmet. Behörigheter som krävs för varje program, både lokala och distribuerade versioner.
* Använd rätt utfärdaren när du ställer in skydd för dynamiska CENC.

  ```
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
  ```

  Följande fungerar inte:

  ```
  <add key="ida:issuer" value="https://username.onmicrosoft.com/" />
  ```

  GUID är Azure AD-klient-ID. GUID kan hittas i den **slutpunkter** popup-menyn i Azure-portalen.

* Bevilja gruppmedlemskap anspråk privilegier. Kontrollera att följande finns i Azure AD application manifest-filen: 

    ”groupMembershipClaims”: ”alla” (standardvärdet är null)

## <a name="next-steps"></a>Nästa steg

[Skydda med AES-kryptering](protect-with-aes128.md)
