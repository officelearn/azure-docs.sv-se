---
title: Använda Azure AD v2.0 att komma åt säkra resurser utan användaråtgärder | Microsoft Docs
description: Skapa webbprogram med hjälp av Azure AD-implementeringen av OAuth 2.0-autentiseringsprotokollet.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 02d5bf9ef293731ce707596a90cd7e9aa0b96450
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581889"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0- och OAuth 2.0 flödet
Du kan använda den [beviljande av autentiseringsuppgifter för OAuth 2.0-klient](http://tools.ietf.org/html/rfc6749#section-4.4) anges i RFC 6749, även kallad *tvåledade OAuth*, för att få åtkomst till webb-värdbaserade resurser med hjälp av identiteten för ett program. Den här typen av ge ofta används för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. Dessa typer av program som ofta kallas *daemons* eller *tjänstkonton*.

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

I en typisk *treledade OAuth*, ett klientprogram som har beviljats behörighet att komma åt en resurs för en viss användares räkning. Behörigheten har delegerats från användaren till programmet, vanligtvis under den [godkänna](v2-permissions-and-consent.md) processen. I flödet, men beviljas behörigheter direkt till själva programmet. När appen anger hur många en token till en resurs måste resursen tillämpar som själva appen har behörighet att utföra en åtgärd och inte som har användaren behörighet.

## <a name="protocol-diagram"></a>Protokollet diagram
Hela flödet ser ut ungefär så i nästa diagram. Vi beskriver varje steg nedan.

![Flödet](./media/v2-oauth2-client-creds-grant-flow/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Få direkt behörighet
En app vanligtvis tar emot direkta tillstånd att komma åt en resurs i ett av två sätt: via en åtkomstkontrollista (ACL) på resursen eller behörighet programtilldelning i Azure Active Directory (AD Azure). Dessa två metoder är de vanligaste i Azure AD, och vi rekommenderar dem för klienter och resurser för klienten flödet. En resurs kan välja att ge sina kunder på annat sätt, men. Varje resurs-server kan välja den metod som passar bäst för sina program.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
En resursprovider kan tvinga en kontroll av auktorisering baserat på en lista med program-ID som den vet och ger en viss nivå av åtkomst till. När resursen tar emot en token från v2.0-slutpunkten, den kan avkoda token och extrahera klientens program-ID från den `appid` och `iss` anspråk. Den jämför sedan programmet mot en ACL som den upprätthåller. ACL granularitet och metoden kan variera avsevärt mellan resurser.

Ett vanligt användningsfall är att använda en ACL ska kunna köra tester för ett webbprogram eller för ett webb-API. Webb-API kan bevilja endast en delmängd av fullständig behörighet till en viss klient. Skapa en Testklient som skaffar token från v2.0-slutpunkten och skickar dem till API: et för att köra slutpunkt till slutpunkt-testerna på API: et. API: et kontrollerar sedan Åtkomstkontrollistan för test-klientens program-ID för fullständig åtkomst till API: ets hela funktioner. Om du använder den här typen av ACL, måste du verifiera inte bara anroparens `appid` värde. Kontrollera också att den `iss` värdet av token är betrodd.

Den här typen av auktorisering är vanligt för Daemon och tjänstkonton som behöver komma åt data som ägs av konsumentanvändare med personliga Microsoft-konton. För data som ägs av organisationer, rekommenderar vi att du får nödvändiga auktoriseringen via behörigheter för programmet.

### <a name="application-permissions"></a>Programbehörigheter
Du kan använda API: er för att exponera en uppsättning behörigheter för programmet istället för att använda ACL: er. Ett program behörighet har beviljats till ett program av en organisations administratör och kan användas endast för att komma åt data som ägs av organisationen och deras anställda. Microsoft Graph visar till exempel flera behörigheter för programmet att göra följande:

* Läsa e-post i alla postlådor
* Läsa och skriva e-post i alla postlådor
* Skicka e-post som valfri användare
* Läsa katalogdata

Mer information om behörigheter för programmet går du till [Microsoft Graph](https://graph.microsoft.io).

Gör stegen som beskrivs i nästa avsnitt för att använda behörigheter för programmet i din app.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörighet i portalen för registrering av app
1. Gå till ditt program i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller [skapa en app](quickstart-v2-register-an-app.md), om du inte redan har gjort. Du måste använda minst en Programhemlighet när du skapar din app.
2. Leta upp den **Microsoft Graph-behörigheter** , och lägger sedan till den **programbehörigheter** som din app kräver.
3. **Spara** appregistreringen.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren till din app
När du skapar ett program som använder behörigheter för programmet kräver normalt appen en sida eller vy som administratören har godkänt dess behörigheter. Den här sidan kan vara en del av appens inloggning flöde, en del av appens inställningar, eller så kan vara ett dedikerat ”ansluta”-flöde. I många fall kan det vara bra för appen ska visa detta ”ansluta” Visa endast när en användare har loggat in med ett arbets- eller din skola Microsoft-konto.

Om du logga in användaren till din app kan du identifiera organisationen som användaren tillhör innan du be användaren att godkänna behörigheterna som programmet. Även om de inte är absolut nödvändigt, det kan hjälpa dig att skapa en mer intuitiv upplevelse för användarna. Om du vill registrera användare i följa våra [v2.0-protokollet självstudier](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en directory-administratör
När du är redo att begära behörighet från organisationens administratör kan du omdirigera användaren till v2.0 *medgivande adminslutpunkten*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Directory-klient som du vill begära behörighet från. Detta kan vara i GUID eller eget namnformat. Om du inte vet vilken klient som användaren tillhör och du vill låta dem logga in med valfri klient, Använd `common`. |
| client_id |Krävs |Programmet med ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| redirect_uri |Krävs |Omdirigerings-URI där du vill att svaret skickas för din app för att hantera. Den måste exakt matcha någon av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodad och kan ha ytterligare sökvägssegment. |
| state |Rekommenderas |Ett värde som ingår i den begäran som också returneras i token-svaret. Det kan vara en sträng med innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |

Nu tillämpar Azure AD att endast en Innehavaradministratör kan logga in att slutföra begäran. Administratören blir ombedd att godkänna alla direkt tillämpning-behörigheter som du har begärt för din app i portalen för registrering av appen.

##### <a name="successful-response"></a>Lyckat svar
Om administratören godkänner behörigheter för ditt program, lyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- | --- |
| klient |Directory-klient som beviljats de behörigheter som den begärda i GUID-format för ditt program. |
| state |Ett värde som ingår i den begäran som också returneras i token-svaret. Det kan vara en sträng med innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| admin_consent |Ange **SANT**. |

##### <a name="error-response"></a>Felsvar
Om administratören inte godkänner behörigheter för ditt program, misslyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- | --- |
| fel |En felsträng kod som du kan använda för att klassificera typer av fel, och som du kan använda för att reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett fel. |

När du har fått ett lyckat svar från slutpunkten för app-etablering, kan din app har fått de direkta programbehörigheter begärda. Nu kan du begära en token för den resurs som du vill.

## <a name="get-a-token"></a>Hämta en token
När du har köpt den nödvändiga auktoriseringen för ditt program kan du fortsätta med att hämta åtkomsttoken för API: er. För att få en token med hjälp av klienten klientautentiseringsuppgifter kan skicka en POST-begäran till den `/token` v2.0-slutpunkten:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Först för användningsfall: begäran om åtkomsttoken med en delad hemlighet

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| klient |Krävs | Directory-klient programmet planerar att arbeta mot, i GUID eller domännamn format. |
| client_id |Krävs |Programmet med ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| omfång |Krävs |Det värde som angavs den `scope` parameter i den här begäran ska vara resursidentifierare (program-ID-URI) av den resurs som du vill, har den `.default` suffix. Microsoft Graph-exemplet är värdet `https://graph.microsoft.com/.default`. Det här värdet informerar v2.0-slutpunkten att den över alla direkt tillämpning behörigheter som du har konfigurerat för din app, bör utfärda en token för de som hör till resursen som du vill använda. |
| client_secret |Krävs |Programhemlighet som du skapade för din app i portalen för registrering av appen. Klienthemlighet måste vara URL-kodat innan de skickas.|
| _typ av beviljande |Krävs |Måste vara `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: begäran om åtkomsttoken med ett certifikat

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| klient |Krävs | Directory-klient programmet planerar att arbeta mot, i GUID eller domännamn format. |
| client_id |Krävs |Programmet med ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| omfång |Krävs |Det värde som angavs den `scope` parameter i den här begäran ska vara resursidentifierare (program-ID-URI) av den resurs som du vill, har den `.default` suffix. Microsoft Graph-exemplet är värdet `https://graph.microsoft.com/.default`. Det här värdet informerar v2.0-slutpunkten att den över alla direkt tillämpning behörigheter som du har konfigurerat för din app, bör utfärda en token för de som hör till resursen som du vill använda. |
| client_assertion_type |obligatorisk |Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obligatorisk | Ett intyg (en JSON Web Token) som du behöver för att skapa och signera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig hur du registrerar ditt certifikat och format för kontrollen.|
| _typ av beviljande |Krävs |Måste vara `client_credentials`. |

Observera att parametrarna är nästan samma sätt som i fallet med begäran från delad hemlighet förutom att parametern client_secret ersätts av två parametrar: en client_assertion_type och client_assertion.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar ut så här:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomst-token. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel till ett webb-API. |
| token_type |Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är `bearer`. |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |

### <a name="error-response"></a>Felsvar
Ett felsvar ser ut så här:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typer av fel som inträffar och reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan underlätta diagnostik. |
| tidsstämpel |Den tid då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan underlätta diagnostik. |
| correlation_id |En unik identifierare för begäran som kan underlätta diagnostik för komponenter. |

## <a name="use-a-token"></a>Använda en token
Nu när du har skaffat en token, kan du använda token för att göra begäranden till resursen. När token upphör att gälla, upprepa begäran till den `/token` slutpunkt för att hämta en ny åtkomsttoken.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Kodexempel
Ett exempel på ett program som implementerar beviljande av klientautentiseringsuppgifter för med hjälp av administratören godkänna slutpunkt finns i vår [v2.0-kodexempel som daemon](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
