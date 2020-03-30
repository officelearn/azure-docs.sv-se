---
title: Design av ett innehållsskyddssystem med åtkomstkontroll med Hjälp av Azure Media Services | Microsoft-dokument
description: Läs mer om hur du licensierar Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162998"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Design av ett innehållsskyddssystem med åtkomstkontroll med Hjälp av Azure Media Services 

## <a name="overview"></a>Översikt

Att utforma och bygga ett drm-delsystem (Digital Rights Management) för en over-the-top (OTT) eller online streaming lösning är en komplex uppgift. Operatörer / online videoleverantörer lägger vanligtvis ut denna uppgift till specialiserade DRM-tjänsteleverantörer. Målet med det här dokumentet är att presentera en referensdesign och implementering av ett heltäckande DRM-delsystem i en OTT- eller onlinestreaminglösning.

De riktade läsarna för detta dokument är ingenjörer som arbetar i DRM-delsystem av OTT eller online streaming /multiscreen-lösningar eller läsare som är intresserade av DRM-delsystem. Antagandet är att läsarna är bekanta med minst en av DRM-teknikerna på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe Access.

I den här diskussionen om DRM inkluderar vi även gemensam kryptering (CENC) med multi-DRM. En stor trend inom online streaming och OTT industrin är att använda CENC med multi-native DRM på olika klientplattformar. Denna trend är en övergång från den tidigare som använde en enda DRM och dess klient SDK för olika klientplattformar. När du använder CENC med multi-native DRM krypteras både PlayReady och Widevine enligt specifikationen [common encryption (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Fördelarna med CENC med multi-DRM är att det:

* Minskar krypteringskostnaden eftersom en enda krypteringsprocess används för att rikta olika plattformar med sina inbyggda DRM-moduler.
* Minskar kostnaden för att hantera krypterade tillgångar eftersom endast en enda kopia av krypterade tillgångar behövs.
* Eliminerar DRM-klientlicensieringskostnad eftersom den inbyggda DRM-klienten vanligtvis är gratis på sin inbyggda plattform.

Microsoft är en aktiv främjare av DASH och CENC tillsammans med några stora branschaktörer. Azure Media Services ger stöd för DASH och CENC. För de senaste meddelandena finns i följande bloggar:

*  [Vi ber att få presentera Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services lägger till Google Widevine-paketering för att leverera en multi-DRM-ström](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Målen för artikeln

Syftet med denna artikel är att:

* Tillhandahålla en referensdesign av ett DRM-delsystem som använder CENC med multi-DRM.
* Tillhandahålla en referensimplementering på en Azure/Media Services-plattform.
* Diskutera några design- och implementeringsämnen.

I artikeln omfattar termen "multi-DRM" följande produkter:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

I följande tabell sammanfattas den inbyggda plattformen/den inbyggda appen och webbläsarna som stöds av varje DRM.

| **Klient plattform** | **Inbyggt DRM-stöd** | **Webbläsare/app** | **Strömmande format** |
| --- | --- | --- | --- |
| **Smarta TV-apparater, operatör STBs, OTT STBs** |SpelaLäs i första hand, och/eller Widevine, och/eller andra |Linux, Opera, WebKit, andra |Olika format |
| **Windows 10-enheter (Windows PC, Windows-surfplattor, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universell Windows-plattform |DASH (för HLS stöds inte PlayReady)<br/><br/>DASH, Smooth Streaming (för HLS stöds inte PlayReady) |
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-klienter och Apple TV** |FairPlay |Safari 8+/EME |HLS |

Med tanke på det aktuella tillståndet för distributionen för varje DRM, vill en tjänst vanligtvis implementera två eller tre DRM-moduler för att se till att du tar itu med alla typer av slutpunkter på bästa sätt.

Det finns en avvägning mellan komplexiteten i tjänstlogiken och komplexiteten på klientsidan för att nå en viss nivå av användarupplevelse på de olika klienterna.

Tänk på ditt val:

* PlayReady implementeras internt i alla Windows-enheter, på vissa Android-enheter och är tillgängligt via SDK:er för programvara på praktiskt taget vilken plattform som helst.
* Widevine implementeras internt i alla Android-enheter, i Chrome och i vissa andra enheter.
* FairPlay är endast tillgängligt på iOS- och Mac OS-klienter eller via iTunes.

Det finns två alternativ för en typisk multi-DRM:

* PlayReady och Widevine
* PlayReady, Widevine och FairPlay

## <a name="a-reference-design"></a>En referensdesign
Det här avsnittet innehåller en referensdesign som är agnostisk för de tekniker som används för att implementera den.

Ett DRM-delsystem kan innehålla följande komponenter:

* Nyckelhantering
* DRM-förpackning
* DRM-licensleverans
* Kontroll av rättigheter
* Autentisering/auktorisering
* Spelare
* Ursprungs-/innehållsleveransnätverk (CDN)

Följande diagram illustrerar interaktionen på hög nivå mellan komponenterna i ett DRM-delsystem:

![DRM-delsystem med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Tjänsten Säkerhetstoken (STS)** |Azure AD |
| **ARBETSFLÖDE för DRM-skydd** |Dynamiskt skydd för Media Services |
| **DRM-licensleverans** |* Media Services licens leverans (PlayReady, Widevine, FairPlay) <br/>* Axinom licensserver <br/>* Anpassad PlayReady licensserver |
| **Ursprung** |Slutpunkt för direktuppspelning av Media Services |
| **Nyckelhantering** |Behövs inte för referensimplementering |
| **Innehållshantering** |Ett C#-konsolprogram |

Med andra ord används både IDP och STS med Azure AD. [Azure Media Player-API:et](https://amp.azure.net/libs/amp/latest/docs/) används för spelaren. Både Media Services och Media Player stöder DASH och CENC med multi-DRM.

Följande diagram visar den övergripande strukturen och flödet med föregående teknikmappning:

![CENC på medietjänster](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om du vill konfigurera dynamisk CENC-kryptering använder innehållshanteringsverktyget följande indata:

* Öppna innehåll
* Innehållsnyckel från nyckelgenerering/hantering
* Url:er för licensförvärv
* En lista med information från Azure AD

Här är utdata från content management-verktyget:

* ContentKeyAuthorizationPolicy innehåller specifikationen för hur licensleverans verifierar specifikationerna för JWT (JWT) och DRM (Web Token).
* AssetDeliveryPolicy innehåller specifikationer för direktuppspelningsformat, DRM-skydd och licensförvärv webbadresser.

Här är flödet under körning:

* Vid användarautentisering genereras en JWT.
* En av anspråken i JWT är ett gruppanspråk som innehåller gruppobjekt-ID EntitledUserGroup. Detta påstående används för att klara rättighetskontrollen.
* Spelaren hämtar klientmanifestet för CENC-skyddat innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är CENC-skyddat.
   * Url:er för licensförvärv.
* Spelaren gör en begäran om licensförvärv baserat på webbläsaren/DRM som stöds. I begäran om licensförvärv skickas även nyckel-ID och JWT. Licensleveranstjänsten verifierar JWT och anspråken innan den utfärdar den nödvändiga licensen.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Genomförandeförfaranden
Implementeringen omfattar följande steg:

1. Förbered testtillgångar. Koda/paketera en testvideo till fragmenterad MP4 med flera bithastigheter i Media Services. Den här tillgången är *inte* DRM-skyddad. DRM-skydd görs genom dynamiskt skydd senare.

2. Skapa ett nyckel-ID och en innehållsnyckel (eventuellt från ett nyckelutsäde). I det här fallet behövs inte nyckelhanteringssystemet eftersom endast ett enda nyckel-ID och innehållsnyckel krävs för ett par testtillgångar.

3. Använd API:et för Media Services för att konfigurera leveranstjänster för flera DRM-licenser för testtillgången. Om du använder anpassade licensservrar av företaget eller företagets leverantörer i stället för licenstjänster i Media Services kan du hoppa över det här steget. Du kan ange url:er för licensförvärv i steget när du konfigurerar licensleverans. Api:et för medietjänster behövs för att ange vissa detaljerade konfigurationer, till exempel begränsning av auktoriseringsprincipen och licenssvarsmallar för olika DRM-licenstjänster. För närvarande tillhandahåller Azure-portalen inte det användargränssnitt som behövs för den här konfigurationen. Information på API-nivå och exempelkod finns i [Använda PlayReady och/eller Widevine dynamisk gemensam kryptering](media-services-protect-with-playready-widevine.md).

4. Använd API:et för mediatjänster för att konfigurera tillgångsleveransprincipen för testtillgången. Information på API-nivå och exempelkod finns i [Använda PlayReady och/eller Widevine dynamisk gemensam kryptering](media-services-protect-with-playready-widevine.md).

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

* Du hittar utvecklarinformation i [Azure Active Directory-utvecklarguiden](../../active-directory/azuread-dev/v1-overview.md).
* Du hittar administratörsinformation i [Administrera din Azure AD-klientkatalog](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Vissa frågor i genomförandet
Använd följande felsökningsinformation för hjälp med implementeringsproblem.

* Utfärdarens URL måste sluta med "/". Målgruppen måste vara klient-ID för spelarprogrammet. Lägg också till "/" i slutet av utfärdarens URL.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    I [JWT Decoder](http://jwt.calebb.net/)ser du **aud** och **iss**, som visas i JWT:

    ![Jwt](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter till programmet i Azure AD på fliken **Konfigurera** för programmet. Behörigheter krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>Ytterligare ämnen för implementering
I det här avsnittet beskrivs några ytterligare ämnen i design och implementering.

### <a name="http-or-https"></a>HTTP eller HTTPS?
Programmet ASP.NET MVC-spelare måste ha stöd för följande:

* Användarautentisering via Azure AD, som är under HTTPS.
* JWT-utbyte mellan klienten och Azure AD, som ligger under HTTPS.
* DRM-licensförvärv av klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. Produktpaketet PlayReady föreskriver inte HTTPS för licensleverans. Om licensservern för PlayReady ligger utanför Media Services kan du använda http- eller HTTPS-tjänster.

Programmet ASP.NET spelare använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP är dock att föredra för direktuppspelning, så du måste överväga problemet med blandat innehåll.

* Webbläsaren tillåter inte blandat innehåll. Men plug-ins som Silverlight och OSMF plug-in för smidig och DASH gör det. Blandat innehåll är ett säkerhetsproblem på grund av hotet om möjligheten att injicera skadliga JavaScript, vilket kan orsaka att kunddata är i fara. Webbläsare blockerar den här funktionen som standard. Det enda sättet att komma runt det är på serversidan (ursprung) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte en bra idé heller.
* Undvik blandat innehåll. Både spelarprogrammet och Media Player ska använda HTTP eller HTTPS. När du spelar upp blandat innehåll kräver silverlightSS-tekniken att du rensar en varning med blandat innehåll. FlashSS-tekniken hanterar blandat innehåll utan en varning om blandat innehåll.
* Om slutpunkten för direktuppspelning skapades före augusti 2014 stöder den inte HTTPS. I det här fallet skapar och använder du en ny slutpunkt för direktuppspelning för HTTPS.

I referensimplementeringen för DRM-skyddat innehåll är både programmet och direktuppspelningen under HTTPS. För öppet innehåll behöver spelaren inte autentisering eller licens, så du kan använda antingen HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory-signeringsnyckelvält
Signering av nyckelvältning är en viktig punkt att ta hänsyn till i implementeringen. Om du ignorerar det, slutar det färdiga systemet så småningom fungerar helt, inom högst sex veckor.

Azure AD använder branschstandarder för att skapa förtroende mellan sig själv och program som använder Azure AD. Azure AD använder en signeringsnyckel som består av ett offentligt och privat nyckelpar. När Azure AD skapar en säkerhetstoken som innehåller information om användaren, signeras den av Azure AD med en privat nyckel innan den skickas tillbaka till programmet. För att verifiera att token är giltig och kommer från Azure AD måste programmet validera tokens signatur. Programmet använder den offentliga nyckeln som exponeras av Azure AD som finns i klientens federationsmetadatadokument. Den här offentliga nyckeln och signeringsnyckeln som den härleder från är samma som används för alla klienter i Azure AD.

Mer information om Azure AD-nyckelöverrullning finns [i Viktig information om hur du signerar nyckelöverrullning i Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mellan det [offentlig-privata nyckelparet:](https://login.microsoftonline.com/common/discovery/keys/)

* Den privata nyckeln används av Azure AD för att generera en JWT.
* Den offentliga nyckeln används av ett program som DRM-licensleveranstjänster i Media Services för att verifiera JWT.

Av säkerhetsskäl roterar Azure AD certifikatet med jämna mellanrum (var sjätte vecka). Vid säkerhetsöverträdelse kan nyckelvälten ske när som helst. Därför måste licensleveranstjänsterna i Media Services uppdatera den offentliga nyckeln som används som Azure AD roterar nyckelparet. Annars misslyckas tokenautentisering i Media Services och ingen licens utfärdas.

Om du vill konfigurera den här tjänsten anger du TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument när du konfigurerar DRM-licensleveranstjänster.

Här är JWT flödet:

* Azure AD utfärdar JWT med den aktuella privata nyckeln för en autentrad användare.
* När en spelare ser en CENC med multi-DRM-skyddat innehåll, lokaliseras först JWT som utfärdats av Azure AD.
* Spelaren skickar en begäran om licensförvärv med JWT för att licensiera leveranstjänster i Media Services.
* Licensleveranstjänsterna i Media Services använder den aktuella/giltiga offentliga nyckeln från Azure AD för att verifiera JWT innan licenserna utfärdas.

DRM-licensleveranstjänster kontrollerar alltid om den aktuella/giltiga offentliga nyckeln från Azure AD. Den offentliga nyckeln som presenteras av Azure AD är nyckeln som används för att verifiera en JWT som utfärdats av Azure AD.

Vad händer om nyckelförtrollningen sker efter att Azure AD genererar en JWT men innan JWT skickas av spelare till DRM-licensleveranstjänster i Media Services för verifiering?

Eftersom en nyckel kan rullas över när som helst är mer än en giltig offentlig nyckel alltid tillgänglig i federationsmetadatadokumentet. Licensleverans med Media Services kan använda någon av nycklarna som anges i dokumentet. Eftersom en nyckel kan rullas snart, kan en annan vara dess ersättare, och så vidare.

### <a name="where-is-the-access-token"></a>Var finns åtkomsttoken?
Om du tittar på hur en webbapp anropar en API-app under [Application identity med OAuth 2.0-klientautentiseringsbehörighetsbehörighet](../../active-directory/azuread-dev/web-api.md)är autentiseringsflödet följande:

* En användare loggar in på Azure AD i webbprogrammet. Mer information finns i [Webbläsare till webbprogram](../../active-directory/azuread-dev/web-app.md).
* Slutpunkten för Azure AD-auktorisering omdirigerar användaragenten tillbaka till klientprogrammet med en auktoriseringskod. Användaragenten returnerar auktoriseringskoden till klientprogrammets omdirigera URI.
* Webbprogrammet måste hämta en åtkomsttoken så att den kan autentisera till webb-API:et och hämta önskad resurs. Den gör en begäran till Azure AD-tokenslutpunkten och tillhandahåller autentiseringsuppgifterna, klient-ID: n och webb-API:s program-ID URI. Den presenterar auktoriseringskoden för att bevisa att användaren samtyckte.
* Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API:et.
* Över HTTPS använder webbprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i "Auktoriseringshuvudet för begäran till webb-API:et". Webb-API:et validerar sedan JWT. Om valideringen lyckas returneras önskad resurs.

I det här programidentitetsflödet litar webb-API:et på att webbprogrammet autentiserade användaren. Därför kallas det här mönstret för ett betrott delsystem. [Auktoriseringsflödesdiagrammet](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) beskriver hur auktoriseringskod-bidragsflödet fungerar.

Licensförvärv med tokenbegränsning följer samma betrodda undersystemmönster. Licensleveranstjänsten i Media Services är webb-API-resursen eller den "backend-resurs" som ett webbprogram behöver komma åt. Så var är åtkomsttoken?

Åtkomsttoken hämtas från Azure AD. Efter lyckad användarautentisering returneras en auktoriseringskod. Auktoriseringskoden används sedan, tillsammans med klient-ID och appnyckeln, för att byta mot åtkomsttoken. Åtkomsttoken används för att komma åt ett "pekare"-program som pekar på eller representerar licensleveranstjänsten för Media Services.

Så här registrerar och konfigurerar du pekarappen i Azure AD:

1. I Azure AD-klienten:

   * Lägg till ett program (resurs) med inloggnings-URL:en https://[resource_name].azurewebsites.net/. 
   * Lägg till ett app-ID med URL:en https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Lägg till en ny nyckel för resursappen.

3. Uppdatera appmanifestfilen så att egenskapen groupMembershipClaims har värdet "groupMembershipClaims": "Alla".

4. I Azure AD-appen som pekar på spelarens webbapp lägger du till resursappen som lades till i steg 1 i avsnittet **Behörigheter till andra program.** Under **Delegerad behörighet**väljer du **Access [resource_name]**. Det här alternativet ger webbappen behörighet att skapa åtkomsttoken som kommer åt resursappen. Gör detta för både den lokala och distribuerade versionen av webbappen om du utvecklar med Visual Studio och Azure-webbappen.

JWT som utfärdas av Azure AD är den åtkomsttoken som används för att komma åt pekarresursen.

### <a name="what-about-live-streaming"></a>Vad sägs om live streaming?
Den tidigare diskussionen fokuserade på tillgångar på efterfråge. Vad sägs om live streaming?

Du kan använda exakt samma design och implementering för att skydda direktuppspelning i Media Services genom att behandla tillgången som är associerad med ett program som en VOD-tillgång.

För att göra livestreaming i Media Services måste du skapa en kanal och sedan skapa ett program under kanalen. Om du vill skapa programmet måste du skapa en tillgång som innehåller programmets livearkiv. Om du vill ge CENC multi-DRM-skydd för liveinnehållet använder du samma inställning/bearbetning på tillgången som om det vore en VOD-tillgång innan du startar programmet.

### <a name="what-about-license-servers-outside-media-services"></a>Hur är det med licensservrar utanför Media Services?
Ofta investerade kunder i en licensservergrupp antingen i sitt eget datacenter eller ett som finns hos DRM-tjänsteleverantörer. Med Content Protection för Media Services kan du arbeta i hybridläge. Innehållet kan vara värd och dynamiskt skyddat i Media Services, medan DRM-licenser levereras av servrar utanför Media Services. I så fall bör du tänka på följande ändringar:

* STS måste utfärda token som är acceptabla och som kan verifieras av licensservergruppen. Till exempel kräver Widevine-licensservrarna som tillhandahålls av Axinom en specifik JWT som innehåller ett berättigandemeddelande. Därför måste du ha en STS att utfärda en sådan JWT. Information om den här typen av implementering finns i [Azure Documentation Center](https://azure.microsoft.com/documentation/) och finns i Använda [Axinom för att leverera Widevine-licenser till Azure Media Services](media-services-axinom-integration.md).
* Du behöver inte längre konfigurera licensleveranstjänsten (ContentKeyAuthorizationPolicy) i Media Services. Du måste ange url:er för licensförvärv (för PlayReady, Widevine och FairPlay) när du konfigurerar AssetDeliveryPolicy för att konfigurera CENC med multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Vad händer om jag vill använda en anpassad STS?
En kund kan välja att använda en anpassad STS för att tillhandahålla JWTs. Orsaker är:

* IDP som används av kunden stöder inte STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöva mer flexibel eller hårdare kontroll för att integrera STS med kundens prenumerant faktureringssystem. En MVPD-operatör kan till exempel erbjuda flera OTT-prenumerationspaket, till exempel premium, grundläggande och sport. Operatören kanske vill matcha anspråken i en token med en prenumerant paket så att endast innehållet i ett visst paket görs tillgängligt. I det här fallet ger en anpassad STS den flexibilitet och kontroll som behövs.

När du använder en anpassad STS måste två ändringar göras:

* När du konfigurerar licensleveranstjänst för en tillgång måste du ange säkerhetsnyckeln som används för verifiering av den anpassade STS i stället för den aktuella nyckeln från Azure AD. (Mer information följer.) 
* När en JTW-token genereras anges en säkerhetsnyckel i stället för den privata nyckeln för det aktuella X509-certifikatet i Azure AD.

Det finns två typer av säkerhetsnycklar:

* Symmetrisk nyckel: Samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: Ett offentligt-privat nyckelpar i ett X509-certifikat används med en privat nyckel för att kryptera/generera en JWT och med den offentliga nyckeln för att verifiera token.

> [!NOTE]
> Om du använder .NET Framework/C# som utvecklingsplattform måste X509-certifikatet som används för en asymmetrisk säkerhetsnyckel ha en nyckellängd på minst 2048. Detta är ett krav för klassen System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework. Annars genereras följande undantag:
> 
> IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey för signering kan inte vara mindre än 2048-bitar.

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

![Anpassat Azure AD-klientdomänkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domänkonto med smartkort:** Inloggningssidan anpassad av Microsofts företags-IT med tvåfaktorsautentisering.

![Anpassat Azure AD-klientdomänkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: Inloggningssidan för Microsoft-kontot för konsumenter.

![Anpassat Azure AD-klientdomänkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använda krypterade medietillägg för PlayReady
I en modern webbläsare med eme-stöd (Encrypted Media Extensions) för PlayReady, till exempel Internet Explorer 11 i Windows 8.1 eller senare och webbläsaren Microsoft Edge i Windows 10, är PlayReady den underliggande DRM för EME.

![Använd EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Det mörka spelarområdet beror på att PlayReady-skyddet hindrar dig från att göra en skärmdump av skyddad video.

Följande skärmbild visar plugin-programmen för spelare och MSE-stöd (Microsoft Security Essentials)/EME:

![Plugin-program för spelare för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 på Windows 10 gör att [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kan anropas på Windows 10-enheter som stöder det. PlayReady SL3000 låser upp flödet av förbättrat premiuminnehåll (4K, HDR) och nya innehållsleveransmodeller (för förbättrat innehåll).

För att fokusera på Windows-enheter är PlayReady den enda DRM i maskinvaran som är tillgänglig på Windows-enheter (PlayReady SL3000). En streamingtjänst kan använda PlayReady via EME eller via ett universellt Windows-plattformsprogram och erbjuda en högre videokvalitet med PlayReady SL3000 än en annan DRM. Vanligtvis flödar innehåll upp till 2K via Chrome eller Firefox, och innehåll upp till 4K flödar via Microsoft Edge/Internet Explorer 11 eller ett universellt Windows-plattformsprogram på samma enhet. Beloppet beror på tjänstinställningar och implementering.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine
I en modern webbläsare med STÖD för EME/Widevine, till exempel Chrome 41+ på Windows 10, Windows 8.1, Mac OSX Yosemite och Chrome på Android 4.4.4, är Google Widevine DRM bakom EME.

![Använd EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine hindrar dig inte från att göra en skärmdump av skyddad video.

![Plugin-program för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Oeniterade användare
Om en användare inte är medlem i gruppen Berättigade användare klarar användaren inte rättighetskontrollen. Multi-DRM-licenstjänsten vägrar sedan att utfärda den begärda licensen som visas. Den detaljerade beskrivningen är "Licenshämtning misslyckades", vilket är som planerat.

![Oeniterade användare](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Köra en anpassad säkerhetstokentjänst
Om du kör en anpassad STS utfärdas JWT av den anpassade STS med hjälp av antingen en symmetrisk eller en asymmetrisk nyckel.

Följande skärmbild visar ett scenario som använder en symmetrisk nyckel (med Chrome):

![Anpassad STS med symmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Följande skärmbild visar ett scenario som använder en asymmetrisk nyckel via ett X509-certifikat (med en modern Microsoft-webbläsare):

![Anpassad STS med en asymmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I båda tidigare fallen förblir användarautentiseringen densamma. Den sker via Azure AD. Den enda skillnaden är att JWTs utfärdas av anpassade STS i stället för Azure AD. När du konfigurerar dynamiskt CENC-skydd anger begränsningen av licensleveranstjänsten vilken typ av JWT, antingen en symmetrisk eller en asymmetrisk nyckel.

## <a name="summary"></a>Sammanfattning

Det här dokumentet diskuterade CENC med multi-native DRM och åtkomstkontroll via tokenautentisering, dess design och dess implementering med hjälp av Azure, Media Services och Media Player.

* En referensdesign presenterades som innehåller alla nödvändiga komponenter i ett DRM/CENC-delsystem.
* En referensimplementering presenterades på Azure, Media Services och Media Player.
* Några ämnen som är direkt involverade i utformningen och genomförandet diskuterades också.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
