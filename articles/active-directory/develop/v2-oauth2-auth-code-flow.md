---
title: OAuth auktoriseringskodflöde - Microsofts identitetsplattform | Azure
description: Skapa webbprogram med implementeringen av OAuth 2.0-autentiseringsprotokollet för Microsoft-identitetsplattformen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ac630c4901c126ed883adbdc7efb03f36372e6ff
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535884"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft-identitetsplattform och OAuth 2.0-auktoriseringskodflöde

Bevilja oauth 2.0-auktoriseringskod kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API:er. Med implementeringen av Microsofts identitetsplattform för OAuth 2.0 kan du lägga till inloggning och API-åtkomst till dina mobil- och skrivbordsappar. Den här guiden är språkoberoende och beskriver hur du skickar och ta emot HTTP-meddelanden utan att använda något av [Azures autentiseringsbibliotek med öppen källkod](reference-v2-libraries.md).

I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

> [!NOTE]
> Alla Azure Active Directory-scenarier & funktioner stöds inte av slutpunkten för Microsoft-identitetsplattformen. För att ta reda på om du ska använda slutpunkten för Microsoft identity platform läser du om begränsningar av [Microsofts identitetsplattform](active-directory-v2-limitations.md).

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). Den används för att utföra autentisering och auktorisering i de flesta apptyper, inklusive [webbappar](v2-app-types.md#web-apps) och [inbyggda appar](v2-app-types.md#mobile-and-native-apps). Flödet gör det möjligt för appar att på ett säkert sätt skaffa access_tokens som kan användas för att komma åt resurser som skyddas av slutpunkten för Microsoft-identitetsplattformen.

## <a name="protocol-diagram"></a>Protokolldiagram

På en hög nivå ser hela autentiseringsflödet för ett inbyggt/mobilprogram lite så här:

![Oauth Auth-kodflöde](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod

Auktoriseringskodflödet börjar med att klienten dirigerar användaren till `/authorize` slutpunkten. I den här begäran `openid`begär `offline_access`klienten , och `https://graph.microsoft.com/mail.read `behörigheter från användaren.  Vissa behörigheter är administratörsbegränsade, till exempel att `Directory.ReadWrite.All`skriva data till en organisations katalog med hjälp av . Om ditt program begär åtkomst till någon av dessa behörigheter från en organisationsanvändare får användaren ett felmeddelande som säger att de inte har behörighet att godkänna appens behörigheter. Om du vill begära åtkomst till administratörsbegränsade scope bör du begära dem direkt från en företagsadministratör.  Mer information finns i [Administratörsbegränsade behörigheter](v2-permissions-and-consent.md#admin-restricted-permissions).

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
> Klicka på länken nedan för att utföra denna begäran! När du har loggat in `https://localhost/myapp/` ska `code` din webbläsare omdirigeras till med en i adressfältet.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Obligatoriskt/valfritt | Beskrivning |
|--------------|-------------|--------------|
| `tenant`    | krävs    | Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | krävs    | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app.  |
| `response_type` | krävs    | Måste `code` inkludera för auktoriseringskodflödet.       |
| `redirect_uri`  | krävs | Den redirect_uri i din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av de redirect_uris du registrerat i portalen, förutom att den måste url-kodas. För inbyggda & mobilappar bör du använda `https://login.microsoftonline.com/common/oauth2/nativeclient`standardvärdet för .   |
| `scope`  | krävs    | En utrymmesavgränsad lista över [scope](v2-permissions-and-consent.md) som du vill att användaren ska godkänna.  För `/authorize` delen av begäran kan detta omfatta flera resurser, så att din app kan få samtycke till flera webb-API:er som du vill ringa. |
| `response_mode`   | Rekommenderas | Anger den metod som ska användas för att skicka tillbaka den resulterande token till din app. Kan vara något av följande:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`tillhandahåller koden som en frågesträngparameter på din omdirigerings-URI. Om du begär en ID-token med det implicita `query` flödet kan du inte använda det som anges i [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du bara begär koden kan du `query` `fragment`använda `form_post`, eller . `form_post`kör en POST som innehåller koden till din omdirigera URI. Mer information finns i [OpenID Connect-protokollet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Rekommenderas | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för [att förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Värdet kan också koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| `prompt`  | valfri    | Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena `login` `none`just `consent`nu är , och .<br/><br/>- `prompt=login`kommer att tvinga användaren att ange sina autentiseringsuppgifter på den begäran, förneka single-sign on.<br/>- `prompt=none`är motsatsen - det kommer att se till att användaren inte presenteras med någon interaktiv uppmaning som helst. Om begäran inte kan slutföras tyst via en inloggning returnerar slutpunkten `interaction_required` för Microsoft-identitetsplattformen ett fel.<br/>- `prompt=consent`utlöser dialogrutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörigheter till appen.<br/>- `prompt=select_account`kommer att avbryta enkel inloggning som ger kontoval erfarenhet lista alla konton antingen i session eller ett ihågkommen konto eller ett alternativ för att välja att använda ett annat konto helt och hållet.<br/> |
| `login_hint`  | valfri    | Kan användas för att förfylla fältet användarnamn/e-postadress på inloggningssidan för användaren, om du känner till deras användarnamn i förväg. Ofta använder appar den här parametern under omautentisering, efter att redan `preferred_username` ha extraherat användarnamnet från en tidigare inloggning med anspråket.   |
| `domain_hint`  | valfri    | Kan vara `consumers` en `organizations`av eller .<br/><br/>Om det inkluderas hoppar den över den e-postbaserade identifieringsprocessen som användaren går igenom på inloggningssidan, vilket leder till en något mer strömlinjeformad användarupplevelse. Ofta använder appar den här parametern under `tid` omautentisering genom att extrahera från ett tidigare inloggning. Om `tid` anspråksvärdet `9188040d-6c67-4c5b-b112-36a304b66dad`är bör `domain_hint=consumers`du använda . Annars kan `domain_hint=organizations`du använda .  |
| `code_challenge_method` | valfri    | Den metod som används `code_verifier` för `code_challenge` att koda för parametern. Kan vara något av följande värden:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Om uteslutas, `code_challenge` antas vara klartext om `code_challenge` ingår. Microsofts identitetsplattform stöder både `plain` och `S256`. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | valfri | Används för att säkra auktoriseringskodbidrag via Proof Key for Code Exchange (PKCE) från en infödd klient. Krävs `code_challenge_method` om ingår. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slutpunkten för Microsoft-identitetsplattformen säkerställer också att användaren har `scope` samtyckt till de behörigheter som anges i frågeparametern. Om användaren inte har samtyckt till någon av dessa behörigheter kommer den att be användaren att samtycka till de behörigheter som krävs. Information om [behörigheter, medgivande och appar för flera innehavare finns här](v2-permissions-and-consent.md).

När användaren har autentiserat och gett sitt samtycke returnerar slutpunkten för Microsoft `redirect_uri`identity platform ett svar `response_mode` till din app med den angivna metoden med den metod som anges i parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat `response_mode=query` svar med ser ut som:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beskrivning  |
|-----------|--------------|
| `code` | Det authorization_code som appen begärde. Appen kan använda auktoriseringskoden för att begära en åtkomsttoken för målresursen. Authorization_codes är kortlivade, vanligtvis de löper ut efter ca 10 minuter. |
| `state` | Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

#### <a name="error-response"></a>Felsvar

Felsvar kan också skickas `redirect_uri` till så att appen kan hantera dem på rätt sätt:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning  |
|----------|------------------|
| `error`  | En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktoriseringsslutpunktsfel

I följande tabell beskrivs de olika felkoder `error` som kan returneras i parametern för felsvaret.

| Felkod  | Beskrivning    | Klientåtgärd   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokollfel, till exempel en parameter som saknas som krävs. | Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel som vanligtvis fångas under inledande testning. |
| `unauthorized_client` | Klientprogrammet är inte tillåtet att begära en auktoriseringskod. | Det här felet uppstår vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| `access_denied`  | Resursägaren nekas samtycke  | Klientprogrammet kan meddela användaren att det inte kan fortsätta om inte användaren samtycker. |
| `unsupported_response_type` | Auktoriseringsservern stöder inte svarstypen i begäran. | Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel som vanligtvis fångas under inledande testning.  |
| `server_error`  | Servern påträffade ett oväntat fel.| Försök igen. Dessa fel kan bero på tillfälliga villkor. Klientprogrammet kan förklara för användaren att dess svar är försenat till ett tillfälligt fel. |
| `temporarily_unavailable`   | Servern är tillfälligt för upptagen för att hantera begäran. | Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt villkor. |
| `invalid_resource`  | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller den är inte korrekt konfigurerad. | Det här felet anger att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| `login_required` | För många eller inga användare hittades | Klienten begärde tyst`prompt=none`autentisering ( ), men en enskild användare kunde inte hittas. Detta kan innebära att det finns flera användare aktiva i sessionen eller inga användare. Detta tar hänsyn till den valda klienten (till exempel om det finns `consumers` två Azure AD-konton aktiva och ett Microsoft-konto, och väljs, kommer tyst autentisering att fungera). |
| `interaction_required` | Begäran kräver användarinteraktion. | Ett ytterligare autentiseringssteg eller samtycke krävs. Försök igen med `prompt=none`begäran utan . |

## <a name="request-an-access-token"></a>Begär en åtkomsttoken

Nu när du har skaffat en authorization_code och har beviljats behörighet `code` av `access_token` användaren kan du lösa in för en till önskad resurs. Gör detta genom `POST` att `/token` skicka en begäran till slutpunkten:

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
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Prova att köra denna begäran i Postman! (Glöm inte att ersätta `code`) [Prova att köra den här begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Obligatoriskt/valfritt | Beskrivning     |
|------------|-------------------|----------------|
| `tenant`   | krävs   | Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | krävs  | Program-ID (klient) som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sida som tilldelats din app. |
| `grant_type` | krävs   | Det `authorization_code` måste vara för auktoriseringskodflödet.   |
| `scope`      | krävs   | En utrymmesavskiljande lista över scope. De scope som begärs i detta ben måste motsvara eller en delmängd av de scope som begärs i det första benet. Scopen måste alla komma från en enda resurs, tillsammans`profile` `openid`med `email`OIDC-scope ( , , ). En mer detaljerad förklaring av omfattningar finns i [behörigheter, medgivande och omfattning .](v2-permissions-and-consent.md) |
| `code`          | krävs  | Den authorization_code som du fick i den första delen av flödet. |
| `redirect_uri`  | krävs  | Samma redirect_uri värde som användes för att förvärva authorization_code. |
| `client_secret` | krävs för webbappar | Programhemligheten som du skapade i appregistreringsportalen för din app. Du bör inte använda programhemligheten i en inbyggd app eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er, som har möjlighet att lagra client_secret säkert på serversidan.  Klienthemligheten måste url-kodas innan den skickas. För mer information klicka [här](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | valfri  | Samma code_verifier som användes för att få authorization_code. Krävs om PKCE användes i begäran om auktoriseringskodsbidrag. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Lyckat svar

Ett lyckat tokensvar kommer att se ut:

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

| Parameter     | Beskrivning   |
|---------------|------------------------------|
| `access_token`  | Den begärda åtkomsttoken. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel ett webb-API.  |
| `token_type`    | Anger tokentypsvärdet. Den enda typen som Azure AD stöder är Bearer |
| `expires_in`    | Hur länge åtkomsttoken är giltig (i sekunder). |
| `scope`         | De scope som access_token är giltiga för. |
| `refresh_token` | En OAuth 2.0-uppdateringstoken. Appen kan använda den här token som hämtar ytterligare åtkomsttoken när den aktuella åtkomsttoken har upphört att gälla. Refresh_tokens är långlivade och kan användas för att behålla åtkomsten till resurser under längre tidsperioder. Mer information om hur du uppdaterar en åtkomsttoken finns i [avsnittet nedan](#refresh-the-access-token). <br> **Anm.:** Endast om `offline_access` omfattning begärdes. |
| `id_token`      | En JSON-webbtoken (JWT). Appen kan avkoda segmenten för den här token för att begära information om användaren som loggade in. Appen kan cachelagra värdena och visa dem, men den bör inte förlita sig på dem för några auktoriserings- eller säkerhetsgränser. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md). <br> **Anm.:** Endast om `openid` omfattning begärdes. |

### <a name="error-response"></a>Felsvar

Felsvar kommer att se ut:

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

| Parameter         | Beskrivning    |
|-------------------|----------------|
| `error`       | En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |
| `error_codes` | En lista över STS-specifika felkoder som kan hjälpa till med diagnostik.  |
| `timestamp`   | Den tidpunkt då felet uppstod. |
| `trace_id`    | En unik identifierare för begäran som kan hjälpa till med diagnostik. |
| `correlation_id` | En unik identifierare för begäran som kan hjälpa till med diagnostik mellan komponenter. |

### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för tokenslutpunktsfel

| Felkod         | Beskrivning        | Klientåtgärd    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollfel, till exempel en parameter som saknas som krävs. | Åtgärda och skicka begäran igen   |
| `invalid_grant`    | Auktoriseringskoden eller PKCE-kodkontrollören är ogiltig eller har upphört att gälla. | Prova en ny `/authorize` begäran till slutpunkten och kontrollera att parametern code_verifier var korrekt.  |
| `unauthorized_client` | Den autentiserade klienten har inte behörighet att använda den här behörighetsbeviljandetypen. | Detta inträffar vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| `invalid_client` | Klientautentisering misslyckades.  | Klientautentiseringsuppgifterna är ogiltiga. För att åtgärda uppdaterar programadministratören autentiseringsuppgifterna.   |
| `unsupported_grant_type` | Auktoriseringsservern stöder inte behörighetsbidragstypen. | Ändra bidragstypen i begäran. Den här typen av fel bör endast uppstå under utvecklingen och upptäckas under inledande testning. |
| `invalid_resource` | Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller den är inte korrekt konfigurerad. | Detta indikerar att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD.  |
| `interaction_required` | Begäran kräver användarinteraktion. Ett ytterligare autentiseringssteg krävs till exempel. | Försök igen med samma resurs.  |
| `temporarily_unavailable` | Servern är tillfälligt för upptagen för att hantera begäran. | Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt villkor. |

## <a name="use-the-access-token"></a>Använda åtkomsttoken

Nu när du har förvärvat `access_token`en kan du använda token i begäranden till `Authorization` webb-API:er genom att inkludera den i sidhuvudet:

> [!TIP]
> Kör denna begäran i Postman! (Byt `Authorization` ut sidhuvudet först) [Prova att köra den här begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Uppdatera åtkomsttoken

Access_tokens är kortlivade och du måste uppdatera dem när de har upphört att gälla för att fortsätta komma åt resurser. Du kan göra det `POST` genom att `/token` skicka en annan `refresh_token` begäran till `code`slutpunkten, den här gången som ger i stället för .  Uppdateringstoken är giltiga för alla behörigheter som klienten redan har fått medgivande `scope=mail.read` för - alltså kan en `scope=api://contoso.com/api/UseResource`uppdateringstoken som utfärdats på en begäran om användas för att begära en ny åtkomsttoken för .

Uppdatera token har inte angivna livstider. Vanligtvis är livslängden för uppdateringstoken relativt lång. I vissa fall upphör dock uppdateringstoken att gälla, återkallas eller saknar tillräckliga privilegier för den önskade åtgärden. Ditt program måste förvänta sig och hantera [fel som returneras av slutpunkten för tokenutfärdande korrekt.](#error-codes-for-token-endpoint-errors)

Även om uppdateringstoken inte återkallas när de används för att hämta nya åtkomsttoken, förväntas du ignorera den gamla uppdateringstoken. [OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-6) säger: "Auktoriseringsservern kan utfärda en ny uppdateringstoken, i vilket fall klienten måste ignorera den gamla uppdateringstoken och ersätta den med den nya uppdateringstoken. Auktoriseringsservern KAN återkalla den gamla uppdateringstoken efter att ha utfärdat en ny uppdateringstoken till klienten."

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Prova att köra denna begäran i Postman! (Glöm inte att ersätta `refresh_token`) [Prova att köra den här begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parameter     |                | Beskrivning        |
|---------------|----------------|--------------------|
| `tenant`        | krävs     | Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | krävs    | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `grant_type`    | krävs    | Det `refresh_token` måste finnas för det här benet i auktoriseringskodflödet. |
| `scope`         | krävs    | En utrymmesavskiljande lista över scope. De scope som begärs i detta ben måste motsvara eller en delmängd av de scope som begärs i det ursprungliga authorization_code begäran. Om de scope som anges i den här begäran sträcker sig över flera resursserver returnerar slutpunkten för Microsoft identity platform en token för den resurs som anges i det första scopet. En mer detaljerad förklaring av omfattningar finns i [behörigheter, medgivande och omfattning .](v2-permissions-and-consent.md) |
| `refresh_token` | krävs    | Den refresh_token som du fick i den andra delen av flödet. |
| `client_secret` | krävs för webbappar | Programhemligheten som du skapade i appregistreringsportalen för din app. Det bör inte användas i en inbyggd app, eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er, som har möjlighet att lagra client_secret säkert på serversidan. Denna hemlighet måste url-kodas, för mer information klicka [här](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat tokensvar kommer att se ut:

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
| Parameter     | Beskrivning         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Den begärda åtkomsttoken. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel ett webb-API. |
| `token_type`    | Anger tokentypsvärdet. Den enda typen som Azure AD stöder är Bearer |
| `expires_in`    | Hur länge åtkomsttoken är giltig (i sekunder).   |
| `scope`         | De scope som access_token är giltiga för.    |
| `refresh_token` | En ny OAuth 2.0-uppdateringstoken. Du bör ersätta den gamla uppdateringstoken med den här nyligen förvärvade uppdateringstoken för att säkerställa att dina uppdateringstoken förblir giltiga så länge som möjligt. <br> **Anm.:** Endast om `offline_access` omfattning begärdes.|
| `id_token`      | En osignerad JSON-webbtoken (JWT). Appen kan avkoda segmenten för den här token för att begära information om användaren som loggade in. Appen kan cachelagra värdena och visa dem, men den bör inte förlita sig på dem för några auktoriserings- eller säkerhetsgränser. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md). <br> **Anm.:** Endast om `openid` omfattning begärdes. |

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

| Parameter         | Beskrivning                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel.           |
| `error_codes` |En lista över STS-specifika felkoder som kan hjälpa till med diagnostik. |
| `timestamp` | Den tidpunkt då felet uppstod. |
| `trace_id` | En unik identifierare för begäran som kan hjälpa till med diagnostik. |
| `correlation_id` | En unik identifierare för begäran som kan hjälpa till med diagnostik mellan komponenter. |

En beskrivning av felkoderna och den rekommenderade klientåtgärden finns i [Felkoder för tokenslutpunktsfel](#error-codes-for-token-endpoint-errors).
