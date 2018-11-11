---
title: Jämföra Azure AD v2.0-slutpunkten med v1.0-slutpunkten | Microsoft Docs
description: Vet skillnaderna mellan Azure AD v2.0-slutpunkten och v1.0-slutpunkten
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: andret
ms.reviewer: hirsin, andret
ms.custom: aaddev
ms.openlocfilehash: 215e0abe196620624dcca7f430aec4ee9b9612f2
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288211"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Jämföra Azure AD v2.0-slutpunkten med v1.0-slutpunkt

När du utvecklar ett nytt program är det viktigt att veta skillnaderna mellan slutpunkterna v1.0 och v2.0. Nedan visas de viktigaste skillnaderna, samt vissa befintliga begränsningar för v2.0-slutpunkten.

> [!NOTE]
> Inte alla Azure Active Directory (Azure AD)-scenarier och funktioner stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](#limitations).

## <a name="who-can-sign-in"></a>Vem som kan logga in

![Vem som kan logga in med v1.0 och v2.0-slutpunkter](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* V1.0-slutpunkten tillåter bara arbets- och skolkonton konton att logga in på ditt program (Azure AD)

* V2.0-slutpunkten kan arbets- och skolkonton från Azure AD- och personliga konton (MSA) (hotmail.com, outlook.com, msn.com) för att logga in.

* Både v1.0 och v2.0-slutpunkter också acceptera inloggningar för *[gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* av Azure AD-katalog för program som har konfigurerats som *[enda klient](single-and-multi-tenant-apps.md)* eller för *flera innehavare* program som har konfigurerats för att peka på den klientspecifik-slutpunkten (`https://login.microsoftonline.com/{TenantId_or_Name}`).

V2.0-slutpunkten kan du skriva appar som har stöd för inloggning från både personliga och arbets och skolan konton, vilket ger dig möjlighet att skriva din app helt konto-oberoende. Till exempel om appen anropar den [Microsoft Graph](https://developer.microsoft.com/graph), vissa ytterligare funktioner och data kommer att kunna arbeta konton, till exempel sina SharePoint-webbplatser eller katalogdata. Men för många åtgärder, till exempel [läsning av en användares e-post](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/message), samma kod kan komma åt e-post för både personliga och arbets och skolkonton.

Du kan använda en enda library (MSAL) för v2.0-slutpunkten för att få åtkomst till både konsument, högskolor och enterprise världar.

 Azure AD v1.0 slutpunkten accepterar inloggningar från arbets- och skolkonton konton.

## <a name="incremental-and-dynamic-consent"></a>Inkrementell och dynamiska medgivande

Appar med hjälp av Azure AD v1.0 slutpunkten krävs för att ange sina OAuth 2.0-behörigheter som krävs i förväg, till exempel:

![Behörigheter för registrering av Användargränssnittet](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Behörighetsinställningar direkt från programregistrering är **Statiska**. Statisk behörigheter definieras i Azure-portalen och sparas koden bra och enkelt, kan det medföra några problem för utvecklare:

* Appen måste känner till alla de behörigheter som den skulle behöver vid tidpunkten för skapandet av appen. Lägger till behörigheter med tiden var svåra.

* Appen måste kunna alla resurser som den skulle någonsin åt förbereds i förväg. Det var svårt att skapa appar som kan komma åt ett valfritt antal resurser.

* Appen måste begära alla behörigheter som den skulle behöver vid användarens första inloggning. I vissa fall detta ledde till en lång lista med behörigheter som det rekommenderas inte slutanvändare från att godkänna appens åtkomst på första inloggning.

Med v2.0-slutpunkten kan du ignorera de statiska definierade behörigheter som definierats i registreringsinformation app i Azure-portalen och ange behörigheterna som dina appbehov **dynamiskt** vid körning, vid vanlig användning av din App, oavsett statiska definierade behörigheter i registreringsinformation för programmet.

Om du vill göra det, du kan ange omfång som din app behöver vid en given tidpunkt i ditt program tid genom att inkludera de nya scope i den `scope` parameter när du begär en åtkomsttoken - utan att behöva förväg definiera dem i program registreringsinformation .

Om användaren inte har ännu godkänt att nya scope som lagts till i begäran, uppmanas de att endast godkänna de nya behörigheterna. Om du vill veta mer kan du läsa mer på [behörigheter och samtycke scope](v2-permissions-and-consent.md).

Så att en app för att begära behörighet dynamiskt via den `scope` parametern ger utvecklarna fullständig kontroll över din användarupplevelse. Om du vill kan välja du också att front Läs in ditt medgivande upplevelse och be om alla behörigheter i en inledande auktoriseringsbegäran. Eller om din app kräver ett stort antal behörigheter, kan du samla in dessa behörigheter från användaren stegvis, så de försöker använda vissa funktioner i din app med tiden.

Observera att administratörens godkännande klar på ett annat företag fortfarande använder statiska behörigheter som har registrerats för appen, så vi rekommenderar att du ställer in dessa behörigheter för appar som använder v2.0-slutpunkten om du behöver en administratör att ge samtycke åt hela organisation. Detta minskar cykler som krävs av administratören organisation för att konfigurera programmet

## <a name="scopes-not-resources"></a>Scope, inte resurser

För appar som använder v1.0 slutpunkten kan en app kan fungera som en **resource**, eller en mottagare av token. En resurs kan definiera ett antal **scope** eller **oAuth2Permissions** som den förstår, vilket gör att klientdatorer appar att begära token till den resursen för en viss uppsättning omfång. Överväg att Azure AD Graph-API som ett exempel på en resurs:

* Resurs-ID eller `AppID URI`: `https://graph.windows.net/`

* Scope, eller `OAuth2Permissions`: `Directory.Read`, `Directory.Write`och så vidare.

Allt detta gäller för v2.0-slutpunkten. En app kan fortfarande fungera som resursen, definiera omfång och identifieras av en URI. Klientappar kan fortfarande att begära åtkomst till dessa omfattningar. Men har det sätt som en klient begär att dessa behörigheter ändrats. För att auktorisera v1.0 slutpunkten, en OAuth 2.0 kan begäran till Azure AD har tittat som:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

där den **resource** parametern anges vilken resurs som klientappen begär auktorisering för. Azure AD beräknade de behörigheter som krävs av appen utifrån statisk konfiguration i Azure portal och därefter utfärdade token. För program som använder v2.0-slutpunkten, auktorisera samma OAuth 2.0 begäran ut:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

där den **omfång** parametern indikerar vilken resurs och behörigheter appen begär auktorisering för. Önskad resurs finns kvar i begäran - det bara finns i vart och ett av värdena för omfångets parameter. Använda omfattningsparametern i det här sättet kan v2.0-slutpunkten är mer kompatibel med OAuth 2.0-specifikationen och överensstämmer bättre med vanliga metoder i branschen. Dessutom kan appar att utföra [inkrementella medgivande](#incremental-and-dynamic-consent), vilket beskrivs tidigare.

## <a name="well-known-scopes"></a>Välkända scope

### <a name="offline-access"></a>Offline-åtkomst

Appar med hjälp av v2.0-slutpunkten kan kräva användning av en ny välkända behörighet för appar - den `offline_access` omfång. Alla appar måste begära denna behörighet om de behöver för att komma åt resurser på uppdrag av en användare under en långvarig tid, även om användaren inte kanske aktivt använder appen. Den `offline_access` omfattning visas för användaren i dialogrutor för användarmedgivande som **komma åt dina data när som helst**, som användaren måste samtycka till. Begär den `offline_access` behörighet kan din webbapp tar emot OAuth 2.0 refresh_tokens från v2.0-slutpunkten. Uppdatera token är långlivade och kan utbyta för nya OAuth 2.0-åtkomsttoken under långa perioder av åtkomst.

Om din app inte begär det `offline_access` omfattning, och tar inte emot uppdateringstoken. Det innebär att när du lösa in en auktoriseringskod i OAuth 2.0-auktoriseringskodflödet endast får du tillbaka en åtkomsttoken från den `/token` slutpunkt. Den åtkomsttoken kommer att gälla för en kort tidsperiod (vanligtvis en timme), men till slut upphör att gälla. AT tidpunkt, din app behöver du därefter skickas användarna tillbaka till den `/authorize` slutpunkt för att hämta en ny auktoriseringskod. Under den här omdirigering, användaren kanske eller kanske inte behöver ange dem igen eller nytt medgivanden till behörigheter, beroende på vilken typ av app.

Mer information om OAuth 2.0 `refresh_tokens`, och `access_tokens`, Kolla in den [protokollreferens för v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID-profil och e-post

Historiskt sett har mest grundläggande OpenID Connect-inloggning flödet med Azure AD skulle uppge en mängd information om användaren i den resulterande *id_token*. Anspråk i en *id_token* kan innehålla användarens namn, önskad användarnamn, e-postadress, objekt-ID och mer.

Informationen som den `openid` omfång får din app åtkomst till är nu begränsade. Den `openid` omfång kan din app för att logga in användaren och ta emot en appspecifika identifierare för användaren. Om du vill hämta personliga data om användare i din app, måste din app och begär ytterligare behörighet från användaren. Två nya scope – den `email` och `profile` omfång – gör att du kan begära ytterligare behörighet.

Den `email` omfånget gör din appåtkomst till användarens primära e-postadress via den `email` anspråk i id_token. Den `profile` omfång får din appåtkomst till andra grundläggande information om användare, deras namn, önskad användarnamn, objekt-ID och så vidare.

Du kan koda din app på en minimal avslöjande sätt – endast kan du be användaren uppsättning information att din app kräver för att utföra sitt arbete. Mer information om dessa scope finns [omfattningsreferens v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Tokenanspråken

Anspråk i token som utfärdas av v2.0-slutpunkten kan inte vara identiskt med token som utfärdas av den allmänt tillgängliga Azure AD-slutpunkter. Appar som migrerar till den nya tjänsten kan inte utgå från ett visst anspråk kommer att finnas i id_tokens eller access_tokens. Ytterligare information om olika typer av token som används i v2.0-slutpunkten är tillgängliga i den [åtkomsttoken](access-tokens.md) referens och [ `id_token` referens](id-tokens.md)

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar vara medvetna om när du använder version 2.0.

När du skapar program som integreras med Microsoft identity-plattformen måste du bestämma om protokollen v2.0-slutpunkten och autentisering uppfyller dina behov. V1.0 slutpunkt och plattformen stöds fortfarande fullt ut och i vissa avseenden är fler funktioner än v2.0. Dock v2.0 [introducerar stora fördelar](azure-ad-endpoint-comparison.md) för utvecklare.

Här är en förenklad rekommendation för utvecklare som vid denna tidpunkt:

* Om du måste ha stöd för personliga Microsoft-konton i ditt program, använder du v2.0. Men innan du gör det, måste du kontrollera att du förstår de begränsningar som beskrivs i den här artikeln.

* Om ditt program behöver bara stöder Microsoft arbets- och skolkonton, Använd inte version 2.0. I stället referera till den [v1.0 guiden](v1-overview.md).

V2.0-slutpunkten kommer att utvecklas för att ta bort de begränsningar som anges här, så att du behöver bara använda v2.0-slutpunkten. Under tiden kan använda den här artikeln för att avgöra om v2.0-slutpunkten är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla det aktuella tillståndet för v2.0-slutpunkten. Kontrollera att gå tillbaka till dina krav mot v2.0 funktioner för att omvärdera.

### <a name="restrictions-on-app-types"></a>Begränsningar för apptyper

För närvarande stöds följande typer av appar som inte av v2.0-slutpunkten. En beskrivning av apptyper som stöds finns i [apptyper i v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>Fristående webb-API: er

Du kan använda v2.0-slutpunkten till [bygga en webb-API som skyddas med OAuth 2.0](v2-app-types.md#web-apis). Men den webb-API kan ta emot token från ett program som har samma programmets ID. Du kan inte komma åt ett webb-API från en klient som har ett annat program-ID. Klienten kommer inte att kunna begära eller erhålla behörighet att ditt webb-API.

Om du vill se hur du skapar ett webb-API som accepterar token från en klient som har samma program-ID finns i v2.0-slutpunkten webb-API-exemplen i den [v2.0 har börjat](v2-overview.md#getting-started) avsnittet.

### <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar

För närvarande för varje app som du vill integrera med v2.0-slutpunkten, måste du skapa en appregistrering i den nya [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Befintligt Azure AD eller Microsoft-konto-appar som inte är kompatibel med v2.0-slutpunkten. Appar som har registrerats i någon portal än på Programregistreringsportalen är inte kompatibla med v2.0-slutpunkten.

Dessutom app-registreringar som du skapar i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) har följande varningar:

* Bara två apphemligheter tillåts per program-ID.

* En appregistrering som registrerats av en användare med ett personligt microsoftkonto kan visas och endast hanteras av en enda developer-konto. Det kan inte delas mellan flera utvecklare. Om du vill dela din appregistrering av flera utvecklare kan du skapa programmet genom att logga in på portalen för registrering med en Azure AD-konto.

* Det finns flera begränsningar på formatet för omdirigerings-URL som är tillåtet. Mer information om omdirigerings-URL finns i nästa avsnitt.

### <a name="restrictions-on-redirect-urls"></a>Begränsningar för omdirigera URL: er

Appar som har registrerats i på Programregistreringsportalen är begränsade till en begränsad uppsättning omdirigerings-URL-värden. Omdirigerings-URL för webbappar och tjänster måste börja med schemat `https`, och alla omdirigerings-URL-värden måste dela en enda DNS-domän. Exempel: du kan inte registrera ett webbprogram som har en av dessa omdirigera URL: er:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

Registreringssystemet jämför hela DNS-namnet på DNS-namnet på omdirigerings-URL som du lägger till befintliga omdirigerings-URL. Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet på den nya omdirigerings-URL: en matchar inte den befintliga omdirigerings-URL: en DNS-namn.

* Hela DNS-namnet på den nya omdirigerings-URL är inte en underdomän till befintliga omdirigerings-URL.

Exempel: om appen har den här omdirigerings-URL:

`https://login.contoso.com`

Du kan lägga till data så här:

`https://login.contoso.com/new`

I det här fallet matchar DNS-namnet exakt. Du kan också göra detta:

`https://new.login.contoso.com`

I så fall måste du referera till DNS-underdomänen login.contoso.com. Om du vill ha en app som har `login-east.contoso.com` och `login-west.contoso.com` som omdirigerings-URL: er, måste du lägga till de omdirigera URL: er i den här ordningen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till två senare eftersom de är underdomäner i första omdirigerings-URL: en, contoso.com. Den här begränsningen tas bort i en kommande version.

Observera också, du kan ha högst 20 svars-URL för ett visst program.

Läs hur du registrerar en app i portalen för registrering av program i [hur du registrerar en app med v2.0-slutpunkten](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK: er

Stöd för v2.0-slutpunkten är för närvarande begränsad. Om du vill använda v2.0-slutpunkten i ett produktionsprogram finns följande alternativ:

* Om du skapar ett webbprogram, kan du på ett säkert sätt använda allmänt tillgängligt från serversidan mellanprogram för Microsoft för att utföra verifiering av inloggning och token. Dessa inkluderar OWIN öppna ID Connect-mellanprogram för ASP.NET och Node.js Passport-plugin-programmet. Kodexempel som använder Microsoft mellanprogram, finns det [v2.0 har börjat](v2-overview.md#getting-started) avsnittet.

* Om du skapar ett skrivbord eller mobila program kan använda du en av förhandsversionen av Microsoft Authentication Libraries (MSAL). Dessa bibliotek finns i en förhandsversion av produktion som stöds, så det är säkert att använda dem i produktionsprogram. Du kan läsa mer om villkoren för förhandsversionen och tillgängliga bibliotek i [autentisering bibliotek referens](reference-v2-libraries.md).

* För plattformar som inte omfattas av Microsoft-bibliotek, kan du integrera med v2.0-slutpunkten genom att skicka och ta emot protokollmeddelanden i din programkod direkt. Protokollen v2.0 OpenID Connect och OAuth [dokumenteras uttryckligen](active-directory-v2-protocols.md) för att utföra en sådan integration.

* Slutligen kan du använda öppna ID Connect och OAuth bibliotek med öppen källkod för integrering med v2.0-slutpunkten. V2.0-protokollet ska vara kompatibel med många bibliotek för öppen källkod-protokollet utan större ändringar. Dessa typer av bibliotek varierar efter språk och plattform. Den [öppna ID Connect](http://openid.net/connect/) och [OAuth 2.0](http://oauth.net/2/) webbplatser underhålla en lista över populära implementeringar. Mer information finns i [Azure Active Directory v2.0 och autentisering bibliotek](reference-v2-libraries.md), och en lista över klientbibliotek med öppen källkod och exempel som har testats med v2.0-slutpunkten.

* För referens anger den `.well-known` slutpunkten för vanliga v2.0-slutpunkten är `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Ersätt `common` med klient-ID för att hämta data som är specifika för din klient.  

### <a name="restrictions-on-protocols"></a>Begränsningar för protokoll

V2.0-slutpunkten har inte stöd för SAML eller WS-Federation Det stöder bara öppna ID Connect och OAuth 2.0. Inte alla funktioner och egenskaper i OAuth-protokoll har införlivats i v2.0-slutpunkten.

Följande protokoll-funktionerna och egenskaperna för närvarande är *inte tillgänglig* eller *stöds inte* i v2.0-slutpunkten:

* Den `email` anspråk returneras bara om ett valfritt anspråk har konfigurerats och är omfånget = e-post har angetts i begäran. Dock förväntar sig problemet att ändras när v2.0-slutpunkten uppdateras för att uppfylla ytterligare öppna ID Connect och OAuth2.0-standarder.

* V2.0-slutpunkten har inte stöd för utfärdande roll eller grupp anspråk i ID-token.

* V2.0-slutpunkten har inte stöd för [OAuth 2.0 Resource ägare lösenord klientautentiseringsuppgifter](https://tools.ietf.org/html/rfc6749#section-4.3).

För att bättre förstå vilka protokoll-funktioner som stöds i v2.0-slutpunkten, Läs igenom vår [referens för OpenID Connect och OAuth 2.0-protokollet](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-begränsningar

Om du har använt Active Directory Authentication Library (ADAL) i Windows-program, kanske du har dragit nytta av integrerad Windows-autentisering, som använder Security Assertion Markup Language (SAML) assertion bevilja. Med det här beviljandet federerade användare av Azure AD klienter tyst kan autentiseras med deras lokala Active Directory-instans utan att ange autentiseringsuppgifter. SAML assertion bevilja stöds för närvarande inte på v2.0-slutpunkten.
