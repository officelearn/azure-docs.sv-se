---
title: Azure AD v2.0 OAuth-auktorisering Code flödet | Microsoft Docs
description: Skapa webbprogram med hjälp av Azure AD-implementeringen av OAuth 2.0-autentiseringsprotokollet.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a3e8f8ab1594e992b6a6c9e9530c3b363d6d0346
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158245"
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v2.0 protokoll - OAuth 2.0-Auktoriseringskodflödet
OAuth 2.0 auktorisering kod bevilja kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel web API: er. App model v2.0 implementering av OAuth 2.0 kan, du använda API tillgång till dina appar och program och logga in. Den här handboken är språkoberoende och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av de [autentiseringsbibliotek för Azure öppen källkod](active-directory-authentication-libraries.md).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnittet 4.1 i OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Används för att utföra autentisering och auktorisering i flesta apptyper, inklusive [webbappar](active-directory-v2-flows.md#web-apps) och [internt installerade appar](active-directory-v2-flows.md#mobile-and-native-apps). Flödet gör det möjligt för appar att på ett säkert sätt hämta access_tokens som kan användas för att komma åt resurser som skyddas av v2.0-slutpunkten. 

## <a name="protocol-diagram"></a>Protokollet diagram
På en hög nivå hela autentiseringsflödet för en intern/mobile-programmet ser ut så här:

![Flödet för OAuth-Auth-kod](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Begära ett auktoriseringskod
Auktoriseringskodflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. I den här förfrågan anger klienten de behörigheter som behövs för att hämta från användaren:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Klicka på länken nedan för att utföra den här begäran! När du har registrerat webbläsaren ska omdirigeras till `https://localhost/myapp/` med en `code` i adressfältet.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parameter             |             | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient                | obligatorisk    | Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                    |
| client_id             | obligatorisk    | Programmet ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.  |
| response_type         | obligatorisk    | Måste innehålla `code` för auktoriseringskodflödet.       |
| redirect_uri          | Rekommenderas | Redirect_uri för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en redirect_uris som du har registrerat i portalen, förutom det måste vara url-kodade. Du bör använda standardvärdet för interna & mobila appar, `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| omfång                 | obligatorisk    | En blankstegsavgränsad lista över [scope](active-directory-v2-scopes.md) som du vill att användaren godkänner du.           |
| response_mode         | Rekommenderas | Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Det kan vara `query` eller `form_post`.   |
| state                 | Rekommenderas | Ett värde som ingår i denna begäran kommer också att returneras i token svaret. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12). Värdet kan också koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| kommandotolk                | valfri    | Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena just nu är ”inloggning” none, och 'godkännande'. `prompt=login` Tvingar användaren att ange sina autentiseringsuppgifter på begäran, giltigt att negera enkel inloggning på. `prompt=none` är motsatsen - säkerställer att användaren inte visas med en interaktiv prompt som helst. Inte om begäran kan slutföras utan meddelanden via enkel inloggning på v2.0-slutpunkten returnerar ett `interaction_required` fel. `prompt=consent` dialogrutan OAuth-medgivande utlöses när användaren loggar in, be användaren att tilldela behörigheter till appen. |
| login_hint            | valfri    | Kan användas för att fylla före adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sitt lösenord i förväg. Ofta appar kommer att använda den här parametern under återautentiseringen redan har extraherats användarnamnet från en tidigare inloggning med hjälp av den `preferred_username` anspråk.                                                                                                                                                                                                                                                                                                    |
| domain_hint           | valfri    | Kan vara något av `consumers` eller `organizations`. Om den hoppar över e-postbaserad identifieringsprocessen användaren som passerar på v2.0-inloggningssidan, vilket leder till en något mer effektiv användarupplevelse. Ofta appar kommer att använda den här parametern under återautentiseringen genom att extrahera den `tid` från en tidigare inloggning. Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, bör du använda `domain_hint=consumers`. Annars använder `domain_hint=organizations`.                                                                                                              |
| code_challenge_method | valfri    | Den metod som används för att koda den `code_verifier` för den `code_challenge` parameter. Kan vara något av `plain` eller `S256`. Om inte, `code_challenge` antas vara klartext om `code_challenge` ingår. Azure AAD version 2.0 stöder både `plain` och `S256`. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | valfri    | Används för att säkra auktorisering kod ger via bevis nyckel för koden Exchange (PKCE) från en ursprunglig-klient. Krävs om `code_challenge_method` ingår. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                    |

Nu ombeds användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. V2.0-slutpunkten säkerställer också att användaren har godkänt för de behörigheter som anges i den `scope` Frågeparametern. Om användaren inte har godkänt till någon av dessa behörigheter, uppmanas användaren att godkänna behörigheterna som krävs. Information om [behörigheter, medgivande och flera innehavare appar finns här](active-directory-v2-scopes.md).

När användaren autentiserar och ger ditt medgivande, v2.0-slutpunkten returnerar ett svar på din app på den angivna `redirect_uri`, med hjälp av den metod som anges i den `response_mode` parameter.

#### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med `response_mode=query` ser ut som:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beskrivning                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Koden      | Authorization_code som begärts av appen. Appen kan använda Auktoriseringskoden för att begära en åtkomst-token för målresursen. Authorization_codes är mycket kort livslängd, vanligtvis de går ut efter 10 minuter. |
| state     | Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska.                                            |

#### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter         | Beskrivning                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| fel             | Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel.          |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktorisering endpoint fel
I följande tabell beskrivs de olika felkoder som kan returneras i den `error` parameter för felsvar.

| Felkod                | Beskrivning                                                                                                           | Klientåtgärd                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Protokollfel, till exempel en obligatorisk parameter saknas.                                                               | Åtgärda och skicka begäran igen. Detta är en utvecklingsfel vanligtvis påträffades under inledande testningen.                                                                                                                                     |
| unauthorized_client       | Klientprogrammet är inte tillåtet att begära en auktoriseringskod.                                           | Felet uppstår vanligen när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| Om ACCESS_DENIED             | Resursägare nekad medgivande                                                                                         | Klientprogrammet kan meddela användaren om att det går inte att fortsätta om användaren godkänner.                                                                                                                                               |
| unsupported_response_type | Auktorisering servern stöder inte svarstypen i begäran.                                         | Åtgärda och skicka begäran igen. Det här är en utveckling fel vanligtvis fångas upp under inledande testningen.                                                                                                                                     |
| server_error              | Ett oväntat fel uppstod på servern.                                                                         | Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att försenas sitt svar till ett tillfälligt fel.                                                                |
| temporarily_unavailable   | Servern är tillfälligt för upptagen för att hantera begäran.                                                           | Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd.                                                                                                               |
| invalid_resource          | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. | Det här felet indikerar resursen, om det finns inte har konfigurerats i klienten. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD.                                          |
|login_required             | För många eller att hitta några användare | Klienten begärde tyst autentisering (`prompt=none`), men det gick inte att hitta en enskild användare. Detta kan innebära att det finns flera användare aktiv i sessionen eller inga användare. Detta tar hänsyn till de valt (till exempel om det finns 2 AAD-konton som aktiv och en MSA och `consumers` är valt, tyst autentisering fungerar). |
|interaction_required       | Begäran kräver interaktion från användaren. | En ytterligare autentisering steg eller godkännande krävs. Försök igen med begäran utan `prompt=none`. |

## <a name="request-an-access-token"></a>Begär en åtkomst-token
Nu när du har skaffat ett authorization_code och har beviljats behörighet av användaren, kan du lösa in den `code` för en `access_token` till en resurs. Gör detta genom att skicka en `POST` begäran om att den `/token` slutpunkt:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Försök köra begäran i Postman! (Glöm inte att ersätta den `code`) [ ![körs i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parameter     |                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient        | obligatorisk              | Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | obligatorisk              | Programmet ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.                                                                                                                                                                                                                             |
| grant_type    | obligatorisk              | Måste vara `authorization_code` för auktoriseringskodflödet.                                                                                                                                                                                                                                                                                                                                                                            |
| omfång         | obligatorisk              | En blankstegsavgränsad lista över scope. Scope som efterfrågas i denna del måste vara likvärdigt med eller en delmängd av scope som efterfrågas i det första del. Om scope som anges i denna begäran täcker flera resursservern ska returnera en token för den resurs som anges i det första omfånget v2.0-slutpunkten. En mer detaljerad förklaring av scope finns [behörigheter, medgivande och scope](active-directory-v2-scopes.md). |
| Koden          | obligatorisk              | Authorization_code som du har införskaffade i den första del av flödet.                                                                                                                                                                                                                                                                                                                                                                   |
| redirect_uri  | obligatorisk              | Samma redirect_uri värde som används för att hämta authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | krävs för webbprogram | Den hemlighet som programmet som du skapade i portalen för registrering av app för din app. Den bör inte användas i en intern app eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er som har möjlighet att lagra client_secret på ett säkert sätt på serversidan.                                                                                                                      |
| code_verifier | valfri              | Samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE användes i tillståndet kod bevilja begäran. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>Lyckat svar
Ett lyckat token svar ser ut:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Den begärda åtkomst-token. Appen kan använda denna token för autentisering till skyddade resursen, till exempel ett webb-API.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är ägar                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Hur länge den åtkomst-token är giltig (i sekunder).                                                                                                                                                                                                                                                                                                                                                                                                       |
| omfång         | Scope som gäller för access_token.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | En token för uppdatering av OAuth 2.0. Appen kan använda denna token få ytterligare åtkomsttoken när den aktuella åtkomst-token upphör att gälla. Refresh_tokens är långlivade och kan användas för att få åtkomst till resurser för längre tid. Mer information finns i den [v2.0 tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `offline_access` omfång begärdes.                                               |
| id_token      | En osignerad JSON-Webbtoken (JWT). Appen kan base64Url avkoda segmenten i den här variabeln för att begäraninformation om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `openid` omfång begärdes. |
#### <a name="error-response"></a>Felsvar
Felsvar ser ut:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beskrivning                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| fel             | Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel.          |
| error_codes       | En lista över STS-specifika felkoder som kan hjälpa dig i diagnostik.                                                |
| tidsstämpel         | Tiden då felet inträffade.                                                                           |
| trace_id          | En unik identifierare för den begäran som hjälper dig diagnostik.                                               |
| correlation_id    | En unik identifierare för den begäran som kan hjälpa i diagnostik för komponenter.                             |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för token för slutpunkt-fel
| Felkod              | Beskrivning                                                                                                           | Klientåtgärd                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Protokollfel, till exempel en obligatorisk parameter saknas.                                                               | Åtgärda och skicka begäran på nytt                                                                                                                                                                                                                |
| invalid_grant           | Auktoriseringskoden eller PKCE kod verifierar är ogiltigt eller har upphört att gälla.                                             | Försök med en ny begäran till den `/authorize` slutpunkt och kontrollera att parametern code_verifier var korrekt.                                                                                                                                   |
| unauthorized_client     | Den autentiserade klienten har inte behörighet att använda den här authorization grant-typen.                                     | Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| invalid_client          | Klientautentisering misslyckades.                                                                                        | Klientens autentiseringsuppgifter är inte giltiga. Om du vill åtgärda, uppdaterar programadministratören autentiseringsuppgifterna.                                                                                                                                       |
| unsupported_grant_type  | Auktorisering servern stöder inte typen bevilja tillstånd.                                              | Ändra bevilja i begäran. Den här typen av fel bör sker enbart under utveckling och identifieras under inledande testningen.                                                                                                      |
| invalid_resource        | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. | Detta anger resursen, om det finns inte har konfigurerats i klienten. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD.                                           |
| interaction_required    | Begäran kräver interaktion från användaren. Till exempel krävs ett steg i ytterligare autentisering.                   | Försök igen med samma resurs.                                                                                                                                                                                                  |
| temporarily_unavailable | Servern är tillfälligt för upptagen för att hantera begäran.                                                            | Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd.                                                                                                                |

## <a name="use-the-access-token"></a>Använd den åtkomst-token
Nu när du har skaffat har en `access_token`, du kan använda token i begäranden till webb-API: er genom att inkludera den i den `Authorization` huvud:

> [!TIP]
> Kör denna begäran i Postman! (Ersätt den `Authorization` huvud första) [ ![körs i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Uppdatera den åtkomst-token
Access_tokens är kort livslängd och du måste uppdatera dem när de går ut om du vill fortsätta få åtkomst till resurser. Du kan göra det genom att skicka in en annan `POST` begäran om att den `/token` slutpunkt, men den här gången ger den `refresh_token` i stället för den `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Försök köra begäran i Postman! (Glöm inte att ersätta den `refresh_token`) [ ![körs i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parameter     |                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient        | obligatorisk              | Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                  |
| client_id     | obligatorisk              | Programmet ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.                                                                                                                                                                                                                                                            |
| grant_type    | obligatorisk              | Måste vara `refresh_token` för denna del av auktoriseringskodflödet.                                                                                                                                                                                                                                                                                                                                                                                                    |
| omfång         | obligatorisk              | En blankstegsavgränsad lista över scope. Scope som efterfrågas i det här ben måste vara likvärdigt med eller en delmängd av scope som efterfrågas i det ursprungliga authorization_code begäran ben. Om scope som anges i denna begäran täcker flera resursservern ska returnera en token för den resurs som anges i det första omfånget v2.0-slutpunkten. En mer detaljerad förklaring av scope finns [behörigheter, medgivande och scope](active-directory-v2-scopes.md). |
| refresh_token | obligatorisk              | Refresh_token som du har införskaffade i andra del av flödet.                                                                                                                                                                                                                                                                                                                                                                                                      |
| redirect_uri  | obligatorisk              | Samma redirect_uri värde som används för att hämta authorization_code.                                                                                                                                                                                                                                                                                                                                                                                            |
| client_secret | krävs för webbprogram | Den hemlighet som programmet som du skapade i portalen för registrering av app för din app. Den bör inte användas i en intern app eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er som har möjlighet att lagra client_secret på ett säkert sätt på serversidan.                                                                                                                                                    |

#### <a name="successful-response"></a>Lyckat svar
Ett lyckat token svar ser ut:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Den begärda åtkomst-token. Appen kan använda denna token för autentisering till skyddade resursen, till exempel ett webb-API.                                                                                                                                                                                                                                                                                                                                     |
| token_type    | Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är ägar                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Hur länge den åtkomst-token är giltig (i sekunder).                                                                                                                                                                                                                                                                                                                                                                                                        |
| omfång         | Scope som gäller för access_token.                                                                                                                                                                                                                                                                                                                                                                                                          |
| refresh_token | En ny OAuth 2.0-uppdateringstoken. Med den här nya anskaffats uppdateringstoken så uppdaterings-tokens vara giltigt så länge som möjligt bör du ersätta den gamla uppdateringstoken. <br> **Obs:** endast angivna om `offline_access` omfång begärdes.                                                                                                                                                                                                |
| id_token      | En osignerad JSON-Webbtoken (JWT). Appen kan base64Url avkoda segmenten i den här variabeln för att begäraninformation om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `openid` omfång begärdes. |

#### <a name="error-response"></a>Felsvar

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beskrivning                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| fel             | Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel.           |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa dig i diagnostik. |
| tidsstämpel |Tiden då felet inträffade. |
| trace_id |En unik identifierare för den begäran som hjälper dig diagnostik. |
| correlation_id |En unik identifierare för den begäran som kan hjälpa i diagnostik för komponenter. |

En beskrivning av felkoder och rekommenderade klientåtgärd finns [felkoder för token för slutpunkt fel](#error-codes-for-token-endpoint-errors).
