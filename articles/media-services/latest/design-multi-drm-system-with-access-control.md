---
title: Ett innehållsskyddssystem med flera DRM - Azure Media Services v3
description: I de här artiklarna beskrivs en detaljerad beskrivning av hur du utformar ett multi-DRM-innehållsskyddssystem med Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161791"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll 

Att designa och bygga ett DRM-delsystem (Digital Rights Management) för en over-the-top (OTT) eller online streaming lösning är en komplex uppgift. Operatörer / online videoleverantörer lägger vanligtvis ut denna uppgift till specialiserade DRM-tjänsteleverantörer. Målet med det här dokumentet är att presentera en referensdesign och en referensimplementering av ett heltäckande DRM-delsystem i en OTT- eller onlinestreaminglösning.

De riktade läsarna för detta dokument är ingenjörer som arbetar i DRM-delsystem av OTT eller online streaming /multiscreen-lösningar eller läsare som är intresserade av DRM-delsystem. Antagandet är att läsarna är bekanta med minst en av DRM-teknikerna på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe Access.

I den här diskussionen, genom multi-DRM, inkluderar vi de 3 DRM som stöds av Azure Media Services: Common Encryption (CENC) för PlayReady och Widevine, FairPlay samt AES-128 tydlig nyckelkryptering. En stor trend i online streaming och OTT industrin är att använda inhemska DRMs på olika klientplattformar. Denna trend är en övergång från den tidigare som använde en enda DRM och dess klient SDK för olika klientplattformar. När du använder CENC med multi-native DRM krypteras både PlayReady och Widevine enligt specifikationen [common encryption (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Fördelarna med att använda inbyggd multi-DRM för innehållsskydd är att det:

* Minskar krypteringskostnaden eftersom en enda krypteringsprocess används för att rikta olika plattformar med sina inbyggda DRM-moduler.
* Minskar kostnaden för att hantera tillgångar eftersom endast en enda kopia av tillgången behövs i lagring.
* Eliminerar DRM-klientlicensieringskostnad eftersom den inbyggda DRM-klienten vanligtvis är gratis på sin inbyggda plattform.

### <a name="goals-of-the-article"></a>Målen för artikeln

Syftet med denna artikel är att:

* Tillhandahålla en referensdesign av ett DRM-undersystem som använder alla 3 DRM -moduler (CENC för DASH, FairPlay för HLS och PlayReady för smidig strömning).
* Tillhandahålla en referensimplementering på Azure- och Azure Media Services-plattformen.
* Diskutera några design- och implementeringsämnen.

I följande tabell sammanfattas inbyggt DRM-stöd på olika plattformar och EME-stöd i olika webbläsare.

| **Klient plattform** | **Inbyggd DRM** | **Eme** |
| --- | --- | --- |
| **Smarta TV-apparater, stb:er** | PlayReady, Widevine och/eller andra | Inbäddad webbläsare/EME för PlayReady och/eller Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 för PlayReady|
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Krom för Widevine |
| **iOS** | FairPlay | Safari för FairPlay (sedan iOS 11.2) |
| **Macos** | FairPlay | Safari för FairPlay (sedan Safari 9 + på Mac OS X 10.11 + El Capitan)|
| **tvOS (på ett sätt)** | FairPlay | |

Med tanke på det aktuella tillståndet för distributionen för varje DRM, vill en tjänst vanligtvis implementera två eller tre DRM-moduler för att se till att du tar itu med alla typer av slutpunkter på bästa sätt.

Det finns en avvägning mellan komplexiteten i tjänstlogiken och komplexiteten på klientsidan för att nå en viss nivå av användarupplevelse på de olika klienterna.

Tänk på ditt val:

* PlayReady implementeras internt i alla Windows-enheter, på vissa Android-enheter och är tillgängligt via SDK:er för programvara på praktiskt taget vilken plattform som helst.
* Widevine implementeras internt i alla Android-enheter, i Chrome och i vissa andra enheter. Widevine stöds också i Firefox och Opera webbläsare över DASH.
* FairPlay finns på iOS, macOS och tvOS.


## <a name="a-reference-design"></a>En referensdesign
Det här avsnittet innehåller en referensdesign som är agnostisk för de tekniker som används för att implementera den.

Ett DRM-delsystem kan innehålla följande komponenter:

* Nyckelhantering
* DRM-krypteringsförpackning
* DRM-licensleverans
* Kontroll/åtkomstkontroll för berättigande
* Användarautentisering/auktorisering
* Appen Spelare
* Ursprungs-/innehållsleveransnätverk (CDN)

Följande diagram illustrerar interaktionen på hög nivå mellan komponenterna i ett DRM-delsystem:

![DRM-delsystem med CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Designen har tre grundläggande lager:

* Ett backoffice-lager (svart) visas inte externt.
* Ett DMZ-lager (mörkblått) innehåller alla slutpunkter som möter allmänheten.
* Ett offentligt internetlager (ljusblått) innehåller CDN och spelare med trafik över det offentliga internet.

Det bör också finnas ett verktyg för innehållshantering för att styra DRM-skydd, oavsett om det är statisk eller dynamisk kryptering. Ingångarna för DRM-kryptering inkluderar:

* MBR-videoinnehåll
* Innehållsnyckel
* Url:er för licensförvärv

Här är det högnivåflöde under uppspelningstiden:

* Användaren autentiseras.
* En auktoriseringstoken skapas för användaren.
* DRM-skyddat innehåll (manifest) hämtas till spelaren.
* Spelaren skickar en begäran om licensförvärv till licensservrar tillsammans med ett nyckel-ID och en auktoriseringstoken.

I följande avsnitt beskrivs utformningen av nyckelhantering.

| **ContentKey-till-tillgång** | **Scenario** |
| --- | --- |
| 1-till-1 |Det enklaste fallet. Det ger den finaste kontrollen. Men detta arrangemang resulterar i allmänhet i den högsta licensleveranskostnaden. Minst krävs en licensbegäran för varje skyddad tillgång. |
| 1-till-många |Du kan använda samma innehållsnyckel för flera tillgångar. För alla tillgångar i en logisk grupp, till exempel en genre eller delmängden av en genre (eller filmgen), kan du till exempel använda en enda innehållsnyckel. |
| Många-till-1 |Flera innehållsnycklar behövs för varje tillgång. <br/><br/>Om du till exempel behöver använda dynamiskt CENC-skydd med multi-DRM för MPEG-DASH och dynamisk AES-128-kryptering för HLS behöver du två separata innehållsnycklar. Varje innehållsnyckel behöver sin egen ContentKeyType. (Använd ContentKeyType.CommonEncryption för innehållsnyckeln som används för dynamiskt CENC-skydd. Använd ContentKeyType.EnvelopeCryption för innehållsnyckeln som används för dynamisk AES-128-kryptering.)<br/><br/>Som ett annat exempel, i CENC-skydd av DASH-innehåll, i teorin kan du använda en innehållsnyckel för att skydda videoströmmen och en annan innehållsnyckel för att skydda ljudströmmen. |
| Många-till-många |Kombination av de två föregående scenarierna. En uppsättning innehållsnycklar används för var och en av de flera tillgångarna i samma tillgångsgrupp. |

En annan viktig faktor att tänka på är användningen av beständiga och nonpersistent licenser.

Varför är dessa överväganden viktiga?

Om du använder ett offentligt moln för licensleverans har beständiga och icke-obeständiga licenser en direkt inverkan på licensleveranskostnaden. Följande två olika designfall tjänar till att illustrera:

* Månadsprenumeration: Använd en beständig licens och 1:n-innehållsnyckel-till-tillgång-mappning. Till exempel, för alla barnens filmer, använder vi en enda innehållsnyckel för kryptering. Om så är fallet:

    Totalt antal licenser som begärs för alla barnfilmer/enheter = 1

* Månadsprenumeration: Använd en icke-olicensierad licens och 1-till-1-mappning mellan innehållsnyckel och tillgång. Om så är fallet:

    Totalt antal licenser som begärs för alla barnfilmer/enheter = [antal filmer som har tittats] x [antal sessioner]

De två olika mönster resultera i mycket olika mönster licens begäran. De olika mönstren resulterar i olika licensleveranskostnader om licensleveranstjänsten tillhandahålls av ett offentligt moln som Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Kartdesign till teknik för implementering
Därefter mappas den allmänna designen till tekniker på Azure/Media Services-plattformen genom att ange vilken teknik som ska användas för varje byggblock.

I följande tabell visas mappningen.

| **Byggsten** | **Teknik** |
| --- | --- |
| **Spelare** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitetsprovider (IDP)** |Azure Active Directory (Azure AD) |
| **Säker tokentjänst (STS)** |Azure AD |
| **ARBETSFLÖDE för DRM-skydd** |Dynamiskt skydd i Azure Media Services |
| **DRM-licensleverans** |* Media Services licens leverans (PlayReady, Widevine, FairPlay) <br/>* Axinom licensserver <br/>* Anpassad PlayReady licensserver |
| **Ursprung** |Slutpunkt för direktuppspelning av Azure Media Services |
| **Nyckelhantering** |Behövs inte för referensimplementering |
| **Innehållshantering** |Ett C#-konsolprogram |

Med andra ord tillhandahålls både IDP och STS av Azure AD. [Azure Media Player-API:et](https://amp.azure.net/libs/amp/latest/docs/) används för spelaren. Både Azure Media Services och Azure Media Player stöder CENC över DASH, FairPlay över HLS, PlayReady över smidig streaming och AES-128-kryptering för DASH, HLS och smidig.

Följande diagram visar den övergripande strukturen och flödet med föregående teknikmappning:

![CENC på medietjänster](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om du vill ställa in DRM-innehållsskydd använder innehållshanteringsverktyget följande indata:

* Öppna innehåll
* Innehållsnyckel från nyckelhantering
* Url:er för licensförvärv
* En lista med information från Azure AD, till exempel målgrupps-, utfärdare- och tokenanspråk

Här är utdata från content management-verktyget:

* ContentKeyPolicy beskriver DRM-licensmall för varje typ av DRM som används.
* ContentKeyPolicyRestriction beskriver åtkomstkontrollen innan en DRM-licens utfärdas
* Streamingpolicy beskriver de olika kombinationerna av DRM - krypteringsläge - streamingprotokoll - behållarformat, för streaming
* StreamingLocator beskriver innehållsnyckel/IV som används för kryptering och strömmande webbadresser 

Här är flödet under körning:

* Vid användarautentisering genereras en JWT.
* En av anspråken i JWT är ett gruppanspråk som innehåller gruppobjekt-ID EntitledUserGroup. Detta påstående används för att klara rättighetskontrollen.
* Spelaren hämtar klientmanifestet för CENC-skyddat innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är DRM-skyddat.
   * Url:er för licensförvärv.
* Spelaren gör en begäran om licensförvärv baserat på webbläsaren/DRM som stöds. I begäran om licensförvärv skickas även nyckel-ID och JWT. Licensleveranstjänsten verifierar JWT och anspråken innan den utfärdar den nödvändiga licensen.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Genomförandeförfaranden
Implementeringen omfattar följande steg:

1. Förbered testtillgångar. Koda/paketera en testvideo till fragmenterad MP4 med flera bithastigheter i Media Services. Den här tillgången är *inte* DRM-skyddad. DRM-skydd görs genom dynamiskt skydd senare.

2. Skapa ett nyckel-ID och en innehållsnyckel (eventuellt från ett nyckelutsäde). I det här fallet behövs inte nyckelhanteringssystemet eftersom endast ett enda nyckel-ID och innehållsnyckel krävs för ett par testtillgångar.

3. Använd API:et för Media Services för att konfigurera leveranstjänster för flera DRM-licenser för testtillgången. Om du använder anpassade licensservrar av företaget eller företagets leverantörer i stället för licenstjänster i Media Services kan du hoppa över det här steget. Du kan ange url:er för licensförvärv i steget när du konfigurerar licensleverans. Api:et för medietjänster behövs för att ange vissa detaljerade konfigurationer, till exempel begränsning av auktoriseringsprincipen och licenssvarsmallar för olika DRM-licenstjänster. För närvarande tillhandahåller Azure-portalen inte det användargränssnitt som behövs för den här konfigurationen. Information på API-nivå och exempelkod finns i [Använda PlayReady och/eller Widevine dynamisk gemensam kryptering](protect-with-drm.md).

4. Använd API:et för mediatjänster för att konfigurera tillgångsleveransprincipen för testtillgången. Information på API-nivå och exempelkod finns i [Använda PlayReady och/eller Widevine dynamisk gemensam kryptering](protect-with-drm.md).

5. Skapa och konfigurera en Azure AD-klient i Azure.

6. Skapa några användarkonton och grupper i din Azure AD-klientorganisation. Skapa minst en "berättigad användare"-grupp och lägg till en användare i den här gruppen. Användare i den här gruppen klarar rättighetskontrollen i licensförvärvet. Användare som inte ingår i den här gruppen klarar inte autentiseringskontrollen och kan inte skaffa en licens. Medlemskap i den här gruppen "Berättigad användare" är ett obligatoriskt gruppanspråk i JWT som utfärdats av Azure AD. Du anger det här anspråkskravet i steget när du konfigurerar leveranstjänster för flera DRM-licenser.

7. Skapa en ASP.NET MVC-app som värd för din videospelare. Den här ASP.NET-appen är skyddad med användarautentisering mot Azure AD-klienten. Korrekta anspråk ingår i åtkomsttoken som erhållits efter användarautentisering. Vi rekommenderar OpenID Connect API för det här steget. Installera följande NuGet-paket:

   * Installationspaket Microsoft.Azure.ActiveDirectory.GraphClient
   * Installationspaket Microsoft.Owin.Security.OpenIdConnect
   * Installera paket Microsoft.Owin.Security.Cookies
   * Installera-paket Microsoft.Owin.Host.SystemWeb
   * Installera-paket Microsoft.IdentityModel.clients.ActiveDirectory

8. Skapa en spelare med hjälp av [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) för att ange vilken DRM-teknik som ska användas på olika DRM-plattformar.

9. I följande tabell visas testmatrisen.

    | **Drm** | **Webbläsare** | **Resultat för berättigade användare** | **Resultat för icke-adetitled användare** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge eller Internet Explorer 11 på Windows 10 |Lyckas |Underkänn |
    | **Widevine** |Chrome, Firefox, Opera |Lyckas |Underkänn |
    | **FairPlay** |Safari på macOS      |Lyckas |Underkänn |
    | **AES-128** |De flesta moderna webbläsare  |Lyckas |Underkänn |

Information om hur du konfigurerar Azure AD för en ASP.NET MVC-spelarapp finns i [Integrera en OWIN MVC-baserad app för Azure Media Services med Azure Active Directory och begränsa leverans av innehållsnyckel baserat på JWT-anspråk](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Mer information finns [i JWT-tokenautentisering i Azure Media Services och dynamisk kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Mer information om Azure AD:

* Du hittar utvecklarinformation i [Azure Active Directory-utvecklarguiden](../../active-directory/develop/v2-overview.md).
* Du hittar administratörsinformation i [Administrera din Azure AD-klientkatalog](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Vissa frågor i genomförandet

Använd följande felsökningsinformation för hjälp med implementeringsproblem.

* Utfärdarens URL måste sluta med "/". Målgruppen måste vara klient-ID för spelarprogrammet. Lägg också till "/" i slutet av utfärdarens URL.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    I [JWT Decoder](http://jwt.calebb.net/)ser du **aud** och **iss**, som visas i JWT:

    ![Jwt](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter till programmet i Azure AD på fliken **Konfigurera** för programmet. Behörigheter krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Använd rätt utfärdare när du ställer in dynamiskt CENC-skydd.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Följande fungerar inte:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID är Azure AD-klient-ID. GUID finns på popup-menyn **Slutpunkter** i Azure-portalen.

* Bevilja gruppmedlemskap anspråk privilegier. Kontrollera att följande finns i manifestfilen för Azure AD-program: 

    "groupMembershipClaims": "Alla" (standardvärdet är null)

* Ange rätt TokenType när du skapar begränsningskrav.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Eftersom du lägger till stöd för JWT (Azure AD) utöver SWT (ACS) är standard TokenType TokenType.JWT. Om du använder SWT/ACS måste du ange token till TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Det färdiga systemet och testet

I det här avsnittet får du hjälp med följande scenarier i det färdiga end-to-end-systemet så att du kan få en grundläggande bild av beteendet innan du får ett inloggningskonto:

* Om du behöver ett icke-integrerat scenario:

    * För videotillgångar som finns i Media Services och som antingen är oskyddade eller DRM-skyddade men utan tokenautentisering (utfärdar en licens till den som begärde det) kan du testa den utan att logga in. Växla till HTTP om videostreamingen är över HTTP.

* Om du behöver ett heltäckande integrerat scenario:

    * För videotillgångar under dynamiskt DRM-skydd i Media Services, med tokenautentisering och JWT som genereras av Azure AD, måste du logga in.

För spelarens webbprogram och dess inloggning, se [denna webbplats](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Användarinloggning
Om du vill testa det heltäckande integrerade DRM-systemet måste du ha ett konto skapat eller lagt till.

Vilket konto?

Även om Azure ursprungligen tillät åtkomst endast av Microsoft-kontoanvändare tillåts åtkomst nu av användare från båda systemen. Alla Azure-egenskaper litar nu på Azure AD för autentisering och Azure AD autentiserar organisationsanvändare. En federationsrelation skapades där Azure AD litar på att Konsumentidentitetssystemet för Microsoft-kontot autentiserar konsumentanvändare. Därför kan Azure AD autentisera gäst-Microsoft-konton samt inbyggda Azure AD-konton.

Eftersom Azure AD litar på Microsoft-kontodomänen kan du lägga till konton från någon av följande domäner till den anpassade Azure AD-klienten och använda kontot för att logga in:

| **Domännamn** | **Domain** |
| --- | --- |
| **Anpassad Azure AD-klientdomän** |somename.onmicrosoft.com |
| **Företagsdomän** |microsoft.com |
| **Microsoft-kontodomän** |outlook.com, live.com, hotmail.com |

Du kan kontakta någon av författarna för att få ett konto skapat eller lagt till åt dig.

Följande skärmbilder visar olika inloggningssidor som används av olika domänkonton:

**Anpassat Azure AD-klientdomänkonto:** Den anpassade inloggningssidan för den anpassade Azure AD-klientdomänen.

![Anpassat Azure AD-klientdomänkonto ett](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domänkonto med smartkort:** Inloggningssidan anpassad av Microsofts företags-IT med tvåfaktorsautentisering.

![Anpassat Azure AD-klientdomänkonto två](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: Inloggningssidan för Microsoft-kontot för konsumenter.

![Anpassat Azure AD-klientdomänkonto tre](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använda krypterade medietillägg för PlayReady

I en modern webbläsare med eme-stöd (Encrypted Media Extensions) för PlayReady, till exempel Internet Explorer 11 i Windows 8.1 eller senare och webbläsaren Microsoft Edge i Windows 10, är PlayReady den underliggande DRM för EME.

![Använd EME för PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Det mörka spelarområdet beror på att PlayReady-skyddet hindrar dig från att göra en skärmdump av skyddad video.

Följande skärmbild visar plugin-programmen för spelare och MSE-stöd (Microsoft Security Essentials)/EME:

![Plugin-program för spelare för PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 på Windows 10 gör att [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kan anropas på Windows 10-enheter som stöder det. PlayReady SL3000 låser upp flödet av förbättrat premiuminnehåll (4K, HDR) och nya innehållsleveransmodeller (för förbättrat innehåll).

För att fokusera på Windows-enheter är PlayReady den enda DRM i maskinvaran som är tillgänglig på Windows-enheter (PlayReady SL3000). En streamingtjänst kan använda PlayReady via EME eller via ett universellt Windows-plattformsprogram och erbjuda en högre videokvalitet med PlayReady SL3000 än en annan DRM. Vanligtvis flödar innehåll upp till 2K via Chrome eller Firefox, och innehåll upp till 4K flödar via Microsoft Edge/Internet Explorer 11 eller ett universellt Windows-plattformsprogram på samma enhet. Beloppet beror på tjänstinställningar och implementering.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine

I en modern webbläsare med STÖD för EME/Widevine, till exempel Chrome 41+ på Windows 10, Windows 8.1, Mac OSX Yosemite och Chrome på Android 4.4.4, är Google Widevine DRM bakom EME.

![Använd EME för Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine hindrar dig inte från att göra en skärmdump av skyddad video.

![Plugin-program för Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Använd EME för FairPlay

På samma sätt kan du testa FairPlay-skyddat innehåll i den här testspelaren i Safari på macOS eller iOS 11.2 och senare.

Se till att du sätter "FairPlay" som skyddInfo.type och sätta i rätt URL för ditt programcertifikat i FPS AC Path (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Oeniterade användare

Om en användare inte är medlem i gruppen Berättigade användare klarar användaren inte rättighetskontrollen. Multi-DRM-licenstjänsten vägrar sedan att utfärda den begärda licensen som visas. Den detaljerade beskrivningen är "Licenshämtning misslyckades", vilket är som planerat.

![Oeniterade användare](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Köra en anpassad säkerhetstokentjänst

Om du kör en anpassad STS utfärdas JWT av den anpassade STS med hjälp av antingen en symmetrisk eller en asymmetrisk nyckel.

Följande skärmbild visar ett scenario som använder en symmetrisk nyckel (med Chrome):

![Anpassad STS med symmetrisk nyckel](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Följande skärmbild visar ett scenario som använder en asymmetrisk nyckel via ett X509-certifikat (med en modern Microsoft-webbläsare):

![Anpassad STS med en asymmetrisk nyckel](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

I båda tidigare fallen förblir användarautentiseringen densamma. Den sker via Azure AD. Den enda skillnaden är att JWTs utfärdas av anpassade STS i stället för Azure AD. När du konfigurerar dynamiskt CENC-skydd anger begränsningen av licensleveranstjänsten vilken typ av JWT, antingen en symmetrisk eller en asymmetrisk nyckel.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar](frequently-asked-questions.md)
* [Översikt över innehållsskydd](content-protection-overview.md)
* [Skydda ditt innehåll med DRM](protect-with-drm.md)
