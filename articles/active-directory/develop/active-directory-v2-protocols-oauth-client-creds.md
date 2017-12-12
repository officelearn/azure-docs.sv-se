---
title: "Använda Azure AD v2.0 åtkomst till säker resurser utan användaråtgärder | Microsoft Docs"
description: "Skapa webbprogram med hjälp av Azure AD-implementeringen av OAuth 2.0-autentiseringsprotokollet."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 28616657c5aae4f6ada1ec592a2a6287e8607b6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 och OAuth 2.0-klientautentiseringsuppgifter
Du kan använda den [OAuth 2.0 klientens autentiseringsuppgifter bevilja](http://tools.ietf.org/html/rfc6749#section-4.4), kallas ibland *tvåledade OAuth*, åtkomst till web-värdbaserade resurser med hjälp av identiteten för ett program. Den här typen av bevilja ofta används för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med användaren. Dessa typer av program som ofta kallas *daemons* eller *tjänstkonton*.

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

I en normal *treledade OAuth*, ett klientprogram har beviljats behörighet att komma åt en resurs för en viss användares räkning. Behörigheten är delegerad från användaren till programmet, vanligtvis under den [medgivande](active-directory-v2-scopes.md) process. Men i klientautentiseringsuppgifter behörigheter direkt till programmet. När appen med en token för en resurs måste resursen tillämpar som en enkel själva appen har behörighet att utföra en åtgärd och inte som har användaren behörighet.

## <a name="protocol-diagram"></a>Protokollet diagram
Hela klientautentiseringsuppgifter liknar nästa diagrammet. Varje steg senare i den här artikeln beskrivs.

![Klientautentiseringsuppgifter](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Hämta direkt auktorisering
En app vanligtvis tar emot direkt behörighet att komma åt en resurs i ett av två sätt: via en åtkomstkontrollista (ACL) för resursen eller programmet behörighetstilldelningen i Azure Active Directory (AD Azure). Dessa metoder är de vanligaste i Azure AD, och vi rekommenderar dem för klienter och resurser för klientens autentiseringsuppgifter flödet. En resurs kan välja att tillåta klienter på andra sätt, men. Varje resursservern kan välja den metod som är bäst för sina program.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
En resursleverantör kan tvinga en kontroll av tillståndet baserat på en lista över program-ID som den vet och ger en viss nivå av åtkomst till. När resursen får en token från v2.0-slutpunkten, det kan avkoda token och extrahera klientens program-ID från den `appid` och `iss` anspråk. Den jämför sedan programmet mot en ACL som den upprätthåller. ACL granularitet och metod kan variera avsevärt mellan resurser.

Ett vanligt användningsfall är att använda en ACL ska kunna köra tester för ett webbprogram eller för en webb-API. Webb-API kan ge en delmängd av fullständig behörighet till en viss klient. Skapa en Testklient som hämtar token från v2.0-slutpunkten och skickar dem till API: et för att köra tester för slutpunkt till slutpunkt på API: et. API: et söker ACL för test-klientens program-ID för fullständig åtkomst till hela den API-funktioner. Om du använder den här typen av ACL, måste du verifiera inte bara anroparens `appid` värde. Kontrollera också att det `iss` värdet för token är betrodd.

Den här typen av auktorisering är vanligt för Daemon och tjänstkonton som behöver åtkomst till data som ägs av konsumentanvändare med personliga Microsoft-konton. För data som ägs av organisationer rekommenderar vi att du får nödvändiga tillståndet via behörigheter för program.

### <a name="application-permissions"></a>Behörigheter för program
Du kan använda API: er för att exponera en uppsättning behörigheter för program i stället för med ACL: er. Ett program behörighet tilldelas till ett program av en organisations administratör och kan användas endast för att komma åt data som ägs av den organisationen och dess anställda. Till exempel visar Microsoft Graph flera applikationen behörighet att göra följande:

* Läsa e-post i alla postlådor
* Läsa och skriva e-post i alla postlådor
* Skicka e-post som valfri användare
* Läsa katalogdata

Mer information om behörigheter för program går du till [Microsoft Graph](https://graph.microsoft.io).

Om du vill använda behörigheter för program i din app, gör det diskuterar vi i nästa avsnitt.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörighet i portalen för registrering av appen
1. Gå till ditt program i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller [skapa en app](active-directory-v2-app-registration.md), om du inte redan har gjort. Du måste använda minst en Programhemlighet när du skapar din app.
2. Leta upp den **direkt programbehörigheter** avsnittet och Lägg sedan till de behörigheter som krävs för din app.
3. **Spara** appregistrering.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren till din app
När du skapar ett program som använder behörigheter för program kräver normalt appen en sida eller vy som administratören godkänner appens behörigheter. Den här sidan kan vara en del av appens inloggning flödet, en del av appinställningar, eller det kan vara ett dedikerat ”ansluta” flöde. I många fall klokt det för appen att visa detta ”ansluta” Visa endast när en användare har loggat in med ett arbets- eller skolkonto Microsoft-konto.

Om du logga in användaren till din app kan du identifiera organisationen som användaren tillhör innan du be användaren att godkänna behörigheter för program. Även om det inte är absolut nödvändigt, hjälper som dig att skapa en mer intuitiv upplevelse för användarna. Om du vill registrera användaren i Följ våra [v2.0-protokollet självstudier](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Begära behörigheter från en katalogadministratör
När du är redo att begära behörigheter från organisationens administratör kan du omdirigera användaren till v2.0 *medgivande adminslutpunkten*.

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
| Klient |Krävs |Directory-klient som du vill begära tillstånd från. Detta kan vara i GUID- eller format för eget namn. Om du inte vet vilken klient som användaren tillhör och du vill låta dem logga in med en klient använder `common`. |
| client_id |Krävs |Programmet ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| redirect_uri |Krävs |Omdirigerings-URI där du vill att svar skickas för att hantera din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen, förutom att det måste vara URL-kodade och det kan ha ytterligare sökvägssegment. |
| state |Rekommenderas |Ett värde som ingår i begäran som också returneras i token svaret. Det kan vara en sträng med innehåll som du vill använda. Tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |

Azure AD tillämpar nu att endast en Innehavaradministratör kan logga in att slutföra begäran. Administratören blir ombedd att godkänna alla direkt tillämpning behörigheter som du har begärt för din app i portalen för registrering av app.

##### <a name="successful-response"></a>Lyckat svar
Om administratören godkänner behörigheter för ditt program, lyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- | --- |
| Klient |Directory-klient som beviljats de behörigheter som den begärda i GUID-format för ditt program. |
| state |Ett värde som ingår i begäran som också returneras i token svaret. Det kan vara en sträng med innehåll som du vill använda. Tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| admin_consent |Ange till **SANT**. |

##### <a name="error-response"></a>Felsvar
Om administratören inte godkänner behörigheter för ditt program, misslyckade svaret ser ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- | --- |
| fel |Koden felsträng som du kan använda för att klassificera typer av fel, och som du kan använda för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till felet. |

När du har fått ett lyckat svar från slutpunkten för app-etablering, har din app fått direkt programbehörigheter begärda. Nu kan du begära en token för den resurs som du vill använda.

## <a name="get-a-token"></a>Hämta en token
När du har skaffat nödvändiga tillståndet för programmet, fortsätter du med erhålla åtkomsttoken för API: er. För att få en token med hjälp av klienten autentiseringsuppgifter bevilja kan skicka en POST-begäran till den `/token` v2.0-slutpunkten:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Först fall: token åtkomst-begäran med en delad hemlighet

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Programmet ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| Omfång |Krävs |Värdet som skickas den `scope` parameter i den här begäran ska vara Resursidentifieraren (program-ID URI) av den resurs du vill, har den `.default` suffix. Värdet är för Microsoft Graph-exempel `https://graph.microsoft.com/.default`. Det här värdet informerar om att det ska utfärda en token för de som associerats med resursen som du vill använda för alla direkt tillämpning behörigheter som du har konfigurerat för din app, v2.0-slutpunkten. |
| client_secret |Krävs |Den hemlighet som programmet som du skapade för din app i portalen för registrering av app. |
| grant_type |Krävs |Måste vara `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: token åtkomst-begäran med ett certifikat

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Programmet ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| Omfång |Krävs |Värdet som skickas den `scope` parameter i den här begäran ska vara Resursidentifieraren (program-ID URI) av den resurs du vill, har den `.default` suffix. Värdet är för Microsoft Graph-exempel `https://graph.microsoft.com/.default`. Det här värdet informerar om att det ska utfärda en token för de som associerats med resursen som du vill använda för alla direkt tillämpning behörigheter som du har konfigurerat för din app, v2.0-slutpunkten. |
| client_assertion_type |Krävs |Värdet måste vara`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Krävs | Ett intyg (en JSON Web Token) som du behöver för att skapa och registrera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig att registrera ditt certifikat och format för kontrollen.|
| grant_type |Krävs |Måste vara `client_credentials`. |

Observera att parametrarna är nästan desamma som i fallet med begäran från delad hemlighet förutom att client_secret-parameter har ersatts av två parametrar: en client_assertion_type och client_assertion.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar ser ut så här:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomst-token. Appen kan använda denna token för att autentisera till den skyddade resursen som för en webb-API. |
| token_type |Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är `bearer`. |
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
| fel |Ett felkod sträng som du kan använda för att klassificera typer av fel som inträffar och att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig identifiera orsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa dig med diagnostik. |
| tidsstämpel |Tiden då felet inträffade. |
| trace_id |En unik identifierare för den begäran som kan hjälpa dig med diagnostik. |
| correlation_id |En unik identifierare för den begäran som kan hjälpa dig med diagnostik för komponenter. |

## <a name="use-a-token"></a>Använda en token
Nu när du har skaffat en token kan du använda token för att göra förfrågningar till resursen. När token upphör att gälla Upprepa begäran till den `/token` slutpunkten för att få en ny åtkomsttoken.

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
Ett exempel på ett program som implementerar klientens autentiseringsuppgifter ger med hjälp av administratören godkänna endpoint finns i vår [v2.0 daemon kodexemplet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
