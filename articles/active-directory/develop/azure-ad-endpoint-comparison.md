---
title: Vad är nytt i Azure AD v2.0-slutpunkten? | Microsoft Docs
description: En jämförelse mellan den ursprungliga Azure AD v2.0-slutpunkter och.
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
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581922"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Vad är skillnaden mellan v2.0-slutpunkten?

Om du är bekant med Azure Active Directory (AD Azure) eller har integrerade appar med Azure AD tidigare, finns det vissa skillnader i v2.0-slutpunkten som inte förväntar dig. Den här artikeln anropar skillnaderna för din förståelse.

> [!NOTE]
> Inte alla Azure AD-scenarier och funktioner stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft-konton och Azure AD-konton

V2.0-slutpunkten kan utvecklare skriva appar som har stöd för inloggning från både Microsoft-Accounts och Azure AD-konton, med hjälp av en enda auth-slutpunkt. Det gör att du kan skriva din app helt konto-oberoende, vilket innebär att appen kan vara ignorant för typ av konto som användaren loggar in med. Du kan göra din app medveten om typ av konto som används i en viss session, men du behöver.

Till exempel om appen anropar den [Microsoft Graph](https://graph.microsoft.io), vissa ytterligare funktioner och data blir tillgänglig för enterprise-användare, till exempel sina SharePoint-webbplatser eller katalogdata. Men för många åtgärder, till exempel [läsning av en användares e-post](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), koden kan skrivas på exakt samma för både Microsoft-Accounts och Azure AD-konton. 

Integrera din app med Microsoft-Accounts och Azure AD-konton är nu en enkel process. Du kan använda en enda uppsättning slutpunkter, ett enda bibliotek och en enda app-registrering för att få åtkomst till både konsument- och enterprise världar. Om du vill veta mer om v2.0-slutpunkten kan ta en titt [översikten](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Ny app-registreringsportal

Om du vill registrera en app som fungerar med v2.0-slutpunkten måste du använda portalen för registrering av Microsoft app: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Det här är den portal där du kan hämta ett program-ID, anpassa utseendet på inloggningssidan för din app och mycket mer. Allt du behöver för att få åtkomst till portalen är ett drivs microsoftkonto - personlig eller arbete/skola.

## <a name="one-app-id-for-all-platforms"></a>En app-ID för alla plattformar

Om du har använt Azure AD, har du förmodligen registrerat flera olika appar för ett enda projekt. Till exempel om du har byggt både en webbplats och en iOS-app, var du tvungen att registrera dem separat, med hjälp av två olika program-ID: N. Registreringsportal för Azure AD app tvingas du gör denna skillnad under registreringen:

![Gamla programregistrering UI](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

På samma sätt, om du har en webbplats och en serverdel som webb-api, du kanske har registrerat var och en som en separat app i Azure AD. Eller om du en iOS-app och en Android-app måste du även kan har registrerat två olika appar. Registrera varje komponent i ett program ledde till vissa oväntat beteende för utvecklare och deras kunder:

* Varje komponent visas som en separat app i Azure AD-klient för varje kund.
* När en Innehavaradministratör försökte tillämpa principen för att hantera åtkomst till eller ta bort en app måste de göra det för varje komponent i appen.
* När kunder samtyckt till ett program, visas varje komponent i godkännandeskärmen som ett olika program.

Med v2.0-slutpunkten kan du nu registrera alla komponenter i ditt projekt som en enda app-registrering och använda ett enda program-Id för hela projektet. Du kan lägga till flera ”plattformar” i en varje projekt och tillhandahåller data som är lämpliga för varje plattform som du lägger till. Naturligtvis kan du skapa så många appar som du vill beroende på dina krav, men endast en program-Id för flesta fall bör vara nödvändig.

Vårt syfte är att det kommer leda till ett mer förenklad hantering och utveckling och skapa en mer samlad vy över ett enda projekt som du kanske arbetar på.

## <a name="scopes-not-resources"></a>Scope, inte resurser

I Azure AD en app kan fungera som en **resource**, eller en mottagare av token. En resurs kan definiera ett antal **scope** eller **oAuth2Permissions** som den förstår, vilket gör att klientdatorer appar att begära token till den resursen för en viss uppsättning omfång. Överväg att Azure AD Graph-API som ett exempel på en resurs:

* Resurs-ID eller `AppID URI`: `https://graph.windows.net/`
* Scope, eller `OAuth2Permissions`: `Directory.Read`, `Directory.Write`osv. 

Allt detta gäller för v2.0-slutpunkten. En app kan fortfarande fungera som resursen, definiera omfång och identifieras av en URI. Klientappar kan fortfarande att begära åtkomst till dessa omfattningar. Men har det sätt som en klient begär att dessa behörigheter ändrats. Tidigare var en OAuth 2.0 godkänna begäran till Azure AD kan har tittat som:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

där den **resource** parametern anges vilken resurs som klientappen begär auktorisering för. Azure AD beräknade de behörigheter som krävs av appen utifrån statisk konfiguration i Azure portal och därefter utfärdade token. Nu kan godkänna samma OAuth 2.0 begäran ut:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

där den **omfång** parametern indikerar vilken resurs och behörigheter appen begär auktorisering för. Önskad resurs finns kvar i begäran - det bara finns i vart och ett av värdena för omfångets parameter. Använda omfattningsparametern i det här sättet kan v2.0-slutpunkten är mer kompatibel med OAuth 2.0-specifikationen och överensstämmer bättre med vanliga metoder i branschen. Dessutom kan appar att utföra [inkrementella medgivande](#incremental-and-dynamic-consent), vilket beskrivs i nästa avsnitt.

## <a name="incremental-and-dynamic-consent"></a>Inkrementell och dynamiska medgivande

Appar som är registrerade i Azure AD som tidigare behövs för att ange sina behörigheter som krävs för OAuth 2.0 i Azure-portalen vid tidpunkten för skapandet av app:

![Behörigheter för registrering av Användargränssnittet](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

De behörigheter som en app som krävs har konfigurerats **statiskt**. När detta tillåts av konfigurationen av appen finns i Azure-portalen och hålls koden bra och enkelt, visar några problem för utvecklare:

* En app behövde känner till alla de behörigheter som den skulle behöver vid tidpunkten för skapandet av appen. Lägger till behörigheter med tiden var svåra.
* En app behövde känner till alla de resurser som den skulle någonsin åt förbereds i förväg. Det var svårt att skapa appar som kan komma åt ett valfritt antal resurser.
* En app var tvunget att begära alla behörigheter som den skulle behöver vid användarens första inloggning. I vissa fall detta ledde till en lång lista med behörigheter som det rekommenderas inte slutanvändare från att godkänna appens åtkomst på första inloggning.

Med v2.0-slutpunkten kan du ange behörigheterna som dina appbehov **dynamiskt**, vid körning, under normala användning av din app. Om du vill göra det, du kan ange omfång som din app behöver vid en given tidpunkt i tid genom att inkludera dem i den `scope` -parametern för en begäran om godkännande:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Behörigheten för appen att läsa katalogdata i en Azure AD-användare, och även skriva data till sin katalog ovan begäranden. Om användaren har samtyckt till dessa behörigheter i förflutna för den här viss app, de ska ange sina autentiseringsuppgifter och vara inloggad i appen. Om användaren inte har godkänt att någon av dessa behörigheter, uppmanas v2.0-slutpunkten användaren medgivande till att dessa behörigheter. Om du vill veta mer kan du läsa mer på [behörigheter och samtycke scope](v2-permissions-and-consent.md).

Så att en app för att begära behörighet dynamiskt via den `scope` parametern ger dig fullständig kontroll över din användarupplevelse. Om du vill kan välja du att frontload ditt medgivande upplevelse och ber om alla behörigheter i en inledande auktoriseringsbegäran. Eller om din app kräver ett stort antal behörigheter, kan du samla in dessa behörigheter från användaren stegvis, så de försöker använda vissa funktioner i din app med tiden.

## <a name="well-known-scopes"></a>Välkända scope

### <a name="offline-access"></a>Offline-åtkomst

Appar med hjälp av v2.0-slutpunkten kan kräva användning av en ny välkända behörighet för appar - den `offline_access` omfång. Alla appar måste begära denna behörighet om de behöver för att komma åt resurser på uppdrag av en användare under en långvarig tid, även om användaren inte kanske aktivt använder appen. Den `offline_access` omfattning visas för användaren i dialogrutor för användarmedgivande som ”åtkomst till dina data offline”, som användaren måste samtycka till. Begär den `offline_access` behörighet kan din webbapp tar emot OAuth 2.0 refresh_tokens från v2.0-slutpunkten. Refresh_tokens är långlivade och kan utbyta för nya OAuth 2.0-access_tokens under långa perioder av åtkomst. 

Om din app inte begär det `offline_access` omfattning, och tar inte emot refresh_tokens. Det innebär att när du löser in ett authorization_code i OAuth 2.0-auktoriseringskodflödet du endast får tillbaka ett access_token från den `/token` slutpunkt. Den access_token förblir giltig för en kort tidsperiod (vanligtvis en timme), men till slut upphör att gälla. AT tidpunkt, din app behöver du därefter skickas användarna tillbaka till den `/authorize` slutpunkt för att hämta en ny authorization_code. Under den här omdirigering, användaren kanske eller kanske inte behöver ange dem igen eller nytt medgivanden till behörigheter, beroende på vilken typ av app.

Om du vill veta mer om OAuth 2.0 och refresh_tokens access_tokens kan ta en titt på [protokollreferens för v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID-profil och e-post

Historiskt sett har skulle mest grundläggande OpenID Connect-inloggning flödet med Azure AD uppge en mängd information om användaren i den resulterande id_token. Anspråk i en id_token kan innehålla användarens namn, önskad användarnamn, e-postadress, objekt-ID och mer.

Informationen som den `openid` omfång får din app åtkomst till är nu begränsade. Den `openid` omfång kan din app för att logga in användaren och ta emot en appspecifika identifierare för användaren. Om du vill hämta personliga data om användare i din app, måste din app och begär ytterligare behörighet från användaren. Två nya scope – den `email` och `profile` omfång – gör att du kan begära ytterligare behörighet.

Den `email` omfånget gör din appåtkomst till användarens primära e-postadress via den `email` anspråk i id_token. 

Den `profile` omfång får din appåtkomst till andra grundläggande information om användare, deras namn, önskad användarnamn, objekt-ID och så vidare.

Du kan koda din app på en minimal avslöjande sätt – endast kan du be användaren uppsättning information att din app kräver för att utföra sitt arbete. Mer information om dessa scope finns [omfattningsreferens v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Tokenanspråken

Anspråk i token som utfärdas av v2.0-slutpunkten kan inte vara identiskt med token som utfärdas av den allmänt tillgängliga Azure AD-slutpunkter. Appar som migrerar till den nya tjänsten kan inte utgå från ett visst anspråk kommer att finnas i id_tokens eller access_tokens. Mer information om specifika anspråk som släpps i v2.0 token, finns det [v2.0 tokenreferens](v2-id-and-access-tokens.md).

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar vara medvetna om när du använder den v2.0-punkten. Läs om någon av dessa begränsningar gäller för ditt specifika scenario i den [v2.0 begränsningar doc](active-directory-v2-limitations.md).
