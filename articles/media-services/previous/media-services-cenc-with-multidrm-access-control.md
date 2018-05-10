---
title: 'CENC med multi-DRM och access control: en referens design och implementeringslösning på Azure och Azure Media Services | Microsoft Docs'
description: Läs mer om hur Microsoft Smooth Streaming klienten portera Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 8f072f13909190eee194565673ccfa1f381f7503
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC med multi-DRM och access control: en referens design och implementeringslösning på Azure och Azure Media Services
 
## <a name="introduction"></a>Introduktion
Designa och skapa en digital rights management (DRM) undersystem för en över-the-upp (OTT) eller online streaming lösningen är en komplicerad uppgift. Operatörer online video leverantörer normalt lägga ut den här uppgiften till specialiserade DRM-leverantörer. Målet med det här dokumentet är att presentera en referens för design och implementering av ett DRM-undersystem för slutpunkt till slutpunkt i en OTT eller strömmande onlinelösning.

De aktuella läsarna för det här dokumentet är tekniker som arbetar i DRM delsystem i OTT eller direktuppspelning flera/skärmar lösningar online eller läsare som är intresserade av DRM-undersystem. Antas att läsaren känner till minst en av DRM-tekniker på marknaden, till exempel PlayReady Widevine, FairPlay eller Adobe åtkomst.

I den här diskussionen av DRM innefatta vi också vanliga kryptering (CENC) med multi-DRM. En större trend i online-strömning och OTT bransch är att använda CENC med flera interna DRM på olika klientplattformar. Denna trend är en övergång från föregående som används av en enda DRM och dess klient-SDK för olika klientplattformar. När du använder CENC med flera interna DRM både PlayReady och Widevine krypteras enligt den [Common Encryption (ISO/IEC 23001 7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikation.

Fördelarna med CENC med multi-DRM är att den:

* Minskar kostnaden för kryptering eftersom en enda krypteringsprocessen används för att fokusera på olika plattformar med dess ursprungliga DRMs.
* Minskar kostnaden för att hantera krypterade tillgångar eftersom bara en enda kopia av krypterade tillgångar krävs.
* Eliminerar DRM klientlicensiering kostnaden eftersom den inbyggda DRM-klienten är vanligtvis ledigt på dess ursprungliga plattform.

Microsoft är en aktiv förslagsställare DASH och CENC tillsammans med vissa viktiga branschen spelare. Azure Media Services tillhandahåller stöd för DASH och CENC. Senaste meddelanden finns i följande bloggar:

*  [Vi ber att få presentera Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services lägger till Google Widevine-paketering för att leverera en multi-DRM-dataström](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>Översikt över den här artikeln
Målen för den här artikeln är att:

* Ange en referens design av ett DRM-undersystem som använder CENC med multi-DRM.
* Ange en referensimplementering på Azure/Media Services-plattformen.
* Diskutera vissa design och implementeringslösning avsnitt.

I artikeln täcker termen ”multi-DRM” följande produkter:

* Microsoft PlayReady
* Google Widevines
* Apple FairPlay 

I följande tabell sammanfattas de inbyggda plattform/egen app och webbläsare som stöds av varje DRM.

| **Klientplattform** | **Inbyggda DRM-stöd** | **Browser-appen** | **Strömningsformat** |
| --- | --- | --- | --- |
| **TV-apparater, operatorn digitalboxar, OTT digitalboxar för smartkort** |PlayReady främst och/eller Widevine och/eller andra |Linux, Opera, WebKit, andra |Olika format |
| **Windows 10-enheter (Windows-dator, Windows-surfplattor, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>Universella Windows-plattformen |STRECK (för HLS, PlayReady stöds inte)<br/><br/>DASH, Smooth Streaming (HLS, PlayReady finns inte stöd för) |
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-klienter och Apple TV** |FairPlay |Safari 8 +/ EME |HLS |


Med tanke på det aktuella tillståndet för varje DRM-distribution, en tjänst vanligtvis vill implementera två eller tre DRMs att se till att du kan hantera alla typer av slutpunkter på bästa sätt.

Det finns en kompromiss mellan komplex logik för tjänsten och komplexitet på klientsidan för att nå en viss nivå av användarupplevelsen på olika klienter.

Tänk på att du vill göra:

* PlayReady implementeras internt i alla Windows-enhet på en Android-enheter och tillgänglig via programvara SDK: er på nästan alla plattformar.
* Widevine implementeras internt i alla Android-enhet, Chrome och vissa andra enheter.
* FairPlay finns bara på iOS och Mac OS x-klienter eller via iTunes.

Det finns två alternativ för en typisk multi-DRM:

* PlayReady och Widevine
* PlayReady och Widevine FairPlay

## <a name="a-reference-design"></a>En referens-design
Det här avsnittet innehåller en referens-design som är oberoende av de tekniker som används för att implementera den.

DRM-undersystemet kan innehålla följande komponenter:

* Hantering av nycklar
* DRM-paketering
* DRM-licensleverans
* Rätt kontroll
* Autentisering/auktorisering
* Spelare
* Ursprung/content delivery network (CDN)

Följande diagram illustrerar övergripande interaktionen mellan komponenter i ett DRM-undersystem:

![DRM-undersystemet med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Designen har tre grundläggande lager:

* Ett back office-lager (svart) visas inte externt.
* Ett lager för DMZ (mörkt blå) innehåller alla slutpunkter mot allmänheten.
* Ett offentligt internet-lager (lätta blå) innehåller CDN och spelare med trafik över det offentliga internet.

Det bör också finnas ett innehåll hanteringsverktyg för att styra DRM-skydd, oavsett om den är statisk eller dynamisk kryptering. Indata för DRM-kryptering är:

* MBR videoinnehåll
* Innehållsnyckel
* Licens förvärv URL: er

Här är det övergripande flödet under uppspelningstid:

* Användaren autentiseras.
* En autentiseringstoken har skapats för användaren.
* DRM-skyddat innehåll (manifestet) laddas ned till spelaren.
* Windows Media player skickar en begäran för anskaffning av licens till servrar för fjärrskrivbordslicenser tillsammans med en nyckel-ID och en autentiseringstoken.

Följande avsnitt beskriver utformningen av nyckelhantering.

| **ContentKey till tillgångsinformation** | **Scenario** |
| --- | --- |
| 1-till-1 |Det enklaste fallet. Den här kontrollen finest. Men leder detta till vanligtvis den högsta licens leverans kostnaden. Åtminstone krävs en licensbegäran för för varje skyddad tillgång. |
| 1-till-många |Du kan använda samma innehållsnyckeln för flera tillgångar. Till exempel för alla tillgångar i en logisk grupp, kan till exempel en kategori eller en delmängd av en kategori (eller film Gen) du använda en enda innehållsnyckel. |
| Många-till-1 |Nycklar för multiinnehåll krävs för varje tillgång. <br/><br/>Om du behöver tillämpa skydd för dynamiska CENC med multi-DRM för MPEG DASH och dynamisk AES-128-kryptering för HLS måste till exempel två separata innehåll nycklar. Varje innehållsnyckeln måste sin egen ContentKeyType. (Använd ContentKeyType.CommonEncryption för innehållsnyckeln används för dynamiska CENC skydd. För innehållsnyckeln används för dynamiska AES-128-kryptering, använder du ContentKeyType.EnvelopeEncryption.)<br/><br/>Ett annat exempel är kan CENC skydda DASH innehåll, i teorin, du använda en innehållsnyckel för att skydda video-ström och en annan innehållsnyckeln att skydda ljudströmmen. |
| Många-till-många |Kombinationen av de föregående två scenarierna. En uppsättning av innehåll nycklar används för var och en av flera resurser i tillgångsgruppen samma. |

En annan viktig faktor är användning av beständiga och Uppdateringsvärdet licenser.

Varför är detta viktigt?

Om du använder ett offentligt moln för licensleverans av har beständiga och Uppdateringsvärdet licenser en direkt inverkan på licens leverans kostnaden. Följande två olika design-fall används för att illustrera:

* Månatlig prenumeration: Använd en beständig licens och 1-till-många innehåll nyckeln till tillgången mappning. Vi kan till exempel använda en enda innehållsnyckel för kryptering för alla barnens filmer. I det här fallet:

    Totalt antal licenser som krävs för alla barn filmer/enhet = 1

* Månatlig prenumeration: använder en Uppdateringsvärdet licens och 1-till-1-mappning mellan innehållsnyckeln och tillgångshantering. I det här fallet:

    Totalt antal licenser som krävs för alla barn filmer/enhet = [antal filmer bevakade] x [antal sessioner]

De två olika konstruktionerna resultera i mycket olika licens begäran mönster. Olika mönster resultera i olika licensleverans om licensleveranstjänst tillhandahålls av ett offentligt moln, till exempel Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mappa design till teknik för implementering
Därefter är generiska designen mappad till tekniker på plattformen Azure/Media Services genom att ange vilken teknik som ska användas för varje byggblock.

Följande tabell visar mappningen.

| **Byggblock** | **Teknik** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitetsprovider (IDP)** |Azure Active Directory (AD Azure) |
| **Säkerhetstokentjänst (STS)** |Azure AD |
| **Arbetsflöde för DRM-skydd** |Media Services dynamisk skydd |
| **DRM-licensleverans** |* Media Services licensleverans (PlayReady Widevine, FairPlay) <br/>* Axinom licensserver <br/>* Anpassade PlayReady-licensserver |
| **ursprung** |Media Services strömmande slutpunkt |
| **Nyckelhantering** |Behövs inte för referensimplementering |
| **Innehållshantering** |Ett C#-konsolprogram |

Med andra ord används både IDP och STS med Azure AD. Den [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/) används för Windows Media player. Både Media Services och Media Player stöder DASH och CENC med multi-DRM.

Följande diagram visar den övergripande struktur och flöde med föregående teknik mappningen:

![CENC på Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om du vill konfigurera dynamisk CENC kryptering använder verktyget innehållshantering indata som följande:

* Öppna innehåll
* Innehållsnyckeln från nyckeln generation och hantering
* Licens förvärv URL: er
* En lista med information från Azure AD

Här är resultatet av verktyget innehållshantering:

* ContentKeyAuthorizationPolicy innehåller specifikation på hur licensleverans verifierar en JSON-Webbtoken (JWT) och DRM-licens specifikationer.
* AssetDeliveryPolicy innehåller specifikationer direktuppspelning format, DRM-skydd och licens förvärv URL: er.

Här är flödet under körning:

* Vid autentisering av användare skapas en JWT.
* Ett anspråk som ingår i JWT är ett anspråk i grupper som innehåller gruppobjekt-ID EntitledUserGroup. Denna begäran används för att skicka rätt kontrollen.
* Windows Media player hämtar klienten manifestet för CENC-skyddat innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är CENC skyddas.
   * Licens förvärv URL: er.
* Windows Media player begär licens förvärv baserat på webbläsaren/DRM som stöds. I licens förvärv begäran, nyckeln ID och JWT skickas också. Licensleveranstjänst verifierar JWT och anspråk finns innan den utfärdar licensen som krävs.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Procedurer för implementering
Implementeringen innehåller följande steg:

1. Förbered test tillgångar. Koda/package test video till flera bithastigheter fragmenterad MP4 i Media Services. Den här tillgången är *inte* DRM-skyddat. DRM skydd görs av dynamisk protection senare.

2. Skapa en nyckel-ID och en innehållsnyckel (du kan också från en nyckel startvärde). I den här instansen nyckelhanteringssystemet inte behövs eftersom endast en enskild nyckel-ID och innehållsnyckeln krävs för några av test tillgångar.

3. Använd Media Services API för att konfigurera multi-DRM-licensleveranstjänster för test tillgången. Om du använder anpassade licensservrar av ditt företag eller företagets leverantörer i stället för licens-tjänster i Media Services kan du hoppa över det här steget. Du kan ange licens förvärv URL: er i steg när du konfigurerar licensleverans. Media Services API behövs för att ange vissa detaljerad konfigurationer, till exempel auktorisering principbegränsning och licens svar mallar för olika tjänster för DRM-licens. För tillfället tillhandahåller inte Azure-portalen nödvändiga Användargränssnittet för den här konfigurationen. Information om API-nivå och exempelkod finns [Använd PlayReady och/eller Widevine dynamic vanliga kryptering](media-services-protect-with-playready-widevine.md).

4. Använd Media Services API för att konfigurera tillgångsleveransprincip för test tillgången. Information om API-nivå och exempelkod finns [Använd PlayReady och/eller Widevine dynamic vanliga kryptering](media-services-protect-with-playready-widevine.md).

5. Skapa och konfigurera en Azure AD-klient i Azure.

6. Skapa några användarkonton och grupper i Azure AD-klienten. Skapa minst en ”rätt” användargrupp och lägga till en användare i den här gruppen. Användare i den här gruppen klarade rätt i licenser. Användarna inte i den här gruppen inte klarade autentisering och kan inte hämta en licens. Medlemskap i gruppen ”rätt användare” är ett obligatoriskt grupper anspråk i JWT som utfärdats av Azure AD. Du kan ange detta anspråk i steg när du konfigurerar multi-DRM-licensleveranstjänster.

7. Skapa en ASP.NET MVC-app som värd för din videospelare. Den här appen i ASP.NET är skyddad med användarautentisering mot Azure AD-klient. Rätt anspråk ingår i åtkomsttoken som erhålls efter autentisering av användare. Vi rekommenderar OpenID Connect API för det här steget. Installera följande NuGet-paket:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Skapa en spelare med hjälp av den [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Använd den [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) att ange vilka DRM-teknik som ska användas på olika DRM-plattformar.

9. I följande tabell visas testningsmatris.

    | **DRM** | **Webbläsare** | **Resultat för rätt användare** | **Resultat för unentitled användare** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge eller Internet Explorer 11 på Windows 10 |Lyckades |Misslyckades |
    | **Widevine** |Chrome på Windows 10 |Lyckades |Misslyckades |
    | **FairPlay** |TBD | | |

Information om hur du konfigurerar Azure AD för en ASP.NET MVC player app finns [integrera en app med Azure Media Services OWIN MVC-baserade med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Mer information finns i [JWT-token autentisering i Azure Media Services och dynamisk kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Information om Azure AD:

* Du kan hitta information för utvecklare i den [Utvecklarhandbok för Azure Active Directory](../../active-directory/active-directory-developers-guide.md).
* Du kan hitta information för administratörer i [administrera Azure AD-klientkatalogen](../../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Vissa problem i implementering
Använd följande felsökningsinformation för hjälp med implementeringsproblem med.

* Utfärdaren URL måste avslutas med ”/”. Målgruppen måste vara player programmet klient-ID. Lägg även till ”/” i slutet av utfärdar-URL.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    I den [JWT avkodarens](http://jwt.calebb.net/), visas **eller** och **iss**, enligt JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter till programmet i Azure AD på den **konfigurera** för programmet. Behörigheter som krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Använd rätt utfärdaren när du ställer in skydd för dynamiska CENC.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Följande fungerar inte:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID är Azure AD-klient-ID. GUID kan hittas i den **slutpunkter** popup-menyn i Azure-portalen.

* Bevilja gruppmedlemskap anspråk privilegier. Kontrollera att följande finns i Azure AD application manifest-filen: 

    ”groupMembershipClaims”: ”alla” (standardvärdet är null)

* Ange rätt TokenType när du skapar begränsning krav.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Eftersom du lägger till stöd för JWT (Azure AD) utöver SWT (ACS) är standard TokenType TokenType.JWT. Om du använder SWT/ACS måste du ange token till TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Ytterligare artiklar om implementering
Det här avsnittet beskrivs vissa ytterligare avsnitt i designen och implementeringen.

### <a name="http-or-https"></a>HTTP eller HTTPS?
ASP.NET MVC player-program måste ha stöd för följande:

* Autentisering av användare via Azure AD, som är under HTTPS.
* JWT exchange mellan klienten och Azure AD, som är under HTTPS.
* DRM-licenser av klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. PlayReady-produktsvit tilldela inte HTTPS för licensleverans av. Om licensservern PlayReady är utanför Media Services kan använda du antingen HTTP eller HTTPS.

ASP.NET-programmet player använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP är prioriterade för strömning, så du behöver tänka på problemet med blandat innehåll.

* Blandat innehåll tillåts inte i webbläsaren. Men Tillåt att av plugin-program som Silverlight och OSMF plugin-program för jämna och STRECK. Blandat innehåll är en säkerhetsfunktion på grund av risken för att kunna mata in skadlig JavaScript, vilket kan orsaka kundens data ska vara i fara. Webbläsare blockera den här funktionen som standard. Det enda sättet att åtgärda problemet är på serversidan (ursprungliga) genom att låta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte bra antingen.
* Undvik blandat innehåll. Både programmet player och Media Player bör använda HTTP eller HTTPS. När du spelar blandat innehåll kräver silverlightSS teknisk att du avmarkerar en varning om blandat innehåll. FlashSS teknisk hanterar blandat innehåll utan att en varning om blandat innehåll.
* Om din strömmande slutpunkten har skapats innan augusti 2014, kan den inte stöder HTTPS. I det här fallet skapar och använder en ny strömmande slutpunkt för HTTPS.

I referensimplementeringen för DRM-skyddat innehåll, både program- och strömning är under HTTPS. För att öppna innehållet behöver spelaren inte autentisering eller en licens så att du kan använda antingen HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory signering nyckelförnyelse
Registrera nyckelförnyelse är en viktig aspekt att beakta i din implementering. Om du ignorerar det upphör färdiga systemet slutligen att fungera helt, inom sex veckor högst.

Azure AD använder branschstandarder för att upprätta förtroende mellan sig själv och program som använder Azure AD. Mer specifikt använder Azure AD signeringsnyckel som består av en offentlig och privat nyckel. När Azure AD skapar en säkerhetstoken som innehåller information om användaren, är det signerat av Azure AD med en privat nyckel innan den skickas tillbaka till programmet. Verifiera att token är giltig och har sitt ursprung från Azure AD, måste programmet Validera token signatur. Programmet använder den offentliga nyckeln som exponeras av Azure AD som ingår i klientens federation Metadatadokumentet. Den här offentliga nyckeln och signeringsnyckeln varifrån det kommer, är samma som används för alla klienter i Azure AD.

Mer information om Azure AD-nyckelförnyelse finns [viktig information om att signera nyckelförnyelse i Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mellan den [privat-offentligt nyckelpar](https://login.microsoftonline.com/common/discovery/keys/):

* Den privata nyckeln används av Azure AD för att generera en JWT.
* Den offentliga nyckeln används av ett program, till exempel DRM-licensleveranstjänster i Media Services för att verifiera JWT.

Av säkerhetsskäl roterar Azure AD certifikatet regelbundet (var sjätte dag). Vid säkerhetsöverträdelser kan av nyckelförnyelse inträffa varje gång. Därför måste licensleveranstjänster i Media Services du uppdatera den offentliga nyckeln som används som Azure AD roterar nyckelparet. Annars misslyckas token autentiseringen i Media Services och ingen licens har utfärdats.

Om du vill konfigurera den här tjänsten måste ange du TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument när du konfigurerar DRM-licensleveranstjänster.

Här är JWT-flöde:

* Azure AD utfärdar JWT med den aktuella privata nyckeln för en autentiserad användare.
* När en spelare får en CENC med multi-DRM-skyddat innehåll, eftersöks JWT som utfärdats av Azure AD.
* Windows Media player skickar en begäran för anskaffning av licens med JWT till licensleveranstjänster i Media Services.
* Licensleveranstjänster i Media Services använder den aktuella giltiga offentliga nyckeln från Azure AD för att verifiera JWT innan utfärda licenser.

DRM-licensleveranstjänster Kontrollera alltid för aktuella giltig offentlig nyckel från Azure AD. Den offentliga nyckeln som presenteras av Azure AD är den nyckel som används för att verifiera en JWT som utfärdats av Azure AD.

Vad händer om nyckelförnyelse händer när Azure AD genererar en JWT men innan JWT skickas av spelare till DRM-licensleveranstjänster i Media Services för verifiering?

Eftersom en nyckel kan rullas när som helst, är mer än en giltig offentlig nyckel alltid tillgänglig i federation Metadatadokumentet. Media Services licensleverans kan använda någon av nycklar som har angetts i dokumentet. Eftersom en nyckel kan återställas snart, kan en annan vara har ersatts och så vidare.

### <a name="where-is-the-access-token"></a>Där är den åtkomst-token?
Om du tittar på hur en webbapp anropar en API-app under [Programidentitet med OAuth 2.0 klientens autentiseringsuppgifter bevilja](../../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), autentiseringsflödet är följande:

* En användare loggar in på Azure AD i webbprogrammet. Mer information finns i [webbläsare till webbprogrammet](../../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* Azure AD-autentiseringsslutpunkt omdirigerar användaragenten tillbaka till klientprogrammet auktorisering kod. Användaragenten returnerar Auktoriseringskoden med klientprogrammets omdirigerings-URI.
* Webbprogrammet måste få en åtkomst-token så att den kan autentisera till webb-API och hämta en resurs. Den gör en begäran till tokenslutpunkten Azure AD och tillhandahåller autentiseringsuppgifter, klient-ID och API: er webbprogrammet ID URI. Det innehåller Auktoriseringskoden för att bevisa att användaren har godkänt.
* Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
* Via HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken att lägga till JWT-sträng med en beteckning ”ägar” i ”tillstånd”-huvudet i begäran i webb-API. Webb-API verifierar sedan av JWT. Om verifieringen lyckas, returneras en resurs.

I det här programmet identitet flödet förtroende webb-API för att webbprogrammet autentiserade användaren. Därför kan kallas det här mönstret betrodda undersystemet. Den [Flödesdiagram för auktorisering](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) beskriver hur authorization-kod-grant flödet fungerar.

Licenser med tokenbegränsningar följer samma mönster för betrodda undersystemet. Licensleveranstjänst i Media Services är web API-resurs, eller ”backend-resursen” som ett program som behöver komma åt. Där är därför den åtkomst-token?

Åtkomst-token hämtas från Azure AD. Efter autentiseringen av användaren returneras ett Auktoriseringskoden. Auktoriseringskoden sedan används tillsammans med klient-ID och nyckeln app till exchange-åtkomst-token. Åtkomst-token används för åtkomst till ett ”pekaren”-program som pekar på eller representerar Media Services licensleveranstjänst.

För att registrera och konfigurera pekaren-app i Azure AD, gör du följande:

1. I Azure AD-klient:

   * Lägg till ett program (resurs) med URL-https://[resource_name].azurewebsites.net/ inloggning. 
   * Lägg till ett app-ID med URL-https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Lägg till en ny nyckel för resurs-app.

3. Uppdatera manifestfilen app så att groupMembershipClaims-egenskap har värdet ”groupMembershipClaims”: ”alla”.

4. I Azure AD-app som pekar på player webbapp i avsnittet **behörigheter för andra program**, Lägg till resurs-app som har lagts till i steg 1. Under **delegerad behörighet**väljer **åtkomst [resource_name]**. Det här alternativet ger web app behörighet att skapa åtkomsttoken som har åtkomst till resursen appen. Gör detta för både lokala och distribuerade versionen av webbprogrammet om du utvecklar med Visual Studio och Azure webbapp.

JWT som utfärdats av Azure AD är den åtkomst-token som används för åtkomst till resursen pekaren.

### <a name="what-about-live-streaming"></a>Vad händer om direktsänd strömning?
Föregående diskussion fokuserar på att på begäran tillgångar. Vad händer om direktsänd strömning?

Du kan använda exakt samma designen och implementeringen för att skydda direktsänd strömning i Media Services genom att behandla tillgången som är associerad med ett program som en VOD tillgång.

Mer specifikt för liveströmning i Media Services, måste du skapa en kanal och sedan skapa ett program i kanalen. För att skapa programmet, måste du skapa en tillgång som innehåller live arkivet för programmet. För att ge CENC med multi-DRM-skydd med levande innehåll, gäller samma installationen/bearbetningen tillgången som om det vore en VOD tillgång innan programmet startas.

### <a name="what-about-license-servers-outside-media-services"></a>Nyheter om licensservrar utanför Media Services?
Ofta investerat kunder i en servergrupp licens antingen i sina egna Datacenter eller en värdbaserad av DRM-leverantörer. Med Media Services innehållsskydd, kan du arbeta hybrid-läge. Innehållet kan vara värd och dynamiskt skyddas i Media Services medan DRM-licenser levereras av utanför Media Services-servrar. I det här fallet, Överväg följande ändringar:

* STS måste token som accepteras och kan verifieras av licens-servergruppen. Widevine-licens-servrar som tillhandahålls av Axinom kan till exempel kräva en specifik JWT som innehåller rätt meddelanden. Därför måste ha en STS att utfärda sådana JWT. För information om den här typen av implementering, gå till den [Azure Documentation Center](https://azure.microsoft.com/documentation/) och se [Använd Axinom att leverera Widevine-licenser till Azure Media Services](media-services-axinom-integration.md).
* Du behöver inte längre konfigurera licensleveranstjänst (ContentKeyAuthorizationPolicy) i Media Services. Du måste ange licensen förvärv URL: er (för PlayReady och Widevine FairPlay) när du konfigurerar AssetDeliveryPolicy att ställa in CENC med multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Vad händer om jag vill använda en anpassad STS?
En kund kan välja att använda en anpassad STS för JWTs. Anledningar:

* IDP används av kunden stöder inte STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöva mer flexibla strängare kontrollen eller integrera STS med kundens prenumeranten faktureringssystem. Exempelvis kan en MVPD operator erbjuder flera OTT prenumeranten paket, till exempel premium och basic, och sport. Operatorn kanske vill matchar anspråk i en token med en prenumerant paketet så att bara innehållet i ett visst paket blir tillgängliga. I det här fallet ger en anpassad STS nödvändiga flexibilitet och kontroll.

När du använder en anpassad STS måste två ändringar göras:

* När du konfigurerar licensleveranstjänst för en tillgång som du behöver ange säkerhetsnyckeln används för verifiering av anpassade STS i stället för den aktuella nyckeln från Azure AD. (Mer information Följ.) 
* När en JTW-token genereras en säkerhetsnyckel har angetts i stället för den privata nyckeln för den aktuella X509 certifikat i Azure AD.

Det finns två typer av säkerhetsnycklar:

* Symmetrisk nyckel: samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: ett privat-offentligt nyckelpar X509 certifikatet används med en privat nyckel för att kryptera/Generera en JWT och den offentliga nyckeln för att verifiera token.

> [!NOTE]
> Om du använder .NET Framework / C# som din plattformar kan X509 certifikat som används för en asymmetrisk säkerhetsnyckeln måste ha en nyckellängd på minst 2048. Detta är ett krav i klassen System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework. I annat fall returneras följande undantag:

> IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey för signering får inte vara mindre än '2048-bitar.

## <a name="the-completed-system-and-test"></a>Slutförda system och testning
Det här avsnittet vägleder dig genom följande scenarier i slutförda slutpunkt till slutpunkt-systemet så att du kan ha en grundläggande bild av problemet innan du skaffa ett konto för inloggning:

* Om du behöver ett icke-integrerade scenario:

    * För video tillgångar i Media Services som är antingen oskyddade eller DRM skyddade men utan token autentisering (utfärdar en licens till den som begärt det), kan du testa den utan att logga in. Växla till HTTP om din direktuppspelning av video via HTTP.

* Om du behöver en inbyggd slutpunkt till slutpunkt-scenario:

    * Du måste logga in för video tillgångar under dynamisk DRM-skydd i Media Services med token autentiserings- och JWT som genereras av Azure AD.

Webbprogrammet player och dess inloggning finns i [webbplatsen](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Användarinloggning
För att testa det integrerade DRM-systemet för slutpunkt till slutpunkt som du behöver ha ett konto som har skapats eller lagts till.

Vilket konto?

Även om Azure ursprungligen åtkomst endast av Microsoft-kontoanvändare, stöds åtkomst nu av användare från båda systemen. Alla Azure egenskaper nu litar Azure AD för autentisering och Azure AD autentiserar användare i organisationer. En federationsrelation skapades där Azure AD litar på Microsoft-konto-konsumentidentitetssystemet för autentisering av konsumentanvändare. Azure AD kan därmed autentisera gästkonton Microsoft-konton samt enligt intern Azure AD.

Eftersom Azure AD litar på domänen för Microsoft-konto, kan du lägga till alla konton från någon av följande domäner med anpassad Azure AD-klient och använda konto för att logga in:

| **Domännamn** | **Domän** |
| --- | --- |
| **Anpassad Azure AD-klient domän** |somename.onmicrosoft.com |
| **Företagets domän** |Microsoft.com |
| **Domänen för Microsoft-konto** |Outlook.com, live.com, hotmail.com |

Du kan kontakta någon av författarna måste ha ett konto som har skapats eller lagts till för dig.

Följande skärmdumpar visar olika inloggningssidor används av olika domänkonton:

**Anpassad Azure AD-klient domänkonto**: den anpassade inloggningssidan anpassad Azure AD-klient domän.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft domänkonto med smartkort**: inloggningssidan anpassade genom Microsoft företagets IT med tvåfaktorsautentisering.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: inloggningssida för Microsoft-kontot för konsumenterna.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använda krypterade Media-tilläggen för PlayReady
På en modern webbläsare med krypterade (EME Media tillägg) för PlayReady-support, till exempel Internet Explorer 11 på Windows 8.1 eller senare och Microsoft Edge-webbläsaren på Windows 10 är PlayReady underliggande DRM för EME.

![Använd EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Området mörkt player beror PlayReady protection hindrar dig från att göra en skärmdump av skyddade video.

Följande skärmbild visar player plugin-program och Microsoft Security Essentials (mus) / EME stöd:

![Player plugin-program för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 på Windows 10 kan [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) anropas på Windows 10-enheter som stöder den. PlayReady SL3000 låser upp flödet av förbättrade premium-innehåll (4K HDR) och nytt innehåll leveransmodell som helst (för förbättrat innehåll).

Om du vill fokusera på de Windows-enheterna är PlayReady endast DRM i maskinvaran som är tillgängliga på Windows-enheter (PlayReady SL3000). En strömmande tjänst kan använda PlayReady via EME eller via en Uwp-program och ger en högre bildkvaliteten genom att använda PlayReady SL3000 än en annan DRM. Normalt innehåll upp till 2 kB flödar genom Chrome eller Firefox, och upp till 4K-flöden via Microsoft Edge/Internet Explorer 11 eller en Uwp-program på samma enhet. Hur mycket beror på implementering och inställningar.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine
På en modern webbläsare med EME/Widevine-support, till exempel Chrome 41 + på Windows 10, Windows 8.1-, Mac OS x Yosemite och Chrome för Android 4.4.4 är Google Widevine DRM bakom EME.

![Använd EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine hindra inte dig från att göra en skärmdump av skyddade video.

![Player plugin-program för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled användare
Om en användare inte är medlem i gruppen ”rätt-användare kan klarade användaren inte rätt. Licenstjänsten multi-DRM sedan vägrar att utfärda begärda licensen som visas. Detaljerad beskrivning är ”licens hämta misslyckades”, vilket är fungerar.

![Unentitled användare](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Kör en anpassad säkerhetstokentjänst
Om du kör en anpassad STS utfärdas av JWT av anpassade STS med hjälp av en symmetrisk eller en asymmetrisk nyckel.

Följande skärmbild visas ett scenario som använder en symmetrisk nyckel (med hjälp av Chrome):

![Anpassade STS med en symmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Följande skärmbild visas ett scenario som använder en asymmetrisk nyckel via X509 certifikat (med en modern webbläsare Microsoft):

![Anpassade STS med en asymmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I båda de föregående fall förblir användarautentisering oförändrat. Det sker via Azure AD. Den enda skillnaden är att JWTs har utfärdats av anpassade STS i stället för Azure AD. När du konfigurerar skydd för dynamiska CENC anger Licensbegränsning leverans av tjänsten hur JWT en symmetrisk eller en asymmetrisk nyckel.

## <a name="summary"></a>Sammanfattning
Det här dokumentet beskrivs CENC med flera interna DRM och åtkomstkontroll via tokenautentisering, sin design och dess implementering med Azure Media Services och Media Player.

* En referens design angavs som innehåller alla nödvändiga komponenter i ett DRM/CENC undersystem.
* En referensimplementering angavs på Azure Media Services och Media Player.
* Vissa avsnitt som är direkt inblandade i designen och implementeringen har också beskrivs.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
