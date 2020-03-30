---
title: Varför uppdatera till Microsoft identity platform (v2.0) | Azure
description: Lär dig skillnaderna mellan Microsoft identity-plattformen (v2.0) och Azure Active Directory (Azure AD) v1.0-slutpunkten och lär dig fördelarna med att uppdatera till v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154924"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Varför uppdatera till Microsoft-identitetsplattformen (v2.0)?

När du utvecklar ett nytt program är det viktigt att känna till skillnaderna mellan Microsoft identity-plattformen (v2.0) och Azure Active Directory (v1.0) slutpunkter. Den här artikeln beskriver de viktigaste skillnaderna mellan slutpunkterna och vissa befintliga begränsningar för Microsofts identitetsplattform.

> [!NOTE]
> Slutpunkten för Microsoft-identitetsplattform stöder inte alla Azure AD-scenarier och -funktioner. För att ta reda på om du ska använda slutpunkten för Microsoft identity platform läser du om begränsningar av [Microsofts identitetsplattform](#limitations).

## <a name="who-can-sign-in"></a>Vem kan logga in

![Vem kan logga in med v1.0- och v2.0-slutpunkter](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* V1.0-slutpunkten tillåter endast arbets- och skolkonton att logga in på ditt program (Azure AD)
* Slutpunkten för Microsoft identity-plattformen gör att arbets- och skolkonton från Azure AD och personliga Microsoft-konton (MSA), till exempel hotmail.com, outlook.com och msn.com, kan logga in.
* Båda slutpunkterna accepterar också inloggningar för *[gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* i en Azure *[AD-katalog](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* för program som konfigurerats som enklöver`https://login.microsoftonline.com/{TenantId_or_Name}`eller för program med flera *innehavare* som konfigurerats för att peka på den klientspecifika slutpunkten ( ).

Med slutpunkten för Microsoft identity platform kan du skriva appar som accepterar inloggningar från personliga Microsoft-konton och arbets- och skolkonton. Detta ger dig möjlighet att skriva din app helt konto-agnostiker. Om din app till exempel anropar [Microsoft Graph](https://graph.microsoft.io)är vissa ytterligare funktioner och data tillgängliga för arbetskonton, till exempel deras SharePoint-webbplatser eller katalogdata. Men för många åtgärder, till exempel [Läsa en användares e-post,](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0)samma kod kan komma åt e-post för både personliga och arbete och skolkonton.

För Microsoft identity platform-slutpunkten kan du använda Microsoft Authentication Library (MSAL) för att få åtkomst till konsument-, utbildnings- och företagsvärldar. Azure AD v1.0-slutpunkten accepterar endast inloggningar från arbets- och skolkonton.

## <a name="incremental-and-dynamic-consent"></a>Inkrementellt och dynamiskt samtycke

Appar som använder slutpunkten Azure AD v1.0 krävs för att ange de nödvändiga OAuth 2.0-behörigheterna i förväg, till exempel:

![Exempel som visar användargränssnittet för behörighetsregistrering](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

Behörigheterna som anges direkt på programregistreringen är **statiska**. Även om statiska behörigheter för appen som definierats i Azure-portalen håller koden trevlig och enkel, presenterar den några möjliga problem för utvecklare:

* Appen måste begära alla behörigheter som den någonsin skulle behöva vid användarens första inloggning. Detta kan leda till en lång lista med behörigheter som avskräcker slutanvändare från att godkänna appens åtkomst vid första inloggningen.

* Appen behöver veta alla resurser den någonsin skulle komma åt i förväg. Det var svårt att skapa appar som kunde komma åt ett godtyckligt antal resurser.

Med slutpunkten för Microsoft-identitetsplattform kan du ignorera de statiska behörigheter som definierats i appregistreringsinformationen i Azure-portalen och begära behörigheter stegvis, vilket innebär att be om en minsta uppsättning behörigheter i förväg och växer mer med tiden när kunden använder ytterligare appfunktioner. För att göra det kan du ange de scope som appen behöver `scope` när som helst genom att inkludera de nya scope i parametern när du begär en åtkomsttoken - utan att behöva fördefiniera dem i programregistreringsinformationen. Om användaren ännu inte har samtyckt till nya scope som lagts till i begäran uppmanas de att bara godkänna de nya behörigheterna. Mer information finns i [behörigheter, medgivande och omfattningar](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Om du tillåter en app att `scope` begära behörigheter dynamiskt via parametern får utvecklarna full kontroll över användarens upplevelse. Du kan också frontladda din medgivande upplevelse och be om alla behörigheter i en första auktoriseringsbegäran. Om din app kräver ett stort antal behörigheter kan du samla in dessa behörigheter från användaren stegvis när de försöker använda vissa funktioner i appen över tid.

Administratörsmedgivande som görs för en organisation kräver fortfarande statiska behörigheter som registrerats för appen, så du bör ange dessa behörigheter för appar i appregistreringsportalen om du behöver en administratör för att ge sitt samtycke för hela organisationen. Detta minskar de cykler som krävs av organisationsadministratören för att konfigurera programmet.

## <a name="scopes-not-resources"></a>Scope, inte resurser

För appar som använder v1.0-slutpunkten kan en app fungera som en **resurs**eller mottagare av token. En resurs kan definiera ett antal **scope** eller **oAuth2Permissions** som den förstår, vilket gör att klientappar kan begära token från den resursen för en viss uppsättning scope. Tänk på Microsoft Graph API som ett exempel på en resurs:

* Resursidentifierare, `AppID URI`eller :`https://graph.microsoft.com/`
* Scope, eller `oAuth2Permissions` `Directory.Read`: `Directory.Write`, , och så vidare.

Detta gäller för slutpunkten för Microsoft-identitetsplattformen. En app kan fortfarande fungera som en resurs, definiera scope och identifieras av en URI. Klientappar kan fortfarande begära åtkomst till dessa scope. Men det sätt som en klient begär dessa behörigheter har ändrats.

För v1.0-slutpunkten kan en OAuth 2.0-auktorisera begäran till Azure AD ha sett ut:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Här anger **resource** resursparametern vilken resurs klientappen begär auktorisering. Azure AD beräknade de behörigheter som krävs av appen baserat på statisk konfiguration i Azure-portalen och utfärdade token därefter.

För program som använder slutpunkten för Microsoft-identitetsplattform ser samma OAuth 2.0-auktoriseringsbegäran ut:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Här anger **scope** scopeparametern vilken resurs och behörigheter appen begär auktorisering. Den önskade resursen finns fortfarande i begäran - den ingår i vart och ett av värdena för scopeparametern. Om du använder scopeparametern på det här sättet kan slutpunkten för Microsoft identity platform vara mer kompatibel med OAuth 2.0-specifikationen och anpassas närmare till vanliga branschpraxis. Det gör det också möjligt för appar att göra [inkrementellt medgivande](#incremental-and-dynamic-consent) - endast begära behörigheter när programmet kräver dem i motsats till på framsidan.

## <a name="well-known-scopes"></a>Välkända omfattningar

### <a name="offline-access"></a>Offlineåtkomst

Appar som använder slutpunkten för Microsofts identitetsplattform kan kräva användning `offline_access` av en ny välkänd behörighet för appar – omfattningen. Alla appar måste begära den här behörigheten om de behöver komma åt resurser för en användares räkning under en längre tid, även om användaren kanske inte aktivt använder appen. Omfattningen `offline_access` visas för användaren i dialogrutor för medgivande som Åtkomst till **dina data när som helst**, vilket användaren måste godkänna. Om du `offline_access` begär behörighet kan webbappen ta emot OAuth 2.0 refresh_tokens från slutpunkten för Microsofts identitetsplattform. Uppdatera token är långlivade och kan bytas ut mot nya OAuth 2.0-åtkomsttoken för längre åtkomstperioder.

Om appen inte begär `offline_access` scopet får den inte uppdateringstoken. Det innebär att när du löser in en auktoriseringskod i OAuth 2.0-auktoriseringskodflödet får du bara tillbaka en åtkomsttoken från `/token` slutpunkten. Åtkomsttoken förblir giltig under en kort tidsperiod (vanligtvis en timme), men upphör så småningom att gälla. Vid den `/authorize` tidpunkten måste appen omdirigera användaren tillbaka till slutpunkten för att hämta en ny auktoriseringskod. Under den här omdirigeringen kanske användaren behöver ange sina autentiseringsuppgifter igen eller stämma av behörigheter igen, beroende på vilken typ av app det är.

Mer information om OAuth `refresh_tokens`2.0 `access_tokens`och information finns i [microsofts identitetsplattformsprotokollreferens](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profil och e-post

Historiskt sett skulle det mest grundläggande OpenID Connect-inloggningsflödet med Microsofts identitetsplattform ge mycket information om användaren i den resulterande *id_token*. Anspråken i en id_token kan innehålla användarens namn, önskat användarnamn, e-postadress, objekt-ID med mera.

Informationen som `openid` omfånget ger din app åtkomst till är nu begränsad. Omfattningen `openid` tillåter bara att appen loggar in användaren och får en appspecifik identifierare för användaren. Om du vill få personuppgifter om användaren i din app måste appen begära ytterligare behörigheter från användaren. Två nya scope `email` `profile`och gör att du kan begära ytterligare behörigheter.

* Omfattningen `email` ger din app åtkomst till användarens primära `email` e-postadress via anspråket i id_token, förutsatt att användaren har en adresserbar e-postadress.
* Omfattningen `profile` ger din app åtkomst till all annan grundläggande information om användaren, till exempel deras namn, önskat användarnamn, objekt-ID och så vidare, i id_token.

Med dessa scope kan du koda din app på ett minimalt avslöjande sätt så att du bara kan be användaren om den information som appen behöver för att göra sitt jobb. Mer information om dessa scope finns [i microsoft identity platform scope reference](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Tokenanspråk

Slutpunkten för Microsoft-identitetsplattformen utfärdar en mindre uppsättning anspråk i sina token som standard för att hålla nyttolaster små. Om du har appar och tjänster som är beroende av ett visst anspråk i en v1.0-token som inte längre tillhandahålls som standard i en Microsoft-identitetsplattformstoken kan du överväga att använda den [valfria anspråksfunktionen](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att inkludera det anspråket.

> [!IMPORTANT]
> v1.0 och v2.0 tokens kan utfärdas av både v1.0 och v2.0-slutpunkter! id_tokens *alltid* matchar slutpunkten som de begärs från och åtkomsttoken *alltid* matchar det format som förväntas av webb-API:et som klienten anropar med den token.  Så om din app använder v2.0-slutpunkten för att få en token att anropa Microsoft Graph, som förväntar sig v1.0-formatåtkomsttoken, får din app en token i v1.0-format.  

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar att vara medveten om när du använder Microsofts identitetsplattform.

När du skapar program som integreras med Microsofts identitetsplattform måste du bestämma om slutpunkten för Microsoft-identitetsplattformen och autentiseringsprotokollen uppfyller dina behov. V1.0-slutpunkten och plattformen stöds fortfarande fullt ut och är i vissa avseenden mer funktionsrik än Microsofts identitetsplattform. Microsofts [identitetsplattform introducerar](azure-ad-endpoint-comparison.md) dock betydande fördelar för utvecklare.

Här är en förenklad rekommendation för utvecklare nu:

* Om du vill eller behöver stödja personliga Microsoft-konton i ditt program, eller om du skriver ett nytt program, använder du Microsofts identitetsplattform. Men innan du gör det, se till att du förstår de begränsningar som diskuteras i den här artikeln.
* Om du migrerar eller uppdaterar ett program som är beroende av SAML kan du inte använda Microsofts identitetsplattform. I stället, se [Azure AD v1.0 guide](v1-overview.md).

Slutpunkten för Microsoft-identitetsplattformen utvecklas för att eliminera de begränsningar som anges här, så att du bara behöver använda slutpunkten för Microsoft-identitetsplattformen. Under tiden kan du använda den här artikeln för att avgöra om slutpunkten för Microsoft-identitetsplattformen är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla det aktuella läget för slutpunkten för Microsoft-identitetsplattformen. Kom tillbaka för att omvärdera dina krav mot Microsofts identitetsplattformsfunktioner.

### <a name="restrictions-on-app-registrations"></a>Begränsningar för appregistreringar

För varje app som du vill integrera med slutpunkten för Microsoft identity-plattformen kan du skapa en appregistrering i den nya [ **appregistreringsupplevelsen** ](https://aka.ms/appregistrations) i Azure-portalen. Befintliga Microsoft-kontoappar är inte kompatibla med portalen, men alla Azure AD-appar är det, oavsett var eller när de registrerades.

Appregistreringar som stöder arbets- och skolkonton och personliga konton har följande förbehåll:

* Endast två apphemligheter tillåts per program-ID.
* Ett program som inte har registrerats i en klient kan bara hanteras av kontot som registrerade det. Det kan inte delas med andra utvecklare. Detta är fallet för de flesta appar som har registrerats med ett personligt Microsoft-konto i App Registration Portal. Om du vill dela din appregistrering med flera utvecklare registrerar du programmet i en klient med hjälp av avsnittet nya **appregistreringar** i Azure-portalen.
* Det finns flera begränsningar för formatet på den omdirigerings-URL:en som är tillåten. Mer information om omdirigerings-URL finns i nästa avsnitt.

### <a name="restrictions-on-redirect-urls"></a>Begränsningar för omdirigeringsadresser

Appar som är registrerade för Microsofts identitetsplattform är begränsade till en begränsad uppsättning url-värden för omdirigering. Omdirigerings-URL:en för `https`webbappar och webbtjänster måste börja med schemat och alla url-värden för omdirigering måste dela en enda DNS-domän.  Registreringssystemet jämför hela DNS-namnet på den befintliga omdirigerings-URL:en med DNS-namnet på den omdirigerings-URL som du lägger till. `http://localhost`stöds också som en omdirigerings-URL.  

Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet på den nya omdirigerings-URL:en matchar inte DNS-namnet på den befintliga omdirigerings-URL:en.
* Hela DNS-namnet på den nya omdirigerings-URL:en är inte en underdomän till den befintliga omdirigerings-URL:en.

#### <a name="example-1"></a>Exempel 1

Om appen har en `https://login.contoso.com`omdirigerings-URL till kan du lägga till en omdirigerings-URL där DNS-namnet matchar exakt, vilket visas i följande exempel:

`https://login.contoso.com/new`

Du kan också referera till en DNS-underdomän i login.contoso.com, som visas i följande exempel:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exempel 2

Om du vill ha en `login-east.contoso.com` `login-west.contoso.com` app som har och som omdirigeringsadresser måste du lägga till dessa omdirigeringsadresser i följande ordning:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till de två sistnämnda eftersom de är underdomäner till den första omdirigeringsadressen, contoso.com.

Du kan bara ha 20 svarsadresser för ett visst program – den här gränsen gäller för alla apptyper som registreringen stöder (spa(single page application), native client, web app och service).  

Mer information om hur du registrerar en app för användning med Microsofts identitetsplattform finns i [Registrera en app med hjälp av den nya appregistreringsupplevelsen](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK:er

För närvarande är biblioteksstödet för slutpunkten för Microsoft identity platform begränsat. Om du vill använda slutpunkten för Microsoft identity platform i ett produktionsprogram har du följande alternativ:

* Om du skapar ett webbprogram kan du på ett säkert sätt använda den allmänt tillgängliga mellanprogram på serversidan för att göra inloggning och tokenvalidering. Dessa inkluderar OWIN OpenID Connect mellanprogram för ASP.NET och node.js Passport plug-in. Kodexempel som använder Microsoft middleware finns i avsnittet [Komma igång med Microsoft identity platform.](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started)
* Om du skapar ett skrivbords- eller mobilprogram kan du använda något av Microsoft Authentication Libraries (MSAL). Dessa bibliotek är allmänt tillgängliga eller i en förhandsversion som stöds av produktionen, så det är säkert att använda dem i produktionsprogram. Du kan läsa mer om villkoren för förhandsversionen och de tillgängliga biblioteken i [autentiseringsbibliotekens referens](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* För plattformar som inte omfattas av Microsoft-bibliotek kan du integrera med slutpunkten för Microsoft-identitetsplattformen genom att skicka och ta emot protokollmeddelanden direkt i programkoden. OpenID Connect- och OAuth-protokollen [dokumenteras uttryckligen](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att hjälpa dig att göra en sådan integrering.
* Slutligen kan du använda OpenID Connect- och OAuth-bibliotek med öppen källkod för att integreras med slutpunkten för Microsoft-identitetsplattformen. Slutpunkten för Microsoft-identitetsplattformen bör vara kompatibel med många protokollbibliotek med öppen källkod utan ändringar. Tillgängligheten för dessa typer av bibliotek varierar beroende på språk och plattform. [OpenID Connect](https://openid.net/connect/) och [OAuth 2.0](https://oauth.net/2/) webbplatser har en lista över populära implementeringar. Mer information finns i [Microsofts identitetsplattform och autentiseringsbibliotek](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)och listan över klientbibliotek och exempel med öppen källkod som har testats med slutpunkten för Microsoft-identitetsplattformen.
* Som referens `.well-known` är `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`slutpunkten för den gemensamma slutpunkten för Microsoft identity platform . Ersätt `common` med ditt klient-ID för att hämta data som är specifika för din klient.  

### <a name="protocol-changes"></a>Ändringar av protokoll

Slutpunkten för Microsoft-identitetsplattform stöder inte SAML eller WS-Federation. Den stöder bara OpenID Connect och OAuth 2.0.  De anmärkningsvärda ändringarna av OAuth 2.0-protokollen från v1.0-slutpunkten är: 

* Anspråket `email` returneras om ett valfritt anspråk har konfigurerats **eller** scope=e-post har angetts i begäran. 
* Parametern `scope` stöds nu i `resource` stället för parametern.  
* Många svar har ändrats för att göra dem mer kompatibla med OAuth `expires_in` 2.0-specifikationen, till exempel korrekt tillbaka som ett int i stället för en sträng.  

Mer omfång för protokollfunktioner som stöds i slutpunkten för Microsoft-identitetsplattform finns i [OpenID Connect och OAuth 2.0-protokollreferensen](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-restrictions"></a>SAML-begränsningar

Om du har använt ADAL (Active Directory Authentication Library) i Windows-program kan du ha utnyttjat Windows-integrerad autentisering, som använder saml-kontrollbidraget (Security Assertion Markup Language). Med det här bidraget kan användare av federerade Azure AD-klienter tyst autentisera med sin lokala Active Directory-instans utan att ange autentiseringsuppgifter. Saml-kontrollbidraget stöds inte på slutpunkten för Microsoft-identitetsplattformen.
