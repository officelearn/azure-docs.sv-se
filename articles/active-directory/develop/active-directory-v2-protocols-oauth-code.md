---
title: Azure AD v2.0 OAuth Authorization Code flödet | Microsoft Docs
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
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 83ac97d1e1860ed9a385563f98318bd5db493b22
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263580"
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v2.0-protokoll – OAuth 2.0-Auktoriseringskodflöde
OAuth 2.0-auktoriseringskod kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. Med app model v2.0 implementering av OAuth 2.0 kan du lägga till logga in och API-åtkomst till dina appar och program. Den här handboken är språkoberoende och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av de [Azure open source-autentiseringsbibliotek](active-directory-authentication-libraries.md).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Används för att utföra autentisering och auktorisering i flesta apptyper, däribland [webbappar](active-directory-v2-flows.md#web-apps) och [internt installerade appar](active-directory-v2-flows.md#mobile-and-native-apps). Flödet gör det möjligt för appar på ett säkert sätt hämta access_tokens som kan användas för att få åtkomst till resurser som skyddas av v2.0-slutpunkten. 

## <a name="protocol-diagram"></a>Protokollet diagram
På en hög nivå ut hela autentiseringsflödet för ett program för intern/mobile lite så här:

![Kodflöde för OAuth-autentisering](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod
Auktoriseringskodsflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. I den här begäran anger klienten de behörigheter som krävs för att läsa från användaren:

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
> Klicka på länken nedan för att utföra den här begäran! Efter inloggningen webbläsaren ska omdirigeras till `https://localhost/myapp/` med en `code` i adressfältet.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parameter             |             | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient                | obligatorisk    | Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                    |
| client_id             | obligatorisk    | Programmet med ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.  |
| response_type         | obligatorisk    | Måste innehålla `code` för auktoriseringskodsflödet.       |
| redirect_uri          | Rekommenderas | Redirect_uri för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av redirect_uris som du registrerade i portalen, men det måste vara url-kodas. För interna & mobila appar, bör du använda standardvärdet för `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| omfång                 | obligatorisk    | En blankstegsavgränsad lista över [scope](active-directory-v2-scopes.md) som du vill att användaren att godkänna.           |
| response_mode         | Rekommenderas | Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Kan vara `query`, `fragment`, eller `form_post`. `query` innehåller koden som en frågesträngsparameter på din omdirigerings-URI. Om du ska få en ID-token med hjälp av det implicita flödet, du kan inte använda `query` som angetts på den [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du ska få enbart koden, kan du använda `query`, `fragment`, eller `form_post`. `form_post` Kör ett INLÄGG som innehåller koden omdirigerings-URI. Mer information finns i [OpenID Connect-protokoll](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| state                 | Rekommenderas | Ett värde som ingår i den begäran som också kommer att returneras i token-svaret. Det kan vara en sträng med innehåll som du önskar. Ett slumpmässigt genererat unikt värde som normalt används för [att förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12). Värdet kan också koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| fråga                | valfri    | Anger vilken typ av interaktion från användaren som krävs. De enda giltiga värdena just nu är ”inloggning”, ”ingen” och ”godkänna”. `prompt=login` kommer att tvinga användaren att ange sina autentiseringsuppgifter på begäran, vilket eliminerar enkel inloggning. `prompt=none` är motsatsen – det säkerställer att användaren inte visas med den interaktiva prompten alls. Om begäran inte kan slutföras tyst via enkel inloggning, v2.0-slutpunkten returnerar ett `interaction_required` fel. `prompt=consent` utlöser OAuth godkännande i dialogrutan när användaren loggar in, ber användaren att bevilja behörigheter till appen. |
| login_hint            | valfri    | Kan användas för att fylla förväg adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sina användarnamn i tid. Ofta appar kommer att använda den här parametern under återautentiseringen redan har extraherats användarnamnet från en tidigare logga in med den `preferred_username` anspråk.                                                                                                                                                                                                                                                                                                    |
| domain_hint           | valfri    | Kan vara något av `consumers` eller `organizations`. Om inkluderat, den hoppar över e-postbaserad identifieringsprocessen som användaren som passerar på v2.0 på inloggningssidan, vilket leder till en något mer effektiv användarupplevelse. Ofta appar kommer att använda den här parametern under återautentiseringen genom att extrahera den `tid` från en föregående inloggning. Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, bör du använda `domain_hint=consumers`. Annars kan du använda `domain_hint=organizations`.                                                                                                              |
| code_challenge_method | valfri    | Den metod som används för att koda den `code_verifier` för den `code_challenge` parametern. Kan vara något av `plain` eller `S256`. Om undantas identifieras `code_challenge` antas vara klartext om `code_challenge` ingår. Azure AAD v2.0 stöder både `plain` och `S256`. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | valfri    | Används för att skydda auktoriseringsbeviljanden kod via Proof-nyckel för kod Exchange (PKCE) från en intern klient. Krävs om `code_challenge_method` ingår. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                    |

Nu kan uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. V2.0-slutpunkten säkerställer också att användaren har samtyckt till behörigheterna som anges i den `scope` frågeparameter. Om användaren inte har godkänt att någon av dessa behörigheter, kommer den be användaren att godkänna behörigheterna som krävs. Information om [behörigheter och samtycke appar för flera klienter finns här](active-directory-v2-scopes.md).

När användaren autentiserar och ger ditt medgivande, v2.0-slutpunkten returnerar ett svar till din app på den angivna `redirect_uri`, med den metod som beskrivs i den `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med `response_mode=query` ser ut som:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beskrivning                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kod      | Authorization_code som appen har begärt. Appen kan använda Auktoriseringskoden för att begära en åtkomsttoken för målresursen. Authorization_codes kortlivade, vanligtvis de går ut efter 10 minuter. |
| state     | Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska.                                            |

#### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter         | Beskrivning                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| fel             | En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel.          |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slutpunkt-auktoriseringsfel
I följande tabell beskrivs olika felkoder som kan returneras i de `error` -parametern för felsvaret.

| Felkod                | Beskrivning                                                                                                           | Klientåtgärd                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Protokollfel, till exempel en obligatorisk parameter saknas.                                                               | Åtgärda och skicka begäran igen. Det här är en utvecklingsfel som vanligtvis påträffades under första testningen.                                                                                                                                     |
| unauthorized_client       | Klientprogrammet har inte behörighet att begära en auktoriseringskod.                                           | Det här felet uppstår vanligen när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| ACCESS_DENIED             | Resursägaren nekas godkännande                                                                                         | Klientprogrammet kan meddela användaren om att det går inte att fortsätta om inte användaren godkänner.                                                                                                                                               |
| unsupported_response_type | Auktoriseringsservern stöder inte svarstypen i begäran.                                         | Åtgärda och skicka begäran igen. Det här är en utveckling fel vanligtvis påträffades under första testningen.                                                                                                                                     |
| server_error              | Ett oväntat fel inträffade på servern.                                                                         | Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad till ett tillfälligt fel.                                                                |
| temporarily_unavailable   | Servern är tillfälligt för upptagen för att hantera begäran.                                                           | Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd.                                                                                                               |
| invalid_resource          | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. | Det här felet indikerar resursen, om den finns, inte har konfigurerats på klienten. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD.                                          |
|login_required             | För många eller inga användare hittades | Klienten har begärt en tyst autentisering (`prompt=none`), men det gick inte att hitta en enskild användare. Detta kan innebära att det finns flera användare aktiv i sessionen eller inga användare. Detta tar hänsyn till den klient som valts (till exempel om det finns 2 AAD-konton som aktiv och en MSA och `consumers` väljs, tyst autentisering ska fungera). |
|interaction_required       | Begäran kräver interaktion från användaren. | Det krävs en ytterligare autentisering steg eller godkännande. Försök igen med begäran utan `prompt=none`. |

## <a name="request-an-access-token"></a>Begär en åtkomsttoken
Nu när du har skaffat en authorization_code och har beviljats behörighet av användaren, kan du lösa in den `code` för en `access_token` till önskad resurs. Gör detta genom att skicka en `POST` begäran till den `/token` slutpunkt:

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
> Försök att köra den här begäran i Postman! (Glöm inte att ersätta den `code`) [ ![kör i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parameter     |                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient        | obligatorisk              | Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | obligatorisk              | Programmet med ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.                                                                                                                                                                                                                             |
| _typ av beviljande    | obligatorisk              | Måste vara `authorization_code` för auktoriseringskodsflödet.                                                                                                                                                                                                                                                                                                                                                                            |
| omfång         | obligatorisk              | En blankstegsavgränsad lista med omfattningar. Omfattningar som efterfrågas i det här ben måste vara motsvarar eller en delmängd av scope som efterfrågas i det första ben. Om omfattningar som angetts i den här begäran sträcker sig över flera resursservern, returnerar en token för den resurs som angetts i det första omfånget v2.0-slutpunkten. En mer detaljerad förklaring av omfång finns [behörigheter och samtycke scope](active-directory-v2-scopes.md). |
| Kod          | obligatorisk              | Authorization_code som du har köpt i den första delen i flödet.                                                                                                                                                                                                                                                                                                                                                                   |
| redirect_uri  | obligatorisk              | Samma redirect_uri värde som används för att hämta authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | krävs för web apps | Programhemlighet som du skapade i portalen för registrering av app för din app. Den bör inte användas i en inbyggd app eftersom client_secrets inte lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er som har möjlighet att lagra client_secret på ett säkert sätt på serversidan.  Klienthemlighet måste vara URL-kodat innan de skickas.                                                                                                                    |
| code_verifier | valfri              | Den samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE har använts i auktoriseringsbegäran kod bevilja. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar för token kommer att se ut:

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
| access_token  | Den begärda åtkomst-token. Appen kan använda den här token för att autentisera till den säkra resursen, till exempel ett webb-API.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är ägar                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Hur länge den åtkomst-token är giltig (i sekunder).                                                                                                                                                                                                                                                                                                                                                                                                       |
| omfång         | Scope som gäller för access_token.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0-uppdateringstoken. Appen kan använda den här token skaffa ytterligare åtkomsttoken när den aktuella åtkomst-token upphör att gälla. Refresh_tokens är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. Mer information finns i den [v2.0 tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `offline_access` omfång begärdes.                                               |
| id_token      | En osignerad JSON Web Token (JWT). Appen kan base64Url avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i den [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `openid` omfång begärdes. |
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
| fel             | En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel.          |
| error_codes       | En lista över STS-specifika felkoder som kan hjälpa i diagnostik.                                                |
| tidsstämpel         | Den tid då felet inträffade.                                                                           |
| trace_id          | En unik identifierare för begäran som kan hjälpa i diagnostik.                                               |
| correlation_id    | En unik identifierare för begäran som kan hjälpa i diagnostik för komponenter.                             |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för tokenslutpunkt fel
| Felkod              | Beskrivning                                                                                                           | Klientåtgärd                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Protokollfel, till exempel en obligatorisk parameter saknas.                                                               | Åtgärda och skicka begäran igen                                                                                                                                                                                                                |
| invalid_grant           | Auktoriseringskod eller PKCE kod verifierare är ogiltigt eller har upphört att gälla.                                             | Testa en ny begäran till den `/authorize` slutpunkt och kontrollera att parametern code_verifier var korrekt.                                                                                                                                   |
| unauthorized_client     | Den autentiserade klienten har inte behörighet att använda den här beviljandetypen för auktorisering.                                     | Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| invalid_client          | Klientautentiseringen misslyckades.                                                                                        | Med klientens autentiseringsuppgifter är inte giltiga. Du kan åtgärda genom uppdaterar programadministratören autentiseringsuppgifterna.                                                                                                                                       |
| unsupported_grant_type  | Auktoriseringsservern stöder inte beviljandetypen för auktorisering.                                              | Ändra beviljandetyp i begäran. Den här typen av fel ska ske under utveckling och identifieras under första testningen.                                                                                                      |
| invalid_resource        | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. | Detta anger resursen om den finns, inte har konfigurerats på klienten. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD.                                           |
| interaction_required    | Begäran kräver interaktion från användaren. Till exempel krävs ett steg för ytterligare autentisering.                   | Försök igen med samma resurs.                                                                                                                                                                                                  |
| temporarily_unavailable | Servern är tillfälligt för upptagen för att hantera begäran.                                                            | Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd.                                                                                                                |

## <a name="use-the-access-token"></a>Använd åtkomsttoken
Nu när du har skaffat har en `access_token`, du kan använda token i begäranden till webb-API: er genom att inkludera den i den `Authorization` rubrik:

> [!TIP]
> Kör den här begäran i Postman! (Ersätt den `Authorization` rubrik första) [ ![kör i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Uppdatera åtkomsttoken
Access_tokens är korta bott och du måste uppdatera dem när de går ut om du vill fortsätta få åtkomst till resurser. Kan du göra det genom att skicka in en annan `POST` begäran till den `/token` slutpunkten, den här gången erbjuder den `refresh_token` i stället för den `code`:

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
> Försök att köra den här begäran i Postman! (Glöm inte att ersätta den `refresh_token`) [ ![kör i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parameter     |                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient        | obligatorisk              | Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                  |
| client_id     | obligatorisk              | Programmet med ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.                                                                                                                                                                                                                                                            |
| _typ av beviljande    | obligatorisk              | Måste vara `refresh_token` för den här delen i auktoriseringskodsflödet.                                                                                                                                                                                                                                                                                                                                                                                                    |
| omfång         | obligatorisk              | En blankstegsavgränsad lista med omfattningar. Omfattningar som efterfrågas i det här ben måste vara motsvarar eller en delmängd av scope som efterfrågas i det ursprungliga authorization_code begäran ben. Om omfattningar som angetts i den här begäran sträcker sig över flera resursservern, returnerar en token för den resurs som angetts i det första omfånget v2.0-slutpunkten. En mer detaljerad förklaring av omfång finns [behörigheter och samtycke scope](active-directory-v2-scopes.md). |
| refresh_token | obligatorisk              | Refresh_token som du har köpt i den andra delen i flödet.                                                                                                                                                                                                                                                                                                                                                                                                      |
| redirect_uri  | obligatorisk              | Samma redirect_uri värde som används för att hämta authorization_code.                                                                                                                                                                                                                                                                                                                                                                                            |
| client_secret | krävs för web apps | Programhemlighet som du skapade i portalen för registrering av app för din app. Den bör inte användas i en inbyggd app eftersom client_secrets inte lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er som har möjlighet att lagra client_secret på ett säkert sätt på serversidan.                                                                                                                                                    |

#### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar för token kommer att se ut:

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
| access_token  | Den begärda åtkomst-token. Appen kan använda den här token för att autentisera till den säkra resursen, till exempel ett webb-API.                                                                                                                                                                                                                                                                                                                                     |
| token_type    | Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är ägar                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Hur länge den åtkomst-token är giltig (i sekunder).                                                                                                                                                                                                                                                                                                                                                                                                        |
| omfång         | Scope som gäller för access_token.                                                                                                                                                                                                                                                                                                                                                                                                          |
| refresh_token | En ny OAuth 2.0-uppdateringstoken. Med den här nyligen förvärvade uppdateringstoken för att säkerställa uppdaterings-tokens är giltig så länge som möjligt bör du ersätta den gamla uppdateringstoken. <br> **Obs:** endast angivna om `offline_access` omfång begärdes.                                                                                                                                                                                                |
| id_token      | En osignerad JSON Web Token (JWT). Appen kan base64Url avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i den [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `openid` omfång begärdes. |

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
| fel             | En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description | Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel.           |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa i diagnostik. |
| tidsstämpel |Den tid då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan hjälpa i diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa i diagnostik för komponenter. |

En beskrivning av felkoder och rekommenderade klientåtgärd i [felkoder för tokenslutpunkt fel](#error-codes-for-token-endpoint-errors).
