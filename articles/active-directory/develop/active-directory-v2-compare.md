---
title: "Vad är olika i Azure AD v2.0-slutpunkten? | Microsoft Docs"
description: "En jämförelse mellan den ursprungliga Azure AD och v2.0-slutpunkter."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 4216c2215f494a81935cd4cdca52427b0cbac143
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="whats-different-about-the-v20-endpoint"></a>Vad är skillnaden mellan v2.0-slutpunkten?
Om du är bekant med Azure Active Directory eller ha integrerade appar med Azure AD tidigare, kan det finnas vissa skillnader i v2.0-slutpunkten som du inte förväntar dig.  Det här dokumentet visar dessa skillnader för din förståelse.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft-konton och Azure AD-konton
V2.0-slutpunkten kan utvecklare skriva appar som accepterar logga in från både Microsoft Accounts och Azure AD-konton med hjälp av en enda auth-slutpunkt.  Detta ger dig möjlighet att skriva din app helt konto-oberoende; Det kan vara ignorant för typ av konto som användaren loggar in med.  Naturligtvis kan du *kan* göra appen känner till vilken typ av konto som används i en viss session, men du behöver.

Till exempel om din app anropar den [Microsoft Graph](https://graph.microsoft.io), vissa ytterligare funktioner och data kommer att användas företaget, till exempel sina SharePoint-webbplatser eller katalogdata.  Men för många åtgärder, t.ex [läsa en användares e-post](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), koden kan skrivas exakt samma för både Microsoft Accounts och Azure AD-konton.  

Integrera din app med Azure AD och Microsoft Accounts är nu en enkel process.  Du kan använda en enda uppsättning slutpunkter, ett enda bibliotek och en enda app-registrering för att få åtkomst till både klienten och enterprise.  Mer information om v2.0-slutpunkten kolla [översikten](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Portalen för registrering av nya app
Om du vill registrera en app som fungerar med v2.0-slutpunkten måste du använda en ny app-portalen för registrering: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Detta är portal där du kan hämta ett program-ID, anpassa utseendet på inloggningssidan för din app och mycket mer.  Allt du behöver åtkomst till portalen är ett påslagen microsoftkonto - personlig eller arbete/Skol-konto.

## <a name="one-app-id-for-all-platforms"></a>En app-ID för alla plattformar
Om du har använt Azure Active Directory, har du förmodligen registrerad flera olika program för ett projekt.  Till exempel om du har skapat både en webbplats och en iOS-app var du tvungen att registrera dem separat, med två olika program-ID: N. Portalen för registrering av Azure AD app måste du göra denna skillnad under registreringen:

![Gamla programregistrering UI](../../media/active-directory-v2-flows/old_app_registration.PNG)

På samma sätt om du har en webbplats och en serverdel webb-api, kanske du har registrerat varje som en separat app i Azure AD.  Eller om du en iOS-app och en Android-app måste du även kan har registrerat två olika appar.  Registrering av varje komponent i ett program ledde till vissa oväntat beteende för utvecklare och sina kunder:

* Varje komponent visades som en separat app i Azure Active Directory-klient för varje kund.
* När en Innehavaradministratör försökte tillämpa principen för att hantera åtkomst till eller ta bort en app, måste de göra det för varje komponent i appen.
* När kunder samtyckt till ett program, visas varje komponent på skärmen medgivande som olika program.

Med v2.0-slutpunkten du nu registrera alla komponenter i ditt projekt som en enda app-registrering och använder ett enda program-Id för hela projektet.  Du kan lägga till flera ”plattformar” i en varje projekt och ange lämpliga data för varje plattform som du lägger till.  Naturligtvis kan du skapa så många appar som du vill beroende på dina krav, men bara en program-Id för flesta fall bör vara nödvändigt.

Vårt mål är att det kommer leda till ett mer förenklad hantering och utvecklingsarbetet och skapa en mer samlad vy över ett projekt som du kanske arbetar på.

## <a name="scopes-not-resources"></a>Scope, inte resurser
I Azure Active Directory, en app kan fungera som en **resurs**, eller en mottagare av token.  En resurs kan definiera ett antal **scope** eller **oAuth2Permissions** som kan användas, vilket gör att klientdatorer appar att begära token till resursen för en viss uppsättning scope.  Överväg att Azure AD Graph-API som ett exempel på en resurs:

* Resurs-ID eller `AppID URI`:`https://graph.windows.net/`
* Scope, eller `OAuth2Permissions`: `Directory.Read`, `Directory.Write`osv.  

Allt detta gäller för den v2.0-slutpunkten.  En app kan fortfarande fungera som resurs, definiera omfång och identifieras av en URI.  Klientprogram kan fortfarande att begära åtkomst till sådana omfattningar.  Men har det sätt som en klient begär de behörigheterna som ändrats.  Tidigare auktorisera en OAuth 2.0 kan har sökt begäran till Azure AD som:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

där den **resurs** parameter anges vilken resurs som klientappen begär tillstånd för.  Azure AD beräknas de behörigheter som krävs av appen baserat på statisk konfiguration i Azure-portalen och därefter utfärdade token.  Nu godkänna samma OAuth 2.0 begäran ser ut som:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

där den **omfång** parametern anger vilken resurs och behörigheter appen begär tillstånd för. En resurs finns fortfarande mycket i begäran - finns bara i värden för parametern omfång.  Använda omfattningsparametern i det här sättet kan v2.0-slutpunkten är mer kompatibel med OAuth 2.0-specifikationen och justerar närmare med vanliga branschens praxis.  Det gör också att appar att utföra [inkrementell medgivande](#incremental-and-dynamic-consent), vilket beskrivs i nästa avsnitt.

## <a name="incremental-and-dynamic-consent"></a>Inkrementell och dynamiska medgivande
Appar som är registrerad i Azure AD som tidigare behövs för att ange behörigheterna som krävs för OAuth 2.0 i Azure-portalen vid tidpunkten för skapandet av app:

![Behörigheter för registrering av Användargränssnittet](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

De behörigheter som har konfigurerats i en app krävs **statiskt**.  När detta tillåts konfigurationen av appen finns i Azure Portal och hålls koden nice och enkel uppstår några problem för utvecklare:

* En app behövt känner till alla de behörigheter som behöver någonsin vid tidpunkten för skapandet av appen.  Lägga till behörigheter över tid var svåra.
* En app behövt känner till alla resurser som den skulle någonsin komma i förväg.  Det var svårt att skapa appar som kan komma åt ett godtyckligt antal resurser.
* En app var tvunget att begära alla behörigheter som behöver någonsin vid användarens första inloggning.  I vissa fall kan detta ledde till en lång lista med behörigheter som rekommenderas inte slutanvändare från godkänner appens åtkomst på första inloggning.

Med v2.0-slutpunkten kan du ange behörigheterna som din app måste **dynamiskt**, vid körning under vanlig användning av din app.  Om du vill göra det, du kan ange omfång som din app måste vid en viss tidpunkt genom att inkludera dem i den `scope` -parametern för en begäran om godkännande:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Ovanstående begäranden behörigheten för appen att läsa katalogdata i en Azure AD-användare, samt skriva data till sina kataloger.  Om användaren har godkänt för dessa behörigheter i förflutna för den här specifika appen, kommer bara att ange sina autentiseringsuppgifter och vara inloggad i appen.  Om användaren inte har godkänt till någon av dessa behörigheter, frågar v2.0-slutpunkten användaren om medgivande till dessa behörigheter.  Om du vill veta mer kan du läsa mer om [behörigheter, medgivande och scope](active-directory-v2-scopes.md).

Att tillåta en app begär behörighet dynamiskt via den `scope` parametern ger dig fullständig kontroll över din användarupplevelse.  Om du vill kan välja du att frontload ditt medgivande får och fråga efter alla behörigheter i en begäran för ursprungliga tillståndet.  Eller om din app kräver ett stort antal behörigheter, kan du samla in dessa behörigheter från användaren inkrementellt, när de försöker använda vissa funktioner i din app med tiden.

## <a name="well-known-scopes"></a>Välkända scope
#### <a name="offline-access"></a>Offline-åtkomst
Appar med v2.0-slutpunkten kan kräva användning av en ny välkända behörighet för appar – den `offline_access` omfång.  Alla appar måste du begära denna behörighet om de behöver för att komma åt resurser på uppdrag av en användare för en längre tidsperiod, även när användaren inte aktivt använder appen.  Den `offline_access` omfattningen visas för användaren i dialogrutor medgivande som ”åtkomst till data offline” som användaren måste acceptera att.  Begär den `offline_access` behörighet kan ditt webbprogram att ta emot OAuth 2.0 refresh_tokens från v2.0-slutpunkten.  Refresh_tokens är långlivade och kan utbyta för nya OAuth 2.0-access_tokens under långa perioder åtkomst.  

Om din app inte begär den `offline_access` omfång, får den inte refresh_tokens.  Det innebär att när du lösa in en authorization_code i OAuth 2.0-auktoriseringskodflödet endast får du tillbaka en access_token från den `/token` slutpunkt.  Den access_token förblir giltig för en kort tidsperiod (vanligtvis en timme), men till slut upphör att gälla.  Vid tidpunkten, din app behöver du omdirigera användaren tillbaka till den `/authorize` slutpunkten för att hämta en ny authorization_code.  Under den här omdirigera användaren kan eller inte behöva ange sina autentiseringsuppgifter igen eller nytt samtycker till att behörigheter, beroende på den typen av app.

Mer information om OAuth 2.0 och refresh_tokens access_tokens ta en titt på [protokollreferens för v2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile-and-email"></a>OpenID profil och e-post
Tidigare skulle mest grundläggande OpenID Connect inloggning flödet med Azure Active Directory innehåller en mängd information om användaren i den resulterande id_token.  Anspråk i en id_token kan innehålla användarens namn, prioriterade användarnamn, e-postadress, objekt-ID och mer.

Vi nu är att begränsa informationen som den `openid` omfång ger din appåtkomst till.  Området 'openid' kommer endast att din app kan logga in användaren i och ta emot en appspecifika identifierare för användaren.  Om du vill hämta personligt identifierbar information (PII) om användaren i din app behöver din app och begär ytterligare behörighet från användaren.  Vi introducerar två nya scope – den `email` och `profile` scope – som gör att du gör.

Den `email` scope är mycket enkelt – den kan din appåtkomst till användarens primära e-postadress via den `email` anspråk i id_token.  Den `profile` omfång ger din appåtkomst till andra grundläggande information om användare – deras namn, prioriterade username, objekt-ID och så vidare.

Du kan code din app på en minimal avslöjande sätt – endast kan du be användaren för informationen att din app kräver för att utföra sitt jobb.  Mer information om dessa scope avser [omfattningsreferens v2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Token anspråk
Anspråk i token som utfärdats av v2.0-slutpunkten inte vara identiskt med token som utfärdats av den allmänt tillgängliga Azure AD-slutpunkter – appar migrera till den nya tjänsten kan inte utgå från ett visst anspråk kommer att finnas i id_tokens eller access_tokens. Mer information om specifika anspråk som hänvisas till i v2.0-token, finns det [v2.0 tokenreferens](active-directory-v2-tokens.md).

## <a name="limitations"></a>Begränsningar
Det finns några begränsningar vara medveten om när du använder v2.0-punkten.  Mer information finns i [v2.0 begränsningar doc](active-directory-v2-limitations.md) att se om någon av dessa begränsningar gäller för ditt specifika scenario.
