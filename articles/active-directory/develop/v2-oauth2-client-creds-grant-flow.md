---
title: Använd Microsoft identity-plattformen för att komma åt säker resurser utan användaråtgärder | Azure
description: Skapa webbprogram med hjälp av Microsoft identity-plattformen implementeringen av OAuth 2.0-protokollet för autentisering.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3073d34a6ffeadd1c1c0022b5c1636f06cc6210a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190824"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft identity-plattformen och OAuth 2.0 flödet

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Du kan använda den [beviljande av autentiseringsuppgifter för OAuth 2.0-klient](https://tools.ietf.org/html/rfc6749#section-4.4) anges i RFC 6749, även kallad *tvåledade OAuth*, för att få åtkomst till webb-värdbaserade resurser med hjälp av identiteten för ett program. Den här typen av bevilja används ofta för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. Dessa typer av program ofta kallas *daemons* eller *tjänstkonton*.

Autentiseringsuppgifter för OAuth 2.0-klient ge flow tillåter en webbtjänst (konfidentiell klient) för att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare för att autentisera vid anrop av en annan webbtjänst. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemontjänst eller en webbplats. Microsoft identity-plattformen kan också anropa tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringsuppgift för en högre säkerhetsnivå.

> [!NOTE]
> Microsoft identity-plattformen slutpunkt stöder inte alla Azure AD-scenarier och funktioner. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md).

I en typisk *treledade OAuth*, ett klientprogram som har beviljats behörighet att komma åt en resurs för en viss användares räkning. Behörigheten har delegerats från användaren till programmet, vanligtvis under den [godkänna](v2-permissions-and-consent.md) processen. Men i klientens autentiseringsuppgifter (*tvåledade OAuth*) flödet, behörigheterna har beviljats direkt till själva programmet. När i appen visas en token till en resurs, tillämpar resursen att själva appen har behörighet att utföra en åtgärd och inte användare.

## <a name="protocol-diagram"></a>Protokollet diagram

Hela flödet ser ut ungefär som i följande diagram. Vi beskriver varje steg nedan.

![Flödet](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Få direkt behörighet

En app tar normalt emot direkta tillstånd att komma åt en resurs i ett av två sätt:

* [Via en åtkomstkontrollista (ACL) på resursen](#access-control-lists)
* [Via programtilldelning behörighet i Azure AD](#application-permissions)

Dessa två metoder är de vanligaste i Azure AD och vi rekommenderar dem för klienter och resurser för klienten flödet. En resurs kan också välja att ge sina kunder på andra sätt. Varje resurs-server kan välja den metod som passar bäst för sina program.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll

En resursprovider kan tvinga en kontroll av auktorisering baserat på en lista över program (klient)-ID som den vet och ger en viss nivå av åtkomst till. När resursen tar emot en token från Microsoft identity-plattformen slutpunkten, den kan avkoda token och extrahera klientens program-ID från den `appid` och `iss` anspråk. Den jämför sedan programmet mot en åtkomstkontrollista (ACL) som den upprätthåller. ACL granularitet och metoden kan variera avsevärt mellan resurser.

Ett vanligt användningsfall är att använda en ACL ska kunna köra tester för ett webbprogram eller för ett webb-API. Webb-API kan bevilja endast en delmängd av fullständig behörighet till en viss klient. Skapa en Testklient som skaffar token från slutpunkten för Microsoft identity-plattformen och skickar dem till API: et för att köra slutpunkt till slutpunkt-testerna på API: et. API: et kontrollerar sedan Åtkomstkontrollistan för test-klientens program-ID för fullständig åtkomst till API: ets hela funktioner. Om du använder den här typen av ACL, måste du verifiera inte bara anroparens `appid` värde men också kontrollera att den `iss` värdet av token är betrodd.

Den här typen av auktorisering är vanligt för Daemon och tjänstkonton som behöver komma åt data som ägs av konsumentanvändare med personliga Microsoft-konton. För data som ägs av organisationer, rekommenderar vi att du får nödvändiga auktoriseringen via behörigheter för programmet.

### <a name="application-permissions"></a>Programbehörigheter

Du kan använda API: er för att exponera en uppsättning behörigheter för programmet istället för att använda ACL: er. Ett program behörighet har beviljats till ett program av en organisations administratör och kan användas endast för att komma åt data som ägs av organisationen och deras anställda. Microsoft Graph visar till exempel flera behörigheter för programmet att göra följande:

* Läsa e-post i alla postlådor
* Läsa och skriva e-post i alla postlådor
* Skicka e-post som valfri användare
* Läsa katalogdata

Mer information om behörigheter för programmet går du till [Microsoft Graph](https://developer.microsoft.com/graph).

Följ stegen som beskrivs i nästa avsnitt för att använda behörigheter för programmet i din app.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörighet i portalen för registrering av app

1. Registrera dig och skapa en app via nya [appregistreringar (förhandsversion) får](quickstart-register-app.md).
2. Gå till ditt program i App-registreringar (förhandsgranskning)-upplevelse. Navigera till den **certifikat och hemligheter** , och lägger till en **nya klienthemligheten**, eftersom du behöver minst en klienthemlighet för att begära en token.
3. Leta upp den **API-behörigheter** , och lägger sedan till den **programbehörigheter** som din app kräver.
4. **Spara** appregistreringen.

#### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderat: Logga in användaren i din app

När du skapar ett program som använder behörigheter för programmet kräver normalt appen en sida eller vy som administratören har godkänt dess behörigheter. Den här sidan kan vara en del av appens inloggning flöde, en del av appens inställningar, eller så kan vara ett dedikerat ”ansluta”-flöde. I många fall kan det vara bra för appen ska visa detta ”ansluta” Visa endast när en användare har loggat in med ett arbets- eller din skola Microsoft-konto.

Om du loggar in användaren i din app kan du identifiera organisationen som användaren tillhör innan du be användaren att godkänna behörigheterna som programmet. Även om de inte är absolut nödvändigt, det kan hjälpa dig att skapa en mer intuitiv upplevelse för användarna. Om du vill registrera användare i följa våra [Microsoft identity-plattformen protokollet självstudier](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en directory-administratör

När du är redo att begära behörighet från organisationens administratör kan du omdirigera användaren till Microsoft identity-plattformen *medgivande adminslutpunkten*.

> [!TIP]
> Försök att köra den här begäran i Postman! (Använda en egen app-ID för bästa resultat – självstudie programmet begär inte användbara behörighet.) [![Kör i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Directory-klient som du vill begära behörighet från. Detta kan vara i GUID eller eget namnformat. Om du inte vet vilken klient som användaren tillhör och du vill låta dem logga in med valfri klient, Använd `common`. |
| `client_id` | Krävs | Den **(klient)-ID: T** som den [Azure-portalen – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelse som tilldelats din app. |
| `redirect_uri` | Obligatoriskt | Omdirigerings-URI där du vill att svaret skickas för din app för att hantera. Den måste exakt matcha någon av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodad och kan ha ytterligare sökvägssegment. |
| `state` | Rekommenderas | Ett värde som ingår i den begäran som returneras även token svaret. Det kan vara en sträng med innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |

Nu kan framtvingar Azure AD som endast en Innehavaradministratör kan logga in på fullständig begäran. Administratören blir ombedd att godkänna alla direkt tillämpning-behörigheter som du har begärt för din app i portalen för registrering av appen.

##### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheter för ditt program, lyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Directory-klient som beviljats de behörigheter som den begärda i GUID-format för ditt program. |
| `state` | Ett värde som ingår i den begäran som också returneras i token-svaret. Det kan vara en sträng med innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| `admin_consent` | Ange **SANT**. |

##### <a name="error-response"></a>Felsvar

Om administratören inte godkänner behörigheter för ditt program, misslyckat svar som ser ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felsträng kod som du kan använda för att klassificera typer av fel, och som du kan använda för att reagera på fel. |
| `error_description` | Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett fel. |

När du har fått ett lyckat svar från slutpunkten för app-etablering, kan din app har fått de direkta programbehörigheter begärda. Nu kan du begära en token för den resurs som du vill.

## <a name="get-a-token"></a>Hämta en token

När du har köpt den nödvändiga auktoriseringen för ditt program kan du fortsätta med att hämta åtkomsttoken för API: er. För att få en token med hjälp av klienten klientautentiseringsuppgifter kan skicka en POST-begäran till den `/token` Microsoft identity-plattformen slutpunkt:

> [!TIP]
> Försök att köra den här begäran i Postman! (Använda en egen app-ID för bästa resultat – självstudie programmet begär inte användbara behörighet.) [![Kör i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fall: Begäran om åtkomsttoken med en delad hemlighet

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
| `tenant` | Krävs | Directory-klient programmet planerar att arbeta mot, i GUID eller domännamn format. |
| `client_id` | Krävs | Program-ID som har tilldelats din app. Du hittar den här informationen i portalen där du har registrerat appen. |
| `scope` | Obligatoriskt | Det värde som angavs den `scope` parameter i den här begäran ska vara resursidentifierare (program-ID-URI) av den resurs som du vill, har den `.default` suffix. Microsoft Graph-exemplet är värdet `https://graph.microsoft.com/.default`. <br/>Det här värdet anger Microsoft identity-plattformen slutpunkten att slutpunkten för alla direkt tillämpning behörigheter du har konfigurerat för din app, bör utfärdar en token för de som hör till resursen som du vill använda. Mer information om den `/.default` omfång kan du läsa den [godkänna dokumentation](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obligatoriskt | Klienthemlighet som du skapade för din app i portalen för registrering av appen. Klienthemlighet måste vara URL-kodat innan de skickas. |
| `grant_type` | Obligatoriskt | Måste anges till `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: Begäran om åtkomsttoken med ett certifikat

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
| `tenant` | Krävs | Directory-klient programmet planerar att arbeta mot, i GUID eller domännamn format. |
| `client_id` | Krävs |ID för programmet (klient) som har tilldelats din app. |
| `scope` | Krävs | Det värde som angavs den `scope` parameter i den här begäran ska vara resursidentifierare (program-ID-URI) av den resurs som du vill, har den `.default` suffix. Microsoft Graph-exemplet är värdet `https://graph.microsoft.com/.default`. <br/>Det här värdet meddelar Microsoft identity-plattformen slutpunkten att den över alla direkt tillämpning behörigheter som du har konfigurerat för din app, bör utfärda en token för de som hör till resursen som du vill använda. Mer information om den `/.default` omfång kan du läsa den [godkänna dokumentation](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Krävs | Värdet måste anges till `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obligatoriskt | Ett intyg (en JSON-webbtoken) som du behöver för att skapa och signera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig hur du registrerar ditt certifikat och format för kontrollen.|
| `grant_type` | Krävs | Måste anges till `client_credentials`. |

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
| `access_token` | Den begärda åtkomst-token. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel till ett webb-API. |
| `token_type` | Anger typ tokenu värdet. Den enda skriver som Microsoft identity-plattformen stöder är `bearer`. |
| `expires_in` | Hur lång tid som en åtkomst-token är giltig (i sekunder). |

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
| `error` | En felkodsträngen som du kan använda för att klassificera typer av fel som inträffar och reagera på fel. |
| `error_description` | Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |
| `error_codes` | En lista över STS-specifika felkoder som kan underlätta diagnostik. |
| `timestamp` | Den tid när felet inträffade. |
| `trace_id` | En unik identifierare för begäran om att hjälpa till med diagnostik. |
| `correlation_id` | En unik identifierare för begäran om att hjälpa till med diagnostik för komponenter. |

> [!NOTE]
> Du kan uppdatera manifestfilen av program från azure Portal för att ditt program för att kunna ta emot v2-token. Du kan lägga till attributet `accessTokenAcceptedVersion` och ange värdet till 2 som `"accessTokenAcceptedVersion": 2`. Kontrollera artikeln [programmanifestet](https://docs.microsoft.com/en-us/azure/active-directory/develop/reference-app-manifest#manifest-reference) vill veta mer om samma. Som standard programmet för tillfället får en v1-token. Om detta inte har definierats inom programmet/webb-API-manifestet det värdet för det här attributet i manifestet standardvärdet 1 och kan därför programmet tar emot v1-token.  


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

## <a name="code-samples-and-other-documentation"></a>Kodexempel och annan dokumentation

Läs den [klientautentiseringsuppgifter översikt dokumentation](https://aka.ms/msal-net-client-credentials) från Microsoft Authentication Library

| Exempel | Plattform |Beskrivning |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET core 2.1-konsolen | Ett enkelt program i .NET Core som visar användarna för en klient frågar Microsoft Graph med hjälp av identiteten för programmet, i stället för för en användares räkning. Exemplet visar också variationen använda certifikat för autentisering. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Ett webbprogram som synkroniserar data från Microsoft Graph med hjälp av identiteten för programmet, i stället för för en användares räkning. |
