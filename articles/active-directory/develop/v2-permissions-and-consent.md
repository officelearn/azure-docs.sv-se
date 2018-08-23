---
title: Azure Active Directory v2.0 omfattningar, behörigheter och godkännande | Microsoft Docs
description: En beskrivning av auktorisering i Azure AD v2.0-slutpunkten, inklusive omfattningar, behörigheter och godkännande.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 6d3847f547646ae7c62f98b4cee716af5c6ba5e9
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42055880"
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Omfattningar, behörigheter och godkännande i Azure Active Directory v2.0-slutpunkten
Appar som integreras med Azure Active Directory (Azure AD) följer en modell för auktorisering som ger användare kontroll över hur en app kan komma åt sina data. V2.0-implementeringen av auktoriseringsmodellen som har uppdaterats, och den ändras hur en app måste interagera med Azure AD. Den här artikeln beskriver de grundläggande principerna för den här auktoriseringsmodellen, inklusive omfattningar, behörigheter och godkännande.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Omfång och behörigheter
Azure AD implementerar den [OAuth 2.0](active-directory-v2-protocols.md) auktoriseringsprotokoll. OAuth 2.0 är en metod som en app från tredje part kan komma åt webb-värdbaserade resurser för en användares räkning. Någon webbaserat resurs som kan integreras med Azure AD har en resurs-ID eller *program-ID-URI*. Några av Microsofts webbaserat resurser är till exempel:

* Office 365 Unified e API: `https://outlook.office.com`
* Azure AD Graph-API: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Detsamma gäller för resurser från tredje part som har integrerat med Azure AD. Någon av följande resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp funktionerna i den här resursen i mindre segment. Till exempel [Microsoft Graph](https://graph.microsoft.io) har definierat behörighet att utföra följande uppgifter, bland annat:

* Läsa en användares kalender
* Skriva till en användares kalender
* Skicka e-post som en användare

Genom att definiera dessa typer av behörigheter för har resursen detaljerad kontroll över sina data och hur data visas. En app från tredje part kan begära dessa behörigheter från en appanvändare. Appanvändare måste godkänna behörigheterna som innan appen kan agera å användarens vägnar. Genom att dela upp resursens funktionen till mindre behörighetsuppsättningar, kan appar från tredje part byggas om du vill begära endast specifika behörigheter som de behöver för att fungera. App-användare kan veta exakt hur en app ska använda sina data och de kan vara mer säker på att appen inte fungerar med skadliga avsikter.

I Azure AD och OAuth, kallas dessa typer av behörigheter *scope*. De också är kallas ibland *oAuth2Permissions*. Ett omfång representeras i Azure AD som ett strängvärde. Fortsättning på Microsoft Graph-exemplet, är scope-värdet för varje behörighet:

* Läsa en användares kalender med hjälp av `Calendars.Read`
* Skriva till en användares kalender genom att använda `Calendars.ReadWrite`
* Skicka e-post som en användare med hjälp av `Mail.Send`

En app kan begära dessa behörigheter genom att ange omfång i begäranden till v2.0-slutpunkten.

## <a name="openid-connect-scopes"></a>OpenID Connect-scope
V2.0-implementeringen av OpenID Connect har några väldefinierade scope som inte gäller för en specifik resurs: `openid`, `email`, `profile`, och `offline_access`.

### <a name="openid"></a>openid
Om en app utför logga in med hjälp av [OpenID Connect](active-directory-v2-protocols.md), den måste begära den `openid` omfång. Den `openid` omfång visas på sidan work medgivande som behörigheten ”logga du in” och på samtyckessida för personliga Microsoft-konto som ”visa din profil och ansluta till appar och tjänster med ditt Microsoft-konto”-behörighet. Med den här behörigheten kan en app kan ta emot en unik identifierare för användaren i form av den `sub` anspråk. Det ger också åtkomst till appen till slutpunkten användarinformationen. Den `openid` omfång som kan användas i token v2.0-slutpunkten för att hämta ID-token som kan användas för att säkra HTTP-anrop mellan olika komponenter i en app.

### <a name="email"></a>e-post
Den `email` omfång kan användas med den `openid` omfång och alla andra. Den ger appen åtkomst till användarens primära e-postadress i form av den `email` anspråk. Den `email` anspråk som ingår i en token endast om en e-postadress är associerad med det användarkonto som inte är alltid fallet. Om den använder den `email` omfattning, din app ska vara beredd att hantera ett fall där den `email` anspråk finns inte i token.

### <a name="profile"></a>profil
Den `profile` omfång kan användas med den `openid` omfång och alla andra. Den ger appen åtkomst till en stor mängd information om användaren. Informationen om den kan komma åt omfattar, men är inte begränsad till användarens förnamn, efternamn, primära användarnamn och objekt-ID. En fullständig lista över anspråk för profilen som är tillgängliga i parametern id_tokens för en viss användare ser den [v2.0 tokens referens](v2-id-and-access-tokens.md).

### <a name="offlineaccess"></a>offline_access
Den [ `offline_access` omfång](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din appåtkomst till resurser å användarens vägnar en längre tid. På sidan work medgivande visas det här omfånget som ”åt dina data när som helst”-behörighet. På personliga Microsoft-konto medgivande sidan visas den som ”åt din information när som helst”-behörighet. När en användare godkänner den `offline_access` omfattning, din app kan ta emot uppdaterings-tokens från token v2.0-slutpunkten. Uppdateringstoken är långlivade. Din app kan hämta nya åtkomsttoken som äldsta förfaller.

Om din app inte begär det `offline_access` omfattning, det inte tar emot uppdateringstoken. Detta innebär att när du har löst in en auktoriseringskod i den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md), får du endast en åtkomsttoken från den `/token` slutpunkt. Åtkomsttoken är giltig för en kort tid. Åtkomsttoken upphör vanligtvis i en timme. AT att punkt, din app måste därefter skickas användarna tillbaka till den `/authorize` slutpunkten för att få en ny auktoriseringskod. Under den här omdirigering, beroende på typen av app måste behöva användaren ange sina autentiseringsuppgifter igen eller godkänna igen behörigheter.

Mer information om hur du hämtar och använder uppdateringstoken finns i den [protokollreferens för v2.0](active-directory-v2-protocols.md).

## <a name="accessing-v10-resources"></a>Åtkomst till v1.0 resurser
v2.0-program kan begära token och medgivande för v1.0 program (till exempel Power BI-API: et `https://analysis.windows.net/powerbi/api` eller Sharepoint API `https://{tenant}.sharepoint.com`).  För att göra det, kan du referera till appen URI och omfång strängen i den `scope` parametern.  Till exempel `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All` skulle begär PowerBI `View all Datasets` behörighet för ditt program. 

Om du vill begära flera behörigheter att lägga till hela URI: N med ett blanksteg eller `+`, t.ex. `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://analysis.windows.net/powerbi/api/Report.Read.All`.  Detta begär både den `View all Datasets` och `View all Reports` behörigheter.  Observera att precis som med alla Azure AD-scope och behörigheter för program kan bara göra en begäran till en resurs i taget – det begäran `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://api.skypeforbusiness.com/Conversations.Initiate`, som begär både Power BI `View all Datasets` behörighet och Skype för företag `Initiate conversations` behörighet, avvisas på grund av begär behörigheter på två olika resurser.  

### <a name="v10-resources-and-tenancy"></a>V1.0 resurser och innehavare
Både v1.0 och v2.0 Azure AD-protokoll använder sig av en `{tenant}` parametern bäddas in i URI: N (`https://login.microsoftonline.com/{tenant}/oauth2/`).  När du använder v2.0-slutpunkten för att komma åt en resurs med v1.0 organisationens den `common` och `consumers` klienter kan inte användas eftersom de här resurserna kan bara kommas åt med organisationens (Azure AD) konton.  Därför vid åtkomst till dessa resurser, endast klienten GUID eller `organizations` kan användas som den `{tenant}` parametern.  

Om ett program försöker komma åt en organisations v1.0-resurs med hjälp av en felaktig klient, returneras ett fel som liknar den nedan. 

`AADSTS90124: Resource 'https://analysis.windows.net/powerbi/api' (Microsoft.Azure.AnalysisServices) is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.`


## <a name="requesting-individual-user-consent"></a>Begär användargodkännande för enskilda
I en [OpenID Connect eller OAuth 2.0](active-directory-v2-protocols.md) auktorisering begär att en app kan begära de behörigheter som krävs med hjälp av den `scope` frågeparameter. Till exempel när en användare loggar in på en app, appen skickar en begäran som i följande exempel (med radbrytningar har lagts till för läsbarhet):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Den `scope` parametern är en blankstegsavgränsad lista med omfattningar som appen begär. Varje område anges genom att lägga till scope-värde till resursidentifierare (program-ID: T URI). I exemplet begäran måste appen behörighet att läsa användarens kalendern och skicka e-post som användaren.

När användaren anger sina autentiseringsuppgifter, v2.0-slutpunkten söker efter matchande koll på *användarmedgivande*. Om användaren inte har godkänt att någon av behörigheterna som krävs i förflutna, ombeds användaren att bevilja behörigheterna som krävs i v2.0-slutpunkten.

![Arbeta konto medgivande](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighet, registreras samtycke så att användaren inte behöver godkänna igen på efterföljande kontoinloggningar.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begär godkännande för en hel-klient
Ofta, när en organisation köper en licens eller prenumeration för ett program kan vill organisationen etablera fullständigt programmet för sina anställda. Som en del av den här processen kan kan en administratör ge samtycke för programmet att fungera som ombud anställda. Om administratören ger ditt medgivande för hela klientorganisationen, ser organisationens anställda inte en samtyckessida för programmet.

Om du vill begära godkännande för alla användare i en klient, kan din app använder medgivande admin-slutpunkten.

## <a name="admin-restricted-scopes"></a>Begränsat omfång
Vissa höga behörigheter i Microsofts ekosystem kan anges till *begränsat*. Exempel på dessa typer av scope är följande behörigheter:

* Läs katalogdata i en organisation med hjälp av `Directory.Read`
* Skriva data till en organisations katalog med hjälp av `Directory.ReadWrite`
* Läsa säkerhetsgrupper i organisationens katalog med hjälp av `Groups.Read.All`

Även om en konsument-användare kan ge ett programåtkomst till den här typen av data, är organisationsanvändare begränsade från att bevilja åtkomst till samma uppsättning av känsliga företagsdata. Om ditt program begär åtkomst till någon av dessa behörigheter från en organisationsanvändare, får användaren ett felmeddelande som säger att de inte har behörighet att ge samtycke för din app-behörigheter.

Om din app kräver åtkomst till begränsat scope för organisationer, bör du begära dem direkt från en företagsadministratör också med hjälp av admin medgivande-slutpunkten, som beskrivs nedan.

Om en administratör ger dessa behörigheter via medgivande adminslutpunkten, beviljas medgivande för alla användare i klienten.

## <a name="using-the-admin-consent-endpoint"></a>Med hjälp av administratören medgivande slutpunkt
Om du följer dessa steg din app kan samla in behörigheter för alla användare i en klient, inklusive begränsat scope. Ett kodexempel som implementerar stegen finns i den [begränsat scope exempel](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörighet i portalen för registrering av app
1. Gå till ditt program i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller [skapa en app](quickstart-v2-register-an-app.md) om du inte redan har gjort.
2. Leta upp den **Microsoft Graph-behörigheter** , och lägger sedan till de behörigheter som din app kräver.
3. Se till att du **spara** appregistreringen.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren till din app
Vanligtvis när du skapar ett program som använder medgivande adminslutpunkten måste appen du en sida eller vy där administratören kan godkänna dess behörigheter. Den här sidan kan vara en del av appens registrering flöde, en del av appens inställningar, eller så kan vara ett dedikerat ”ansluta”-flöde. I många fall kan det vara bra för appen ska visa detta ”ansluta” Visa endast när en användare har loggat in med ett arbets- eller din skola Microsoft-konto.

När du loggar du in till din app kan identifiera du den organisation som administratören tillhör innan ber dem att godkänna behörigheterna som krävs. Även om de inte är absolut nödvändigt, det kan hjälpa dig att skapa en mer intuitiv upplevelse för din organisations användare. Om du vill registrera användare i följa våra [v2.0-protokollet självstudier](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en directory-administratör
När du är redo att begära behörighet från administratören för din organisation kan du omdirigera användaren till v2.0 *medgivande adminslutpunkten*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Directory-klient som du vill begära behörighet från. Kan anges i GUID eller eget namnformat eller med det allmänna skyddet som hänvisas med ”vanliga” som visas i exemplet. |
| client_id |Krävs |Programmet med ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| redirect_uri |Krävs |Omdirigerings-URI där du vill att svaret skickas för din app för att hantera. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen för registrering av appen. |
| state |Rekommenderas |Ett värde som ingår i den begäran som också kommer att returneras i token-svaret. Det kan vara en sträng med innehåll. Använda tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |

Azure AD kräver nu en Innehavaradministratör för att logga in att slutföra begäran. Administratören uppmanas att godkänna de behörigheter som du har begärt för din app i portalen för registrering av appen.

#### <a name="successful-response"></a>Lyckat svar
Om administratören godkänner behörigheterna för din app, lyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- | --- |
| klient |Directory-klient som beviljats de behörigheter som den begärda i GUID-format för ditt program. |
| state |Ett värde som ingår i den begäran som också kommer att returneras i token-svaret. Det kan vara en sträng med innehåll. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| admin_consent |Anges till **SANT**. |

#### <a name="error-response"></a>Felsvar
Om administratören inte godkänner behörigheterna för din app, misslyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- | --- |
| fel |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett fel. |

När du har fått ett lyckat svar från admin-slutpunkten för medgivande, kommer din app har fått de behörigheter som begärde. Därefter kan du begära en token för den resurs som du vill.

## <a name="using-permissions"></a>Med behörigheter
När användaren godkänner behörighet för din app, kan appen hämta åtkomsttoken som representerar appens behörighet att komma åt en resurs i vissa kapacitet. En åtkomsttoken kan bara användas för en enskild resurs, men kodad i åtkomsttoken är varje behörighet som din app har beviljats för den resursen. Om du vill hämta en åtkomsttoken kan appen göra en begäran v2.0 tokenslutpunkten, så här:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Du kan använda den resulterande åtkomsttoken i HTTP-förfrågningar till resursen. På ett tillförlitligt sätt betyder det att resursen att din app har rätt behörighet för att utföra en viss uppgift. 

Läs mer om OAuth 2.0-protokollet och hur du hämtar åtkomsttoken, den [protokollreferens för v2.0-slutpunkten](active-directory-v2-protocols.md).
