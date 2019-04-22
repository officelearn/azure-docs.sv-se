---
title: Skydda enkelsidigt program med hjälp av det implicita flödet för Microsoft identity-plattformen | Azure
description: Skapa webbprogram med Microsoft identity-plattformen implementeringen av det implicita flödet för enkelsidiga appar.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
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
ms.openlocfilehash: d517828b30629cd9dfba5459b1d90913d8bc4f77
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698460"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft identity-plattformen och Implicit beviljat flöde

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Med Microsoft identity-plattformen slutpunkt kan du registrera användare i dina enkelsidiga appar med både personliga och arbets eller skolkonto konton från Microsoft. Enkelsidigt och andra JavaScript-appar som körs i första hand i ett ansikte i webbläsaren några intressanta utmaningar när det gäller autentisering:

* Security-egenskaperna för dessa appar skiljer sig avsevärt från traditionell server-baserade webbprogram.
* Många servrar för auktorisering och identitetsprovidrar har inte stöd för CORS-förfrågningar.
* Helsida webbläsare omdirigerar bort från appen blir särskilt inkräktande användarupplevelsen.

Microsoft identity-plattformen stöder Implicit beviljande av OAuth 2.0-flöde för dessa program (AngularJS, Ember.js, React.js och så vidare). Det implicita flödet beskrivs i den [OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.2). Den största fördelen är att den tillåter appen att hämta token från Microsoft identity-plattformen utan att utföra en backend-servern autentiseringsutbyte. På så sätt kan appen att logga in användaren, hålla sessionen aktiv och hämta token till andra webb-API: er inom klienten JavaScript-kod. Det finns några viktiga säkerhetsöverväganden ta hänsyn till när du använder det implicita flödet specifikt cirka [klienten](https://tools.ietf.org/html/rfc6749#section-10.3) och [användarpersonifiering](https://tools.ietf.org/html/rfc6749#section-10.3).

Om du vill använda implicit flöde och Microsoft identity-plattformen för att lägga till autentisering i din app med JavaScript, vi rekommenderar att du använder JavaScript-bibliotek med öppen källkod, [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Om du inte föredrar att använda ett bibliotek i dina program och skicka protokollmeddelanden dig själv, följa de allmänna stegen nedan.

> [!NOTE]
> Inte alla Azure Active Directory (Azure AD)-scenarier och funktioner stöds av Microsoft identity-plattformen slutpunkten. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokollet diagram

Följande diagram visar hur det hela implicita inloggning flödet ser ut och avsnitten som följer beskriver varje steg i detalj.

![OpenID Connect spaltformat](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Skicka begäran inloggning

Om du vill logga först in användaren i din app, kan du skicka en [OpenID Connect](v2-protocols-oidc.md) autentiseringsbegäran och få en `id_token` från slutpunkten för Microsoft identity-plattformen.

> [!IMPORTANT]
> Att begära appregistreringen i en ID-token har den [Azure portal – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan måste ha implicit beviljande av flödet aktiverats korrekt, genom att välja **åtkomsttoken** och **ID-token** under den **Implicit beviljande** avsnittet. Om den inte är aktiverad, en `unsupported_response` fel returneras: **Det angivna värdet för Indataparametern 'response_type ”är inte tillåtet för den här klienten. Förväntat värde är ”code”**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Testa inloggningen med hjälp av det implicita flödet genom att klicka på <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Efter inloggningen webbläsaren ska omdirigeras till `https://localhost/myapp/` med en `id_token` i adressfältet.
>

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` | obligatorisk |Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obligatorisk | Programmet (klient)-ID som den [Azure portal – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan som tilldelats din app. |
| `response_type` | obligatorisk |Måste innehålla `id_token` för OpenID Connect-inloggning. De kan också innehålla response_type `token`. Med hjälp av `token` här gör att din app för att få en åtkomsttoken direkt från slutpunkten för auktorisering utan att behöva göra en andra begäran till slutpunkten för auktorisering. Om du använder den `token` response_type, den `scope` parametern måste innehålla en omfattning som anger vilken resurs för att utfärda token för. |
| `redirect_uri` | Rekommenderas |Redirect_uri för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av redirect_uris som du registrerade i portalen, men det måste vara url-kodas. |
| `scope` | obligatorisk |En blankstegsavgränsad lista över [scope](v2-permissions-and-consent.md). Det måste innehålla omfånget för OpenID Connect, `openid`, vilket innebär att behörigheten ”logga du in” i godkännande-UI. Alternativt kan du också vill ska ingå i `email` eller `profile` omfång för att få åtkomst till ytterligare användardata. Du kan också omfatta andra scope i den här begäran för att begära tillstånd att olika resurser. |
| `response_mode` | valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Standardinställningen är att fråga efter ett åtkomsttoken, men fragment om förfrågningen innehåller en id_token. |
| `state` | Rekommenderas |Ett värde som ingår i den begäran som också kommer att returneras i token-svaret. Det kan vara en sträng med innehåll som du önskar. Ett slumpmässigt genererat unikt värde som normalt används för [att förhindra attacker med förfalskning av begäran](https://tools.ietf.org/html/rfc6749#section-10.12). Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| `nonce` | obligatorisk |Ett värde som ingår i den begäran som skapats av appen, som ska tas med i den resulterande id_token som ett anspråk. Appen kan sedan att verifiera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig, unik sträng som kan användas för att fastställa ursprunget för begäran. Krävs endast när en id_token har begärts. |
| `prompt` | valfri |Anger vilken typ av interaktion från användaren som krävs. The only valid values at this time are 'login', 'none', 'select_account', and 'consent'. `prompt=login` kommer att tvinga användaren att ange sina autentiseringsuppgifter på begäran, vilket eliminerar enkel inloggning. `prompt=none` är motsatsen – det säkerställer att användaren inte visas den interaktiva prompten alls. Om begäran inte kan slutföras tyst via enkel inloggning, returnerar Microsoft identity-plattformen slutpunkten ett fel. `prompt=select_account` skickar användaren till en kontoväljare där alla konton som sparas i sessionen visas. `prompt=consent` utlöser OAuth godkännande i dialogrutan när användaren loggar in, ber användaren att bevilja behörigheter till appen. |
| `login_hint`  |valfri |Kan användas till att fylla förväg adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sina användarnamn i tid. Ofta appar kommer att använda den här parametern under återautentiseringen redan har extraherats användarnamnet från en tidigare logga in med den `preferred_username` anspråk.|
| `domain_hint` | valfri |Kan vara något av `consumers` eller `organizations`. Om inkluderat, den hoppar över e-postbaserad identifieringsprocessen som användaren som passerar på inloggningssidan, vilket leder till en något mer effektiv användarupplevelse. Ofta appar kommer att använda den här parametern under återautentiseringen genom att extrahera den `tid` anspråk från id_token. Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad` (Account konsument klienten), bör du använda `domain_hint=consumers`. Annars kan du använda `domain_hint=organizations` under autentiseras igen. |

Nu kan uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Microsoft identity-plattformen endpoint säkerställer också att användaren har samtyckt till behörigheterna som anges i den `scope` frågeparameter. Om användaren har samtyckt till **ingen** av dessa behörigheter, kommer den be användaren att godkänna behörigheterna som krävs. Mer information finns i [behörigheter och samtycke appar för flera klienter](v2-permissions-and-consent.md).

När användaren autentiserar och ger ditt medgivande, Microsoft identity-plattformen slutpunkten returnerar ett svar till din app på den angivna `redirect_uri`, med den metod som beskrivs i den `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` och `response_type=id_token+token` ser ut som följande (med radbrytningar för läsbarhet):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkluderade om `response_type` innehåller `token`. Åtkomst-token som appen begärdes i det här fallet för Microsoft Graph. Åtkomsttoken får inte avkodas eller annars kontrolleras, den ska behandlas som en täckande sträng. |
| `token_type` |Inkluderade om `response_type` innehåller `token`. Kommer alltid att `Bearer`. |
| `expires_in`|Inkluderade om `response_type` innehåller `token`. Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` |Inkluderade om `response_type` innehåller `token`. Anger område(n) som access_token ska gälla. Får inte innehålla alla omfattningar som begärts, om de inte gäller för användaren (när det gäller Azure AD endast scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Appen kan avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i den [ `id_token reference` ](id-tokens.md). <br> **Obs!** Endast angivna om `openid` omfång begärdes. |
| `state` |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska. |

#### <a name="error-response"></a>Felsvar

Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| `error_description` |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

## <a name="validate-the-idtoken"></a>Verifiera id_token

Bara tar emot en id_token är inte tillräckliga för att autentisera användaren. Du måste också verifiera signaturen för den id_token och verifiera anspråken i token baserat på dina appkrav. Microsoft identity-plattformen slutpunkten använder [JSON Web token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och verifiera att de är giltiga.

Du kan välja att verifiera den `id_token` i klienten kod, men en vanlig metod är att skicka den `id_token` till backend-servern och utföra valideringen det. När du har verifierat signaturen för id_token, finns det några anspråk som du kommer att behöva kontrollera. Se den [ `id_token` referens](id-tokens.md) för mer information, inklusive [verifiera token](id-tokens.md#validating-an-id_token) och [viktig information om signeringsnyckel](active-directory-signing-key-rollover.md). Vi rekommenderar att du utnyttjar ett bibliotek för parsning och validera token: det finns minst en tillgänglig för de flesta språk och plattformar.

Du kan också välja att validera ytterligare anspråk beroende på ditt scenario. Vissa vanliga verifieringar är:

* Att se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt auktorisering/behörighet.
* Att se till att en viss styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

När du har verifierat id_token du starta en session med användaren och använder anspråken i id_token för att hämta information om användare i din app. Den här informationen kan användas för visning, poster, anpassning och mycket mer.

## <a name="get-access-tokens"></a>Få åtkomst-token

Nu när du har registrerat användaren till din ensidesapp kan du kan få åtkomst-token för anropande webb-API: er som skyddas av Microsoft identity-plattformen, till exempel den [Microsoft Graph](https://developer.microsoft.com/graph). Även om du redan fått en token med den `token` response_type, du kan använda den här metoden för att hämta token till ytterligare resurser utan att behöva omdirigera användaren att logga in igen.

I det normala OpenID Connect/OAuth-flödet ska du göra detta genom att göra en begäran till Microsoft identity-plattformen `/token` slutpunkt. Microsoft identity-plattformen endpoint stöder dock inte CORS-förfrågningar, så gör AJAX-anrop för att hämta och uppdatera token är inte en chans. Du kan i stället använda det implicita flödet i en dold iframe för att hämta nya token för andra webb-API: er: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Mer information om Frågeparametrar i URL-Adressen finns [skicka tecknet i begäran](#send-the-sign-in-request).

> [!TIP]
> Försök kopiera och klistra in den nedan begäran till en ny flik i webbläsaren! (Glöm inte att ersätta den `login_hint` värden med rätt värde för dina användare)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Tack vare den `prompt=none` parametern denna begäran antingen lyckas eller misslyckas omedelbart och gå tillbaka till ditt program. Ett lyckat svar skickas till din app på den angivna `redirect_uri`, med den metod som beskrivs i den `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` ser ut som:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkluderade om `response_type` innehåller `token`. Åtkomst-token som appen begärdes i det här fallet för Microsoft Graph. Åtkomsttoken får inte avkodas eller annars kontrolleras, den ska behandlas som en täckande sträng. |
| `token_type` | Kommer alltid att `Bearer`. |
| `expires_in` | Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` | Anger område(n) som access_token ska gälla. Får inte innehålla alla omfattningar som begärts, om de inte gäller för användaren (när det gäller Azure AD endast scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Inkluderade om `response_type` innehåller `id_token`. Appen kan avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i den [ `id_token` referens](id-tokens.md). <br> **Obs!** Endast angivna om `openid` omfång begärdes. |
| `state` |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska. |

#### <a name="error-response"></a>Felsvar

Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt. I fall med `prompt=none`, ett oväntat fel kommer att:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| `error_description` |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

Om du får detta felmeddelande i iframe-begäran måste användaren interaktivt logga in igen att hämta en ny token. Du kan välja att hantera den här situationen i sättet som passar bäst för ditt program.

## <a name="validating-access-tokens"></a>Verifiera åtkomsttoken

När du får en access_token kan du se till att verifiera signaturen för token samt följande anspråk. Du kan också välja att validera ytterligare anspråk baserat på ditt scenario.

* **publik** anspråk så att token ska tilldelas till din app
* **utfärdaren** anspråk för att verifiera att token utfärdats av Microsoft identity-plattformen slutpunkten till din app
* **inte före** och **förfallotid** anspråk för att verifiera att token inte har gått ut

Mer information om anspråk som finns i åtkomst-token finns i den [komma åt tokenreferens](access-tokens.md)

## <a name="refreshing-tokens"></a>Uppdatera token

Implicit beviljande ger inte uppdateringstoken. Båda `id_token`s och `access_token`s upphör att gälla efter en kort tidsperiod, så att din app måste förberedas för att uppdatera dessa token med jämna mellanrum. Om du vill uppdatera valfri typ av token, du kan utföra samma dolda iframe-begäran, exempel med hjälp av den `prompt=none` parameter styr beteendet för identity-plattformen. Om du vill ta emot en ny `id_token`, se till att använda `response_type=id_token` och `scope=openid`, samt en `nonce` parametern.

## <a name="send-a-sign-out-request"></a>Skicka en utloggning begäran

OpenID Connect `end_session_endpoint` kan din app att skicka en begäran till Microsoft identity-plattformen slutpunkt för att avsluta en användarsession och rensa cookies som skapats av Microsoft identity-plattformen slutpunkten. För att fullständigt loggar en användare från ett webbprogram, bör din app avslutar sin egen session med användaren (vanligtvis genom att avmarkera en token-cache eller släppa cookies) och sedan dirigera webbläsaren om du vill:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` |obligatorisk |Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Rekommenderas | Den URL som användaren ska returneras till efter utloggning har slutförts. Det här värdet måste matcha en av omdirigerings-URI: er som har registrerats för programmet. Om inte inkluderat visas användaren ett allmänt meddelande av Microsoft identity-plattformen slutpunkten. |

## <a name="next-steps"></a>Nästa steg

* Gå över i [MSAL JS-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) att komma igång med programmeringen.
