---
title: "Azure Active Directory v2.0-scope, behörigheter och medgivande | Microsoft Docs"
description: "En beskrivning av auktorisering i Azure AD v2.0-slutpunkten, inklusive scope, behörigheter och samtycke."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: b35e4a7619c23660d93d91219a92be7e93a35139
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Scope, behörigheter och medgivande i Azure Active Directory v2.0-slutpunkten
Appar som integreras med Azure Active Directory (AD Azure) följer en auktoriseringsmodell som ger användare kontroll över hur en app kan komma åt sina data. V2.0-implementeringen av auktoriseringsmodellen som har uppdaterats och ändras hur en app måste interagera med Azure AD. Den här artikeln beskriver de grundläggande principerna för det här tillståndet modellen, inklusive scope, behörigheter och samtycke.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Omfång och behörigheter
Azure AD-implementerar den [OAuth 2.0](active-directory-v2-protocols.md) authorization protocol. OAuth 2.0 är en metod som en app från tredje part kan komma åt webbaserat resurser för en användares räkning. Alla webbaserat resurser som kan integreras med Azure AD har ett resurs-ID eller *program-ID URI*. Till exempel omfattar vissa av Microsofts webbaserat resurser:

* Office 365 enhetlig e API: `https://outlook.office.com`
* Azure AD Graph-API: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Detsamma gäller för resurser från tredje part som har integrerat med Azure AD. Något av dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp funktionerna i den här resursen i mindre segment. Exempelvis [Microsoft Graph](https://graph.microsoft.io) har definierat behörighet att utföra följande uppgifter, bland annat:

* Läsa användarens kalender
* Skriva till en användares kalendrar
* Skicka e-post som en användare

Genom att definiera typerna av behörigheter för har resursen detaljerad kontroll över dess data och hur data visas. En tredjeparts-app kan begära dessa behörigheter från en app-användare. App-användare måste godkänna behörigheterna innan appen kan fungera för användarens räkning. Genom att dela upp resursens funktioner i mindre behörighetsgrupper kan appar från tredje part byggas om du vill begära de särskilda behörigheter som de behöver för att fungera. Användarna kan veta exakt hur en app använder sina data och de kan vara mer säker på att appen inte fungerar med skadliga åtgärder.

I Azure AD och OAuth dessa typer av behörigheter kallas *scope*. De ibland också kallas *oAuth2Permissions*. Ett omfång representeras i Azure AD som ett strängvärde. Fortsättning på exemplet Microsoft Graph, är scope-värdet för varje behörighet:

* Läsa användarens kalender med hjälp av `Calendars.Read`
* Skriva till en användares kalendrar med hjälp av `Calendars.ReadWrite`
* En användare med hjälp av genom att skicka e-post `Mail.Send`

En app kan begära dessa behörigheter genom att ange scope i begäranden till v2.0-slutpunkten.

## <a name="openid-connect-scopes"></a>OpenID Connect scope
V2.0-implementeringen av OpenID Connect har några väldefinierade omfattningar som inte gäller för en viss resurs: `openid`, `email`, `profile`, och `offline_access`.

### <a name="openid"></a>openid
Om en app utför logga in med hjälp av [OpenID Connect](active-directory-v2-protocols.md), den måste begära den `openid` omfång. Den `openid` scope visas på sidan arbete konto medgivande som behörigheten ”logga in dig på” och på personliga Microsoft-konto medgivande sida som behörigheten ”Visa din profil och ansluta till appar och tjänster med ditt Microsoft-konto”. Med den här behörigheten kan en app kan ta emot en unik identifierare för användaren i form av den `sub` anspråk. Det ger även appen åtkomst till slutpunkten användarinformationen. Den `openid` scope som kan användas i token v2.0-slutpunkten för att hämta ID-token som kan användas för att skydda http-anrop mellan olika komponenter i en app.

### <a name="email"></a>e-post
Den `email` omfång kan användas med den `openid` scope och övriga. Det ger appen åtkomst till användarens primära e-postadress i form av den `email` anspråk. Den `email` anspråk ingår i en token endast om en e-postadress som är kopplad till det användarkonto som inte alltid fallet. Om den använder den `email` omfång, din app ska vara beredd att hantera ett fall där det `email` anspråk finns inte i token.

### <a name="profile"></a>Profil
Den `profile` omfång kan användas med den `openid` scope och övriga. Det ger appen åtkomst till en stor mängd information om användaren. Den kan komma åt informationen inkluderar, men är inte begränsat till användarens förnamn, efternamn, prioriterade användarnamn och objekt-ID. En fullständig lista över profil anspråk finns i parametern id_tokens för en viss användare, finns det [v2.0 tokens referens](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
Den [ `offline_access` omfång](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din appåtkomst till resurser för användarens räkning som en längre tid. På sidan arbete konto medgivande visas detta scope som ”åtkomst till data när som helst”-behörighet. På sidan personliga Microsoft-konto medgivande visas den som ”åtkomst till din information när som helst”-behörighet. När en användare godkänner den `offline_access` omfång, din app kan ta emot uppdaterings-tokens från token v2.0-slutpunkten. Uppdateringstoken är långlivade. Din app kan få nya åtkomsttoken när äldsta upphör.

Om din app inte begär den `offline_access` omfång och kan den inte fick uppdaterings-tokens. Detta innebär att när du lösa in en Auktoriseringskoden i den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md), får du endast en åtkomst-token från den `/token` slutpunkt. Åtkomst-token är giltig för en kort tid. Åtkomst-token upphör vanligtvis på en timme. AT att punkten, din app behöver omdirigera användaren tillbaka till den `/authorize` slutpunkten för att få en ny auktoriseringskod. Under den här omdirigering, beroende på vilken typ av app måste behöva användaren ange sina autentiseringsuppgifter igen eller medgivanden igen till behörigheter.

Mer information om hur du hämtar och använder uppdaterings-tokens finns i [protokollreferens för v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Begära samtycke för enskilda användare
I en [OpenID Connect och OAuth 2.0](active-directory-v2-protocols.md) auktorisering begär en app kan begära behörigheter som krävs med hjälp av den `scope` Frågeparametern. Till exempel när en användare loggar in på en app, appen skickar en begäran som i följande exempel (med radbrytningar som lagts till för läsbarhet):

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

Den `scope` parameter är en blankstegsavgränsad lista över scope som begär appen. Varje område anges genom att lägga till scope-värde till resursens identifierare (program-ID URI). I exemplet begäran måste appen behörighet att läsa användarens kalender och skicka e-post som användaren.

När användaren anger sina autentiseringsuppgifter, v2.0-slutpunkten söker efter en matchande post av *användargodkännande*. Om användaren inte har godkänt till någon av de begärda behörigheterna tidigare, ombeds användaren att bevilja behörigheterna som begärda v2.0-slutpunkten.

![Fungerar konto medgivande](../../media/active-directory-v2-flows/work_account_consent.png)

När användaren godkänner behörigheten, registreras samtycke så att användaren inte har tillstånd igen på efterföljande kontoinloggningar.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begäran om godkännande för en hel klient
Ofta när organisationen köper en licens eller prenumeration för ett program kan vill organisationen etablera fullständigt program till sina anställda. Som en del av den här processen kan ge en administratör ditt medgivande för programmet som agerar på uppdrag av anställda. Om administratören ger ditt medgivande för hela innehavaren, ser organisationens anställda en medgivande sidan för programmet.

Om du vill begära godkännande för alla användare i en klient kan din app använder medgivande admin-slutpunkten.

## <a name="admin-restricted-scopes"></a>Begränsat scope
Vissa höga behörigheter i Microsoft-ekosystemet kan anges till *begränsat*. Exempel på dessa typer av scope är följande behörigheter:

* Läsa katalogdata i en organisation med hjälp av `Directory.Read`
* Skriva data till en organisations katalog med hjälp av `Directory.ReadWrite`
* Läsa säkerhetsgrupper i organisationens katalog med hjälp av `Groups.Read.All`

Även om en konsument-användare kan ge ett programmet åtkomst till den här typen av data, begränsad organisationens användare från att bevilja åtkomst till samma uppsättning känsliga företagsdata. Om ditt program begär åtkomst till någon av dessa behörigheter från en organisations användare, får användaren ett felmeddelande som säger att de inte har behörighet att samtycka till appens behörigheter.

Om appen kräver åtkomst till begränsat scope för organisationer kan begära du dem direkt från en företagsadministratör också med hjälp av admin medgivande slutpunkten beskrivs nedan.

När en administratör ger dessa behörigheter via medgivande adminslutpunkten, beviljas medgivande för alla användare i klienten.

## <a name="using-the-admin-consent-endpoint"></a>Med hjälp av admin medgivande-slutpunkten
Om du gör följande appen kan samla in behörigheter för alla användare i en klient, inklusive begränsat scope. Kodexempel som implementerar stegen finns i [begränsat scope exempel](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörighet i portalen för registrering av appen
1. Gå till ditt program i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller [skapa en app](active-directory-v2-app-registration.md) om du inte redan har gjort.
2. Leta upp den **Microsoft Graph behörigheter** avsnittet och Lägg sedan till de behörigheter som krävs för din app.
3. Se till att du **spara** appregistrering.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren till din app
När du skapar ett program som använder medgivande admin-slutpunkten måste appen normalt en sida eller vy som administratören kan godkänna appens behörigheter. Den här sidan kan vara en del av appens registrering flödet, en del av appinställningar, eller det kan vara ett dedikerat ”ansluta” flöde. I många fall klokt det för appen att visa detta ”ansluta” Visa endast när en användare har loggat in med ett arbets- eller skolkonto Microsoft-konto.

När du logga in användaren till din app kan du identifiera organisationen som administratören tillhör innan ber dem att godkänna behörigheterna som krävs. Även om det inte är absolut nödvändigt, hjälper som dig att skapa en mer intuitiv upplevelse för din organisations användare. Om du vill registrera användaren i Följ våra [v2.0-protokollet självstudier](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Begära behörigheter från en katalogadministratör
När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till v2.0 *medgivande adminslutpunkten*.

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

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Directory-klient som du vill begära tillstånd från. Kan anges i GUID eller format för eget namn eller allmänt refereras till med ”gemensamma” som visas i exemplet. |
| client_id |Krävs |Programmet ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| redirect_uri |Krävs |Omdirigerings-URI där du vill att svar skickas för att hantera din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen för registrering av app. |
| state |Rekommenderas |Ett värde som ingår i denna begäran kommer också att returneras i token svaret. Det kan vara en sträng med innehåll. Använda läget för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |

Azure AD kräver nu Innehavaradministratör att logga in att slutföra begäran. Administratören blir ombedd att godkänna alla behörigheter som du har begärt för din app i portalen för registrering av app.

#### <a name="successful-response"></a>Lyckat svar
Om administratören godkänner behörigheterna för din app, lyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- | --- |
| klient |Directory-klient som beviljats de behörigheter som den begärda i GUID-format för ditt program. |
| state |Ett värde som ingår i denna begäran som också kommer att returneras i token svaret. Det kan vara en sträng med innehåll. Tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| admin_consent |Kommer att ställas in **SANT**. |

#### <a name="error-response"></a>Felsvar
Om administratören inte godkänner behörigheterna för din app misslyckade svaret ser ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till felet. |

När du har fått ett lyckat svar från medgivande adminslutpunkten, har din app fått de behörigheter som begärde. Därefter kan du begära en token för den resurs du vill.

## <a name="using-permissions"></a>Med behörigheter
När användaren godkänner av behörigheter för din app, kan din app hämta åtkomsttoken som representerar appens behörighet att komma åt en resurs i vissa kapacitet. En åtkomst-token kan endast användas för en enskild resurs, men kodad i åtkomsttoken är varje behörighet som din app har beviljats för den här resursen. Om du vill få en åtkomsttoken som gör din app en begäran till v2.0 tokenslutpunkten, så här:

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

Du kan använda den resulterande åtkomst-token i HTTP-begäranden till resursen. På ett tillförlitligt sätt anger till resursen att din app har rätt behörighet att utföra en viss uppgift.  

Läs mer om OAuth 2.0-protokollet och hur du kan få åtkomst-token i [protokollreferens för v2.0-slutpunkten](active-directory-v2-protocols.md).
