---
title: "CENC med Multi-DRM och Access Control: en referens Design och Implementeringslösning på Azure och Azure Media Services | Microsoft Docs"
description: "Mer information om hur för Microsoft® Smooth Streaming klienten portera Kit."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 730917b6859f8dbd800ef2cb141062f45d7779ac
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC med Multi-DRM och åtkomstkontroll: En referensdesign och implementering i Azure och Azure Media Services
 
## <a name="introduction"></a>Introduktion
Det är känt att det är komplicerat att utforma och skapa ett DRM-undersystem för en OTT eller online strömning lösning. Och det är vanligt för operatörer online video leverantörer att flytta ut den här delen specialiserade DRM-leverantörer. Målet med det här dokumentet är att presentera en referens för design och implementering av DRM-undersystemet för slutpunkt till slutpunkt i OTT eller strömmande onlinelösning.

De aktuella läsarna i det här dokumentet är tekniker som arbetar i DRM-undersystemet OTT strömning/flera-screen lösningar online eller alla läsare som är intresserade av DRM-undersystemet. Antas att läsaren känner till minst en av DRM-tekniker på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe åtkomst.

Av DRM inkludera vi också CENC (Common Encryption) med multi-DRM. En större trend i online-strömning och OTT branschen är att använda CENC med flera-native-DRM på olika klientplattformar, vilket är en övergång från föregående trend med en enda DRM och dess klient-SDK för olika klientplattformar. När du använder CENC med flera native DRM både PlayReady och Widevine krypteras enligt den [Common Encryption (ISO/IEC 23001 7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikation.

Fördelarna med CENC med multi-DRM är följande:

1. Minskar kryptering kostnad eftersom en enda kryptering bearbetning används inriktning på olika plattformar med dess ursprungliga DRMs;
2. Minskar kostnaden för att hantera krypterade tillgångar eftersom bara en enda kopia av krypterade tillgångar krävs;
3. Eliminerar DRM klientlicensiering kostnaden eftersom den inbyggda DRM-klienten är vanligtvis ledigt på dess ursprungliga plattform.

Microsoft har en aktiv förslagsställare DASH och CENC tillsammans med vissa viktiga branschen spelare. Microsoft Azure Media Services tillhandahåller i stöd för DASH och CENC. Senaste meddelanden finns Mingfeis bloggar: [meddelar Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/), och [Azure Media Services lägger till Google Widevine-paketering för att leverera Multi-DRM dataströmmen](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Översikt över den här artikeln
Syftet med den här artikeln innehåller följande:

1. Innehåller en referens design av DRM-undersystemet med CENC med multi-DRM;
2. Innehåller en referensimplementering för Microsoft Azure-/ Azure Media Services-plattform.
3. Beskriver vissa design och implementeringslösning avsnitt.

I den här artikeln beskriver ”multi-DRM” följande:

1. Microsoft PlayReady
2. Google Widevines
3. Apple FairPlay 

I följande tabell sammanfattas de inbyggda plattform/egen app och webbläsare som stöds av varje DRM.

| **Klientplattform** | **Intern DRM-Support** | **Browser-appen** | **Strömningsformat** |
| --- | --- | --- | --- |
| **TV-apparater, operatorn digitalboxar, OTT digitalboxar för smartkort** |PlayReady främst och/eller Widevine och/eller andra |Linux, Opera, WebKit, andra |Olika format |
| **Windows 10-enheter (Windows-dator, Windows-surfplattor, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |STRECK (för HLS, PlayReady stöds inte)<br/><br/>DASH, Smooth Streaming (HLS för, PlayReady stöds inte) |
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-klienter och Apple TV** |FairPlay |Safari 8 +/ EME |HLS |


Med tanke på det aktuella tillståndet för varje DRM-distribution, en tjänst kan du implementera 2 eller 3 DRMs att se till att du kan hantera alla typer av slutpunkter på bästa sätt.

Det finns en kompromiss mellan komplex logik för tjänsten och komplexitet på klientsidan för att nå en viss nivå av användarupplevelsen på olika klienter.

För att göra ditt val, Tänk på dessa uppgifter:

* PlayReady är internt implementerad i alla Windows-enhet på en Android-enheter och tillgänglig via programvara SDK: er på nästan alla plattformar
* Widevine implementeras internt i alla Android-enhet, Chrome och vissa andra enheter
* FairPlay finns bara på iOS och Mac OS x-klienter eller via iTunes.

Detta är en typisk multi-DRM:

* Alternativ 1: PlayReady och Widevine
* Alternativ 2: PlayReady, Widevine och FairPlay

## <a name="a-reference-design"></a>En referens-design
I det här avsnittet presenterar vi en referens-design som är oberoende tekniker som används för att implementera den.

DRM-undersystemet kan innehålla följande komponenter:

1. Hantering av nycklar
2. DRM-paketering
3. DRM-licensleverans
4. Rätt kontroll
5. Autentisering/auktorisering
6. Player
7. Ursprung/CDN

Följande diagram illustrerar hög nivå interaktionen mellan komponenter i ett DRM-undersystem.

![DRM-undersystemet med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Det finns tre grundläggande ”lager” i designen:

1. Säkerhetskopiera office lager (i svart) som inte exponeras externt.
2. ”DMZ” layer (blå) som innehåller alla slutpunkter mot offentlig.
3. Offentliga Internet lager (lätta blå) som innehåller CDN och spelare med trafik över offentligt Internet.

Det bör finnas ett innehållshantering verktyg för att styra DRM-skydd, oavsett är statisk eller dynamisk kryptering. Indata för DRM kryptering bör innehålla:

1. MBR videoinnehåll.
2. Innehållsnyckeln;
3. Licens förvärv URL: er.

Under uppspelningen tiden är hög nivå flödet:

1. Användaren autentiseras;
2. Autentiseringstoken har skapats för användaren.
3. DRM-skyddat innehåll (manifestet) laddas ned till player.
4. Player skickar licens förvärv begäran till servrar för fjärrskrivbordslicenser tillsammans med nyckel-ID och auktorisering token.

Innan du flyttar till nästa avsnitt, några ord om utformningen för hantering av nycklar.

| **Tillgång till ContentKey –** | **Scenario** |
| --- | --- |
| 1-till-1 |Det enklaste fallet. Den här kontrollen finest. Men det vanligtvis resulterar i den högsta licens leverans kostnaden. Begäran måste anges för varje skyddad tillgång på minst en licens. |
| 1-till-många |Du kan använda samma innehållsnyckeln för flera tillgångar. Till exempel för alla tillgångar i en logisk grupp, till exempel en kategori eller en delmängd av kategori (eller film Gen) använda en enda innehållsnyckel. |
| Många-till-1 |Nycklar för multiinnehåll krävs för varje tillgång. <br/><br/>Du måste till exempel två separata innehåll nycklar, var och en med sin egen ContentKeyType om du behöver tillämpa skydd för dynamiska CENC med multi-DRM för MPEG DASH och dynamisk AES-128-kryptering för HLS. (För innehållsnyckeln används för dynamiska CENC skydd ContentKeyType.CommonEncryption bör vara används även för innehållsnyckeln används för dynamiska AES-128-kryptering, ContentKeyType.EnvelopeEncryption ska användas.)<br/><br/>Ett annat exempel är CENC skydda DASH innehåll, i teorin en innehållsnyckel kan användas för att skydda video-ström och en annan innehållsnyckeln att skydda ljudström. |
| Många-till - många |Kombinationen av ovannämnda två scenarier: en uppsättning av innehåll nycklar används för var och en av flera resurser i samma tillgång ”grupp”. |

En annan viktig faktor är användning av beständiga och icke-beständig licenser.

Varför är detta viktigt?

De ha direkt påverkan på kostnaden för leverans av licens om du använder offentliga moln för licensleverans av. Nu ska vi titta på följande två olika design-fall att illustrera:

1. Månatlig prenumeration: använda beständiga licens och 1-till-många innehåll nyckeln till tillgången mappning. T.ex. för alla barnen filmer använder vi en enda innehållsnyckel för kryptering. I det här fallet:

    Totalt antal # licenser som krävs för alla barnen filmer enhet = 1
2. Månatlig prenumeration: använda icke-beständig licens och 1-till-1-mappning mellan innehållsnyckeln och tillgångshantering. I det här fallet:

    Totalt antal # licenser som krävs för alla barnen filmer enhet = [# filmer bevakade] x [# sessioner]

Som du enkelt kan se leda de två olika konstruktionerna till väldigt annorlunda licens begäran därför mönster licensleverans om licensleveranstjänst tillhandahålls av ett offentligt moln, till exempel Azure Media Services.

## <a name="mapping-design-to-technology-for-implementation"></a>Mappa design till teknik för implementering
Därefter mappa vi vårt allmänna design tekniker på Microsoft Azure-/ Azure Media Services-plattformen genom att ange vilken teknik som ska användas för varje byggblock.

Följande tabell visar mappningen:

| **Byggblock** | **Teknik** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitetsprovider (IDP)** |Azure Active Directory |
| **Secure Token Service (STS)** |Azure Active Directory |
| **Arbetsflöde för DRM-skydd** |Azure Media Services dynamisk skydd |
| **DRM-licensleverans** |1. Azure Media Services licensleverans (PlayReady Widevine, FairPlay), eller <br/>2. Axinom licensserver <br/>3. Anpassade PlayReady licensserver |
| **Ursprung** |Azure Media Services strömmande slutpunkt |
| **Hantering av nycklar** |Behövs inte för referensimplementering |
| **Innehållshantering** |Ett C#-konsolprogram |

Med andra ord kommer både identitet Provider (IDP) och Secure säkerhetstokentjänst (STS) att Azure AD. Vi använder för spelare, [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Stöder både Azure Media Services och Azure Media Player DASH och CENC med multi-DRM.

Följande diagram visar den övergripande struktur och flöde med ovan teknik mappningen.

![CENC på AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

För att konfigurera dynamisk CENC kryptering använder verktyget innehållshantering följande indata:

1. Öppna innehåll.
2. Innehållsnyckeln från nyckeln generation och hantering
3. Licens förvärv webbadresserna.
4. En lista med information från Azure AD.

Utdata från verktyget innehållshantering blir:

1. ContentKeyAuthorizationPolicy som innehåller specifikationen på hur licensleverans verifierar en JWT-token och DRM-licens specifikationerna.
2. AssetDeliveryPolicy som innehåller specifikationer direktuppspelning format, DRM-skydd och licens förvärv URL: er.

Under körning, är flödet som nedan:

1. Vid autentisering av användare genereras en JWT-token;
2. En av de anspråk som ingår i JWT-token är ”grupper” anspråk som innehåller gruppen objekt-ID för ”EntitledUserGroup”. Denna begäran används för att skicka ”Kontrollera rätt”.
3. Player hämtningar klienten manifestet för en CENC skyddat innehåll och ”ser” följande:

   1. nyckel-ID
   2. innehållet är skyddade, CENC
   3. Licens förvärv URL: er.
4. Player begär licens förvärv baserat på webbläsaren/DRM som stöds. Nyckeln ID i dess licens förvärv och JWT-token ska skickas. Licensleveranstjänst kontrollerar JWT-token och anspråk finns innan licensen som krävs.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Procedurer för implementering
Implementeringen omfattar följande steg:

1. Förbereda test tillgångarna: koda/package ett test videoklipp i flera bithastigheter fragmenterad MP4 i Azure Media Services. Den här tillgången är inte DRM-skyddat. DRM skydd kommer att göras av dynamisk protection senare.
2. Skapa nyckel-ID och innehåll nyckel (eventuellt från viktiga startvärde). Exemplet nyckelhanteringssystem inte behövs eftersom vi arbetar med en uppsättning med ID: T och nyckeln till ett par test tillgångar.
3. Använd AMS API för att konfigurera multi-DRM-licensleveranstjänster för test tillgången. Om du använder anpassade licensservrar av ditt företag eller företagets leverantörer i stället för licens-tjänster i Azure Media Services kan du hoppa över detta steg och ange licens förvärv URL: er i steg för att konfigurera licens. AMS API behövs för att ange detaljerad konfigurationer, till exempel auktorisering principbegränsning, licens svar mallar för olika tjänster för DRM-licens, osv. Just nu är innehåller Azure-portalen ännu inte nödvändiga Användargränssnittet för den här konfigurationen. Du kan hitta API-nivå info och exempelkod i Julia Kornich dokument: [med PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-drm.md).
4. Använda AMS API för att konfigurera tillgångsleveransprincip för test tillgången. Du kan hitta API-nivå info och exempelkod i Julia Kornich dokument: [med PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-drm.md).
5. Skapa och konfigurera en Azure Active Directory-klient i Azure
6. Skapa några användarkonton och grupper i Azure Active Directory-klient: du bör skapa minst ”EntitledUser” och lägga till en användare till den här gruppen. Användare i den här gruppen ska skicka rätt kontroll i licenser och användare inte i den här gruppen kommer inte att skicka autentiseringskontroll och kommer inte att kunna hämta någon licens. Vara medlem i gruppen ”EntitledUser” är ett obligatoriskt ”grupper” anspråk i JWT-token som utfärdas av Azure AD. Detta anspråk krav anges när du konfigurerar multi-DRM-licens leverans services steg.
7. Skapa en ASP.NET MVC-app som ska vara värd för din videospelare. ASP.NET-app skyddas med användarautentisering mot Azure Active Directory-klient. Rätt anspråk ska inkluderas i åtkomsttoken som erhålls efter autentisering av användare. OpenID Connect API rekommenderas för det här steget. Du måste installera följande NuGet-paket:
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. Skapa en Windows Media player [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) kan du ange vilka DRM-teknik som ska användas på olika DRM-plattformen.
9. Testningsmatris:

| **DRM** | **Webbläsare** | **Resultat för rätt användare** | **Resultat för icke rätt användare** |
| --- | --- | --- | --- |
| **PlayReady** |MS kant eller IE11 på Windows 10 |Lyckas |Misslyckas |
| **Widevine** |Chrome på Windows 10 |Lyckas |Misslyckas |
| **FairPlay** |TBD | | |

George Trifonov för Azure Media Services-teamet har skrivit en blogg som ger detaljerade anvisningar för att konfigurera Azure Active Directory för en ASP.NET MVC player app: [integrera Azure Media Services OWIN MVC baserat app med Azure Active Directory och begränsa innehåll viktiga leverans baserat på JWT anspråk](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George har skapat en blogg på [JWT-token autentisering i Azure Media Services och dynamisk kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Och här är operativsystemets [på Azure AD-integrering med Azure Media Services viktiga leverans](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Information om Azure Active Directory:

* Du kan hitta information för utvecklare i [Utvecklarhandbok för Azure Active Directory](../active-directory/active-directory-developers-guide.md).
* Du kan hitta information för administratörer i [administrera din Azure AD-katalog](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Vissa saker i implementering
Det finns vissa ”saker” i implementeringen. Förhoppningsvis hjälper följande lista över ”saker” dig felsöka om du stöter på problem.

1. **Utfärdaren** URL bör avslutas med **”/”**.  

    **Målgruppen** bör vara player programmet klient-ID och bör du också lägga till **”/”** i slutet av utfärdar-URL.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    I [JWT avkodarens](http://jwt.calebb.net/), bör du se **eller** och **iss** som nedan i JWT-token:

    ![1 gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. Lägga till behörigheter för programmet i AAD (på fliken Konfigurera av programmet). Detta krävs för varje program (lokala och distribuerade versioner).

    ![2 gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. Använd rätt utfärdaren för inställning av dynamisk CENC skydd:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Följande fungerar inte:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID är AAD-klient-ID. GUID kan hittas i slutpunkter popup-fönster i Azure-portalen.
4. Bevilja gruppmedlemskap anspråk privilegier. Kontrollera att i AAD programmanifestfilen har vi följande

    ”groupMembershipClaims”: ”All” (standardvärdet är null)
5. Ange rätt TokenType när du skapar begränsning krav.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Sedan lägger till stöd för JWT (AAD) utöver SWT (ACS), är standard TokenType TokenType.JWT. Om du använder SWT/ACS måste du ange att TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Ytterligare artiklar om implementering
Vi kommer nästa iden uss vissa ytterligare avsnitt i våra designen och implementeringen.

### <a name="http-or-https"></a>HTTP eller HTTPS?
ASP.NET MVC-player-program som vi byggde måste ha stöd för följande:

1. Autentisering av användare via Azure AD som måste vara under HTTPS;
2. JWT-token exchange mellan klienten och Azure AD som måste vara under HTTPS;
3. DRM-licenser med klienten som måste vara under HTTPS om licensleverans tillhandahålls av Azure Media Services. Naturligtvis tvingade PlayReady produktsvit inte HTTPS för licensleverans av. Om licensservern PlayReady är utanför Azure Media Services kan användas antingen HTTP eller HTTPS.

Player ASP.NET-programmet kommer därför använda HTTPS som bästa praxis. Detta innebär att Azure Media Player på en sida under HTTPS. Men för strömning vi föredrar HTTP måste därför Vi anser blandat innehåll problemet.

1. Blandat innehåll tillåts inte i webbläsaren. Men Tillåt plugin-program som Silverlight och OSMF plugin-program för smooth och STRECK. Blandat innehåll är en säkerhetsfunktion - beror detta på hot om möjligheten att mata in skadlig JS vilket kan orsaka kundinformation ska vara i fara.  Webbläsare blockera det som standard så långt det enda sättet att åtgärda problemet är på servern (ursprungliga), så att alla domäner (oavsett https eller http). Detta är förmodligen inte bra antingen.
2. Vi bör inte blandat innehåll: båda använda HTTP eller båda använder HTTPS. När du spelar blandat innehåll kräver silverlightSS teknisk att du avmarkerar en varning om blandat innehåll. flashSS teknisk hanterar blandat innehåll utan blandat innehåll varning.
3. Om din strömningsslutpunkt skapades innan augusti 2014, stöder inte HTTPS. I det här fallet, skapa och använda en ny strömmande slutpunkt för HTTPS.

I referensimplementeringen för DRM-skyddat innehåll, program och strömning inte under HTTTPS. För att öppna innehållet behöver spelaren inte autentisering eller licens, så att du har frihet att använda HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory signering nyckelförnyelse
Det här är en viktig aspekt att beakta av implementeringen. Om du anser det i din implementering, slutförda systemet så småningom att sluta fungera helt inom högst 6 veckor.

Azure AD använder branschstandarden för att upprätta förtroende mellan sig själv och program med Azure AD. Mer specifikt använder Azure AD signeringsnyckel som består av en offentlig och privat nyckel. När Azure AD skapas en säkerhetstoken som innehåller information om användaren kan är denna token signerat av Azure AD med hjälp av den privata nyckeln innan den skickas tillbaka till programmet. För att verifiera att token är giltig och faktiskt har sitt ursprung från Azure AD, måste programmet Validera token signatur med hjälp av den offentliga nyckeln som exponeras av Azure AD som ingår i klientens federation Metadatadokumentet. Den här offentliga nyckeln – och signeringsnyckeln som härleds – är densamma som används för alla klienter i Azure AD.

Detaljerad information om Azure AD-nyckelförnyelse finns i dokumentet: [viktig Information om nyckeln signering förnyelse i Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Mellan den [privat-offentligt nyckelpar](https://login.microsoftonline.com/common/discovery/keys/),

* Den privata nyckeln används av Azure Active Directory för att skapa en JWT-token;
* Den offentliga nyckeln används av ett program, till exempel DRM-Licensleveranstjänster i AMS för att verifiera JWT-token;

För säkerhet ändamål roterar Azure Active Directory certificate regelbundet (var 6 veckor). Vid säkerhetsintrång, kan nyckelförnyelse inträffa varje gång. Därför måste licensleveranstjänster i AMS du uppdatera den offentliga nyckeln som används som Azure AD roterar nyckelparet, annars misslyckas tokenautentisering i AMS och ingen licens utfärdas.

Detta uppnås genom att ange TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument när du konfigurerar DRM-licensleveranstjänster.

JWT-token flödet är som nedan:

1. Azure AD utfärdar JWT-token med den aktuella privata nyckeln för en autentiserad användare;
2. När en spelare får en CENC med multi-DRM-skyddat innehåll, kommer den först lokalisera JWT-token som utfärdas av Azure AD.
3. Windows Media player skickar licens förvärv begäran med JWT-token till licensleveranstjänster i AMS;
4. Licensleveranstjänster i AMS använder aktuella giltig offentlig nyckel från Azure AD för att verifiera JWT-token innan utfärda licenser.

DRM-licensleveranstjänster kommer alltid kontroll av den aktuella giltig offentlig nyckeln från Azure AD. Den offentliga nyckeln som presenteras av Azure AD blir den nyckel som används för att verifiera en JWT-token som utfärdas av Azure AD.

Vad händer om nyckelförnyelse händer efter AAD genererar en JWT-token, men innan JWT token skickas av spelare till DRM licensleveranstjänster i AMS för verifiering?

Eftersom en nyckel kan återställas när som helst, finns det alltid mer än en giltig offentlig nyckel i federation Metadatadokumentet. Leverans av Azure Media Services-licens kan använda någon av nycklar som har angetts i dokumentet, eftersom en nyckel kan återställas snart, en annan kan dess ersättare, och så vidare.

### <a name="where-is-the-access-token"></a>Var finns åtkomst-Token?
Om du tittar på hur en webbapp anropar en API-app under [Programidentitet med OAuth 2.0 klientens autentiseringsuppgifter Grant](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), autentiseringsflödet är som nedan:

1. En användare är inloggad i Azure AD i webbprogrammet (finns i [webbläsare till webbprogrammet](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
2. Azure AD-autentiseringsslutpunkt omdirigerar användaragenten tillbaka till klientprogrammet auktorisering kod. Användaragenten returnerar auktoriseringskod med klientprogrammets omdirigerings-URI.
3. Webbprogrammet måste få en åtkomst-token så att den kan autentisera till webb-API och hämta en resurs. Den gör en begäran till tokenslutpunkten för Azure AD, att tillhandahålla autentiseringsuppgifter, klient-ID och API: er webbprogrammet ID URI. Det innehåller Auktoriseringskoden för att bevisa att användaren har godkänt.
4. Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
5. Via HTTPS används webbprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud i begäran webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.

I det här flödet ”Programidentitet” förtroende webb-API för att webbprogrammet autentiserade användaren. Därför kan kallas det här mönstret betrodda undersystemet. Den [diagram på den här sidan](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) beskriver hur auktoriseringskod bevilja flödet fungerar.

I licenser med tokenbegränsningar följer vi samma mönster betrodda undersystemet. Licensleveranstjänst i Azure Media Services är web API-resurs, ”backend resursen” ett webbprogram som behöver åtkomst till. Där är därför den åtkomst-token?

Faktiskt får vi åtkomst-token från Azure AD. Efter lyckad användarautentisering returneras auktoriseringskod. Auktoriseringskoden sedan används tillsammans med klient-ID och app nyckeln till exchange-åtkomst-token. Och åtkomst-token för åtkomst till en ”pekaren” programmet pekar eller som representerar Azure Media Services licensleveranstjänst.

Vi behöver registrera och konfigurera ”pekaren”-app i Azure AD genom att följa stegen nedan:

1. I Azure AD-klient

   * Lägg till ett program (resurs) med inloggnings-URL:

   https://[resource_name].azurewebsites.NET/ och

   * App-ID-URL:

   https://[aad_tenant_name].onmicrosoft.com/[resource_name];
2. Lägg till en ny nyckel för resursen appen;
3. Uppdatera manifestfilen app så att egenskapen groupMembershipClaims har följande värde: ”groupMembershipClaims”: ”alla”  
4. Lägg till resurs-app som har lagts till i steg 1 ovan i avsnittet ”behörigheter för andra program” i Azure AD app pekar till player-webbprogram. Kontrollera ”åtkomst [resource_name]” markering under ”delegerad behörighet”. Detta ger web app behörighet att skapa åtkomst-token för åtkomst till resursen appen. Du bör göra detta för både lokala och distribuerade versionen av webbprogrammet om du utvecklar med Visual Studio och Azure webbapp.

Detta JWT-token som utfärdas av Azure AD är därför verkligen åtkomst-token för åtkomst till resursen ”pekaren”.

### <a name="what-about-live-streaming"></a>Vad händer om Live Streaming?
I ovanstående har vår fokus på tillgångar på begäran. Vad händer om direktsänd strömning?

Goda nyheter är att du kan använda exakt samma designen och implementeringen för att skydda direktsänd strömning i Azure Media Services genom att behandla tillgången som är associerad med ett program som en ”VOD tillgång”.

Det är särskilt välkända att om du vill göra direktsänd strömning i Azure Media Services måste du skapa en kanal och sedan ett program under kanalen. För att skapa programmet, måste du skapa en tillgång som innehåller live arkivet för programmet. För att ge CENC med multi-DRM-skydd med levande innehåll du behöver göra är att tillämpa samma inställningar/bearbetningen till tillgången som om det var en ”VOD tillgång” innan du startar programmet.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Nyheter om licensservrar utanför Azure Media Services?
Kunder kan ofta har investerat i licens servergruppen antingen i sina egna data center eller värdbaserad av DRM-leverantörer. Lyckligtvis innehållsskydd för Azure Media Services kan du använda i hybrid läge: innehållet finns och dynamiskt skyddas i Azure Media Services medan DRM-licenser levereras av utanför Azure Media Services-servrar. I det här fallet finns följande ändringar:

1. Secure Token Service måste token som accepteras och som kan verifieras av licens-servergruppen. Widevine-licens-servrar som tillhandahålls av Axinom kräver till exempel en JWT-token som innehåller ”rätt meddelandet”. Du måste därför ha en STS att utfärda sådana JWT-token. Författarna har slutfört en sådan implementering och du kan hitta information i följande dokument i [Azure Documentation Center](https://azure.microsoft.com/documentation/): [med Axinom att leverera Widevine-licenser till Azure Media Services](media-services-axinom-integration.md).
2. Du behöver inte längre konfigurera licensleveranstjänst (ContentKeyAuthorizationPolicy) i Azure Media Services. Vad du behöver göra är att tillhandahålla licensen förvärv URL: er (för PlayReady Widevine och FairPlay) när du konfigurerar AssetDeliveryPolicy ställer in CENC med multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Vad händer om jag vill använda en anpassad STS?
Det kan finnas flera skäl som en kund kan välja att använda en anpassad STS (Secure Token Service) för att tillhandahålla JWT-tokens. Vissa av dessa är:

1. Identitet Provider (IDP) används av kunden stöder inte STS. I det här fallet kan en anpassad STS vara ett alternativ.
2. Kunden måste kanske flexibla eller strängare kontroll i integrera STS med kundens prenumeranten faktureringssystem. Operatör MVPD kan exempelvis erbjuda flera OTT prenumeranten paket, till exempel premium och basic, sport, osv. Operatorn vilja matchar anspråk i en token med en prenumerant paketet så att endast innehållet i det högra paketet görs tillgänglig. I det här fallet ger en anpassad STS nödvändiga flexibilitet och kontroll.

Två förändringar som måste göras när du använder en anpassad STS:

1. När du konfigurerar licensleveranstjänst för en tillgång som du behöver ange säkerhetsnyckeln används för verifiering av anpassade STS (Mer information finns nedan) i stället för den aktuella nyckeln från Azure Active Directory.
2. När en JTW-token genereras en säkerhetsnyckel har angetts i stället för den privata nyckeln för den aktuella X509 certifikat i Azure Active Directory.

Det finns två typer av säkerhetsnycklar:

1. Symmetrisk nyckel: samma nyckel används för både generera och verifiera en JWT-token;
2. Asymmetrisk nyckel: ett privat-offentligt nyckelpar i X509 används certifikat med privat nyckel för att kryptera/Generera en JWT-token och den offentliga nyckeln för att verifiera token.

#### <a name="tech-note"></a>Teknisk kommentar
Om du använder .NET Framework / C# som din plattformar kan X509 certifikat som används för asymmetrisk säkerhetsnyckeln måste ha en nyckellängd på minst 2048. Detta är ett krav i klassen System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework. I annat fall genereras följande undantagsfel:

IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey för signering får inte vara mindre än '2048-bitar.

## <a name="the-completed-system-and-test"></a>Slutförda system och testning
Går igenom några scenarier i slutförda slutpunkt till slutpunkt-systemet så att läsare kan ha en grundläggande ”bild” av funktionen innan du hämtar ett inloggningskonto.

Webbprogrammet player och dess inloggningen finns [här](https://openidconnectweb.azurewebsites.net/).

Om vad du behöver är ”icke-integrerade” scenario: video tillgångar finns i Azure Media Services som antingen är oskyddade eller DRM skyddade men utan tokenautentisering (utfärdar en licens till den som begär den), du kan testa den utan inloggning (genom att växla till HTTP om din videoströmning är över HTTP).

Om du behöver är integrerad slutpunkt till slutpunkt-scenario: video tillgångar är under dynamisk DRM-skydd i Azure Media Services med token autentisering och JWT-token som genereras av Azure AD, måste du logga in.

### <a name="user-login"></a>Användarinloggning
Du måste ha en ”konto” skapas eller läggs för att testa det integrerade DRM-systemet för slutpunkt till slutpunkt.

Vilket konto?

Även om Azure ursprungligen åtkomst endast av Microsoft-kontoanvändare, kan nu åtkomst av användare från båda systemen. Vi gjorde detta genom att konfigurera alla Azure-egenskaper att lita på Azure AD för autentisering, genom att låta Azure AD autentisera organisationsanvändare och genom att skapa en federationsrelation där Azure AD litar på konsumentidentitetssystemet för Microsoft-konton för autentisering av konsumentanvändare. Resultatet är att Azure AD kan autentisera Microsoft-gästkonton och ”interna” Azure AD-konton.

Eftersom Azure AD litar på Microsoft-konto (MSA)-domän, kan du lägga till alla konton från någon av följande domäner med anpassad Azure AD-klient och använda konto för inloggning:

| **Domännamn** | **Domän** |
| --- | --- |
| **Anpassad Azure AD-klient domän** |somename.onmicrosoft.com |
| **Företagets domän** |Microsoft.com |
| **Microsoft-konto (MSA)-domän** |Outlook.com, live.com, hotmail.com |

Du kan kontakta någon av författarna måste ha ett konto som har skapats eller lagts till för dig.

Nedan visas skärmdumpar av olika inloggningssidor som används av olika domänkonton.

**Anpassad Azure AD-klient domänkonto**: I detta fall kan du se anpassade inloggningssidan för ett anpassat Azure AD-klient-domän.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft domänkonto med smartkort**: I det här fallet visas på inloggningssidan anpassade genom Microsoft företagets IT med tvåfaktorsautentisering.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto (MSA)**: I detta fall kan du se inloggningssidan för Account för konsumenter.

![Domänkonto för anpassad Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Använder krypterade Media-tillägg för PlayReady
På en modern webbläsare med krypterade (EME Media tillägg) för PlayReady-support, till exempel Internet Explorer 11 på Windows 8.1 och in och Microsoft Edge-webbläsaren på Windows 10 blir PlayReady underliggande DRM för EME.

![Med hjälp av EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Området mörkt player beror på att PlayReady skydd förhindrar en gör skärmdump av skyddade video.

Följande skärmbild visar player-plugin-program och mus/EME support.

![Med hjälp av EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 på Windows 10 kan anropas av [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) på Windows 10-enheter som stöder den. PlayReady SL3000 låser upp flödet av förbättrade premium-innehåll (4K HDR, etc.) och nya modeller för innehållsleverans (tidig fönster för förbättrat innehåll).

Fokusera på de Windows-enheterna: PlayReady är den enda DRM i maskinvara som är tillgängliga på Windows-enheter (PlayReady SL3000). En strömmande tjänst kan använda PlayReady via EME eller en UWP-appen och ger en högre bildkvaliteten med PlayReady SL3000 än en annan DRM. Normalt flödar 2K innehåll via Chrome eller Firefox och 4K innehåll via Microsoft Edge/IE11 eller en UWP-appen på samma enhet (beroende på implementering och inställningar).

#### <a name="using-eme-for-widevine"></a>Med hjälp av EME för Widevine
På en modern webbläsare med EME/Widevine-support, till exempel Chrome 41 + på Windows 10, Windows 8.1-, Mac OS x Yosemite och Chrome för Android 4.4.4 är Google Widevine DRM bakom EME.

![Med hjälp av EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Observera att Widevine inte hindrar något från att göra en skärmdump av skyddade video.

![Med hjälp av EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Inte berättigat till användare
Om en användare inte är medlem i gruppen ”rätt användare” användaren kommer inte att kunna skicka ”Kontrollera rätt” och tjänsten multi-DRM-licens kommer neka att utfärda begärda licensen enligt nedan. Detaljerad beskrivning är ”licens hämta misslyckades”, vilket är fungerar.

![Icke rätt användare](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Kör anpassade Secure Token Service
För scenariot med anpassade Secure säkerhetstokentjänst (STS) som körs utfärdas JWT-token av anpassade STS med hjälp av antingen symmetriskt eller asymmetriskt nyckel.

I fallet med hjälp av symmetriska nyckel (med hjälp av Chrome):

![Kör anpassade STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

I fallet med hjälp av asymmetrisk nyckel via X509 certifikat (med Microsoft moderna webbläsare).

![Kör anpassade STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I båda ovanstående fall oförändrad användarautentisering – via Azure AD. Den enda skillnaden är att JWT-token har utfärdats av anpassade STS i stället för Azure AD. Naturligtvis när du konfigurerar skydd för dynamiska CENC begränsningar av licensleveranstjänst anger vilken typ av JWT-token, antingen symmetriskt eller asymmetriskt nyckel.

## <a name="summary"></a>Sammanfattning
I det här dokumentet beskrivs vi CENC med flera native DRM och åtkomstkontroll via tokenautentisering: sin design och dess implementering med Azure, Azure Media Services och Azure Media Player.

* En referens design visas som innehåller alla nödvändiga komponenter i ett DRM/CENC undersystem;
* En referensimplementering på Azure, Azure Media Services och Azure Media Player.
* Vissa avsnitt som är direkt inblandade i struktur och totala beskrivs också.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
