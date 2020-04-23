---
title: OAuth 2.0-klientautentiseringsuppgifter flödar på Microsofts identitetsplattform | Azure
description: Skapa webbprogram med hjälp av Implementeringen av Microsofts identitetsplattform av OAuth 2.0-autentiseringsprotokollet.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 36a5fdf990432e3a41cf8fc578fa20b4910250b2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868441"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft-identitetsplattform och OAuth 2.0-klientautentiseringsflödet

Du kan använda [bevilja OAuth 2.0-klientautentiseringsuppgifter](https://tools.ietf.org/html/rfc6749#section-4.4) som anges i RFC 6749, ibland kallad *tvåbent OAuth,* för att komma åt webbaserade resurser med hjälp av ett programs identitet. Den här typen av bidrag används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. Dessa typer av program kallas ofta *demoner* eller *tjänstkonton*.

I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program. När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

OAuth 2.0-klientautentiseringsuppgifterna bevilja flödet tillåter en webbtjänst (konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare, för att autentisera när du ringer en annan webbtjänst. I det här fallet är klienten vanligtvis en mellannivåwebbtjänst, en daemontjänst eller en webbplats. För en högre säkerhetsnivå gör Microsofts identitetsplattform det också möjligt för den anropande tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringstjänst.

I den mer typiska *oauth med trebent*beviljas ett klientprogram behörighet att komma åt en resurs för en viss användares räkning. Behörigheten delegeras från användaren till programmet, vanligtvis under [medgivandeprocessen.](v2-permissions-and-consent.md) I klientautentiseringsuppgifterna *(tvåbenta OAuth)* beviljas dock behörigheter direkt till själva programmet. När appen visar en token för en resurs, tvingar resursen att själva appen har behörighet att utföra en åtgärd och inte användaren.

## <a name="protocol-diagram"></a>Protokolldiagram

Hela klientautentiseringsflödet liknar följande diagram. Vi beskriver vart och ett av stegen senare i den här artikeln.

![Diagram som visar klientautentiseringsflödet](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Få direkt auktorisering

En app får vanligtvis direkt auktorisering för åtkomst till en resurs på två sätt:

* [Genom en åtkomstkontrollista (ACL) på resursen](#access-control-lists)
* [Genom programbehörighetstilldelning i Azure AD](#application-permissions)

Dessa två metoder är de vanligaste i Azure AD och vi rekommenderar dem för klienter och resurser som utför klientautentiseringsflödet. En resurs kan också välja att auktorisera sina klienter på andra sätt. Varje resursserver kan välja den metod som är mest meningsfull för dess program.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll

En resursprovider kan framtvinga en auktoriseringskontroll baserat på en lista över program-ID:er (klient)som den känner till och ger en viss åtkomstnivå till. När resursen tar emot en token från slutpunkten för Microsoft identity platform kan den avkoda token och extrahera klientens program-ID från `appid` och `iss` anspråk. Sedan jämförs programmet med en åtkomstkontrollista (ACL) som den underhåller. Åtkomstkontrollistans granularitet och metod kan variera avsevärt mellan resurser.

Ett vanligt användningsfall är att använda en åtkomstkontrollista för att köra tester för ett webbprogram eller för ett webb-API. Webb-API:et kan bara ge en delmängd av fullständiga behörigheter till en viss klient. Om du vill köra heltäckande tester på API:et skapar du en testklient som hämtar token från slutpunkten för Microsoft-identitetsplattformen och skickar dem sedan till API:et. API:et kontrollerar sedan ACL för testklientens program-ID för fullständig åtkomst till API:ets hela funktioner. Om du använder den här typen av åtkomstkontrollista måste `appid` du verifiera inte `iss` bara anroparens värde utan också verifiera att värdet för token är betrott.

Den här typen av auktorisering är vanligt för demoner och tjänstkonton som behöver komma åt data som ägs av konsumentanvändare som har personliga Microsoft-konton. För data som ägs av organisationer rekommenderar vi att du får nödvändig auktorisering via programbehörigheter.

### <a name="application-permissions"></a>Programbehörigheter

I stället för att använda ACL:er kan du använda API:er för att visa en uppsättning **programbehörigheter**. En programbehörighet beviljas ett program av en organisations administratör och kan endast användas för att komma åt data som ägs av den organisationen och dess anställda. Microsoft Graph visar till exempel flera programbehörigheter för att göra följande:

* Läsa e-post i alla postlådor
* Läsa och skriva e-post i alla postlådor
* Skicka e-post som användare
* Läs katalogdata

Mer information om programbehörigheter finns i [Microsoft Graph](https://developer.microsoft.com/graph).

Om du vill använda programbehörigheter i appen följer du stegen som beskrivs i nästa avsnitt.


> [!NOTE]
> När du autentiserar som ett program, i motsats till med en användare, kan du inte använda "delegerade behörigheter" (scope som beviljas av en användare).  Du måste använda "programbehörigheter", även kallade "roller", som beviljas av en administratör för programmet (eller via förauktorisering av webb-API: et).


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörigheterna i appregistreringsportalen

1. Registrera dig och skapa en app via den nya [appregistreringsupplevelsen (Förhandsgranska).](quickstart-register-app.md)
2. Gå till din ansökan i appregistreringarna (förhandsversionen). Navigera till avsnittet **Certifikat & hemligheter** och lägg till en ny **klienthemlighet**eftersom du behöver minst en klienthemlighet för att begära en token.
3. Leta reda på avsnittet **API-behörigheter** och lägg sedan till de **programbehörigheter** som appen kräver.
4. **Spara** appregistreringen.

#### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderas: Logga in användaren i appen

När du skapar ett program som använder programbehörigheter kräver appen vanligtvis en sida eller vy där administratören godkänner appens behörigheter. Den här sidan kan vara en del av appens inloggningsflöde, en del av appens inställningar, eller så kan det vara ett dedikerat "connect"-flöde. I många fall är det vettigt att appen visar den här "connect"-vyn först när en användare har loggat in med ett Microsoft-konto för arbete eller skola.

Om du signerar användaren i appen kan du identifiera den organisation som användaren tillhör innan du ber användaren att godkänna programbehörigheterna. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina användare. Om du vill logga in användaren följer du våra [självstudier för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalogadministratör

När du är redo att begära behörigheter från organisationens administratör kan du omdirigera användaren till *slutpunkten för administratör*för Microsoft-identitetsplattform.

> [!TIP]
> Prova att köra denna begäran i Postman! (Använd ditt eget app-ID för bästa resultat - självstudieprogrammet begär inte användbara behörigheter.) [Prova att köra den här begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Proffstips: Försök att klistra in följande begäran i en webbläsare.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Den katalogklient som du vill begära behörighet från. Detta kan vara i GUID eller eget namnformat. Om du inte vet vilken klient organisation användaren tillhör och du vill låta `common`dem logga in med en klient använder du . |
| `client_id` | Krävs | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `redirect_uri` | Krävs | Omdirigerings-URI:n där du vill att svaret ska skickas för att appen ska hanteras. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen, förutom att den måste vara URL-kodad och att den kan ha ytterligare sökvägssegment. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |

Nu framtvingar Azure AD att endast en klientadministratör kan logga in på slutföra begäran. Administratören uppmanas att godkänna alla direkta programbehörigheter som du har begärt för din app i appregistreringsportalen.

##### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för ditt program ser det lyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Katalogklienten som gav ditt program de behörigheter som begärs, i GUID-format. |
| `state` | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| `admin_consent` | Ställ in på **Sant**. |

##### <a name="error-response"></a>Felsvar

Om administratören inte godkänner behörigheterna för ditt program ser det misslyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felkodssträng som du kan använda för att klassificera typer av fel och som du kan använda för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett fel. |

När du har fått ett lyckat svar från slutpunkten för appetablering har appen fått de direkta programbehörigheter som den begärde. Nu kan du begära en token för den resurs som du vill ha.

## <a name="get-a-token"></a>Skaffa en token

När du har skaffat nödvändig auktorisering för ditt program fortsätter du med att hämta åtkomsttoken för API:er. Om du vill hämta en token med hjälp av `/token` klientautentiseringsbeviljanden skickar du en POST-begäran till slutpunkten för Microsoft-identitetsplattform:

> [!TIP]
> Prova att köra denna begäran i Postman! (Använd ditt eget app-ID för bästa resultat - självstudieprogrammet begär inte användbara behörigheter.) [Prova att köra den här begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Åtkomsttokenbegäran med en delad hemlighet

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Katalogklienten som programmet planerar att arbeta mot, i GUID- eller domännamnsformat. |
| `client_id` | Krävs | Program-ID:t som har tilldelats din app. Du hittar den här informationen i portalen där du registrerade din app. |
| `scope` | Krävs | Värdet som skickas `scope` för parametern i den här begäran ska vara resursidentifieraren (program-ID `.default` URI) för den resurs du vill ha, anbringad med suffixet. För exemplet Microsoft Graph är `https://graph.microsoft.com/.default`värdet . <br/>Det här värdet talar om för slutpunkten för Microsoft-identitetsplattformen att av alla direkta programbehörigheter som du har konfigurerat för din app, ska slutpunkten utfärda en token för de som är associerade med den resurs som du vill använda. Mer information om `/.default` omfånget finns i dokumentationen för [medgivande](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Krävs | Klienthemligheten som du har skapat för din app i appregistreringsportalen. Klienthemligheten måste url-kodas innan den skickas. |
| `grant_type` | Krävs | Måste ställas `client_credentials`in på . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Åtkomsttokenbegäran med ett certifikat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Katalogklienten som programmet planerar att arbeta mot, i GUID- eller domännamnsformat. |
| `client_id` | Krävs |Programmets (klient)-ID som har tilldelats din app. |
| `scope` | Krävs | Värdet som skickas `scope` för parametern i den här begäran ska vara resursidentifieraren (program-ID `.default` URI) för den resurs du vill ha, anbringad med suffixet. För exemplet Microsoft Graph är `https://graph.microsoft.com/.default`värdet . <br/>Det här värdet informerar slutpunkten för Microsoft identity platform att av alla direkta programbehörigheter som du har konfigurerat för din app, bör det utfärda en token för de som är associerade med den resurs som du vill använda. Mer information om `/.default` omfånget finns i dokumentationen för [medgivande](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Krävs | Värdet måste anges `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`till . |
| `client_assertion` | Krävs | Ett påstående (en JSON-webbtoken) som du behöver skapa och signera med certifikatet som du registrerade som autentiseringsuppgifter för ditt program. Läs om [certifikatuppgifter](active-directory-certificate-credentials.md) om du vill lära dig hur du registrerar certifikatet och formatet på påståendet.|
| `grant_type` | Krävs | Måste ställas `client_credentials`in på . |

Observera att parametrarna är nästan desamma som i fallet med begäran av delad hemlighet förutom att parametern client_secret ersätts med två parametrar: en client_assertion_type och client_assertion.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar ut så här:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` | Den begärda åtkomsttoken. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel till ett webb-API. |
| `token_type` | Anger tokentypsvärdet. Den enda typ som Microsoft `bearer`identity platform stöder är . |
| `expires_in` | Den tid som en åtkomsttoken är giltig (i sekunder). |

### <a name="error-response"></a>Felsvar

Ett felmeddelande ser ut så här:

```json
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
| `error` | En felkodsträng som du kan använda för att klassificera typer av fel som uppstår och för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |
| `error_codes` | En lista över STS-specifika felkoder som kan hjälpa till med diagnostik. |
| `timestamp` | Den tid då felet uppstod. |
| `trace_id` | En unik identifierare för begäran för att hjälpa till med diagnostik. |
| `correlation_id` | En unik identifierare för begäran för att hjälpa till med diagnostik mellan komponenter. |

## <a name="use-a-token"></a>Använda en token

Nu när du har skaffat en token använder du token för att göra begäranden till resursen. När token upphör att gälla upprepar `/token` du begäran till slutpunkten för att hämta en ny åtkomsttoken.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Kodexempel och annan dokumentation

Läsa [dokumentationen för klientautentiseringsuppgifter](https://aka.ms/msal-net-client-credentials) från Microsoft Authentication Library

| Exempel | Plattform |Beskrivning |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1-konsol | Ett enkelt .NET Core-program som visar användarna av en klient som frågar Microsoft Graph med hjälp av programmets identitet i stället för för en användare. Exemplet illustrerar också variationen med certifikat för autentisering. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Ett webbprogram som synkroniserar data från Microsoft Graph med hjälp av programmets identitet i stället för för en användares räkning. |
