---
title: Säkra program med en enda sida med hjälp av implicit flöde för Microsoft Identity Platform | Azure
description: Skapa webb program med hjälp av Microsoft Identity Platform-implementering av det implicita flödet för appar på en sida.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab5ba4fde7469854954ed19d2e643f2b8a23f34f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193268"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform och implicit beviljande flöde

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Med Microsoft Identity Platform-slutpunkten kan du logga användare i appar med en sida med både personliga konton och arbets-eller skol konton från Microsoft. En enkel sida och andra JavaScript-appar som körs främst i en webbläsare och har några intressanta utmaningar när de kommer till autentisering:

* Säkerhetsegenskaperna för dessa appar skiljer sig avsevärt från traditionella serverbaserade webb program.
* Många auktoriseringsregler och identitets leverantörer stöder inte CORS-begäranden.
* Full sid webbläsare omdirigeras bort från appen, vilket är särskilt invasivt i användar upplevelsen.

För dessa program (AngularJS, maskininlärning. js, reagerar. js osv.) stöder Microsoft Identity Platform det OAuth-för implicit beviljande av OAuth-2,0. Det implicita flödet beskrivs i [OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2)-specifikationen. Den främsta fördelen är att den tillåter att appen hämtar token från Microsoft Identity Platform utan att utföra ett utbyte av autentiseringsuppgifter för backend-servern. Detta gör att appen kan logga in användaren, underhålla sessionen och hämta token till andra webb-API: er i klientens JavaScript-kod. Det finns några viktiga säkerhets aspekter att ta med i beräkningen när du använder det implicita flödet specifikt kring [klient](https://tools.ietf.org/html/rfc6749#section-10.3) -och [användarautentisering](https://tools.ietf.org/html/rfc6749#section-10.3).

Om du vill använda det implicita flödet och Microsoft Identity Platform för att lägga till autentisering i din JavaScript-app rekommenderar vi att du använder JavaScript-biblioteket med öppen källkod, [msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Om du däremot hellre inte vill använda ett bibliotek i en app med en enda sida och skicka protokoll meddelanden själv, följer du de allmänna stegen nedan.

> [!NOTE]
> Inte alla Azure Active Directory (Azure AD)-scenarier och-funktioner stöds av Microsoft Identity Platform-slutpunkten. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokoll diagram

Följande diagram visar hur hela det implicita inloggnings flödet ser ut och de avsnitt som följer beskriver varje steg i detalj.

![Diagram som visar det implicita inloggnings flödet](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

För att först signera användaren till din app kan du skicka en [OpenID Connect](v2-protocols-oidc.md) -autentiseringsbegäran och hämta en `id_token` från slut punkten för Microsoft Identity Platform.

> [!IMPORTANT]
> För att kunna begära en ID-token måste appens registrering på sidan [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) ha det implicita tilldelnings flödet aktiverat korrekt, genom att välja **åtkomsttoken** och **ID-token** under den **implicita tilldelningen** avsnittet. Om den inte är aktive rad `unsupported_response` returneras ett fel: **Det angivna värdet för Indataparametern response_type är inte tillåtet för den här klienten. Förväntat värde är ' Code '**

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
> Om du vill testa att logga in med det implicita flödet klickar du på <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> När du har loggat in bör webbläsaren omdirigeras `https://localhost/myapp/` till med `id_token` ett i adress fältet.
>

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` | obligatorisk |`{tenant}` Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` `consumers`, `organizations`, och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obligatorisk | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `response_type` | obligatorisk |Måste inkludera `id_token` för OpenID Connect-inloggning. Den kan även innehålla response_type `token`. Om `token` du använder detta kan din app ta emot en åtkomsttoken omedelbart från den auktoriserade slut punkten utan att behöva göra en andra begäran till behörighets slut punkten. Om du använder `token` response_type `scope` måste parametern innehålla ett definitions område som anger vilken resurs som ska utfärda token för. |
| `redirect_uri` | rekommenderas |Din app i redirect_uri där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. |
| `scope` | obligatorisk |En blankstegsavgränsad lista över [omfång](v2-permissions-and-consent.md). För att OpenID ska kunna ansluta måste den innehålla `openid`omfånget, som översätts till "logga in dig"-behörighet i medgivande gränssnittet. Du kanske också vill ta med `email` eller `profile` -omfattningarna för att få åtkomst till ytterligare användar data. Du kan också inkludera andra omfattningar i den här begäran för att begära medgivande till olika resurser. |
| `response_mode` | valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Standardvärdet för att fråga efter en åtkomsttoken, men fragment om begäran innehåller en id_token. |
| `state` | rekommenderas |Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `nonce` | obligatorisk |Ett värde som ingår i begäran, som genereras av appen, som kommer att ingå i den resulterande id_token som ett anspråk. Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. Krävs endast när en id_token begärs. |
| `prompt` | valfri |Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är "inloggning" "," none "," select_account "och" medgivande ". `prompt=login`tvingar användaren att ange sina autentiseringsuppgifter för den begäran och negera enkel inloggning. `prompt=none`är motsatt – det ser till att användaren inte visas med interaktiva prompter. Om begäran inte kan slutföras i bakgrunden via enkel inloggning, returnerar slut punkten för Microsoft Identity Platform ett fel. `prompt=select_account`skickar användaren till en konto väljare där alla konton som lagras i sessionen kommer att visas. `prompt=consent`utlöser dialog rutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörighet till appen. |
| `login_hint`  |valfri |Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar kommer ofta att använda den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med `preferred_username` hjälp av anspråket.|
| `domain_hint` | valfri |Kan vara en av `consumers` eller `organizations`. Om den är inkluderad hoppar den e-postbaserad identifierings processen som användaren går igenom på inloggnings sidan, vilket leder till en något mer strömlinjeformad användar upplevelse. Appar använder ofta den här parametern vid omautentisering genom att extrahera `tid` anspråket från id_token. Om anspråk svärdet är `9188040d-6c67-4c5b-b112-36a304b66dad` (Microsoft-kontots klient organisation) bör du använda `domain_hint=consumers`. `tid` Annars kan du använda `domain_hint=organizations` vid ny autentisering. |

Användaren uppmanas att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slut punkten för Microsoft Identity Platform ser också till att användaren har samtyckt till de behörigheter som anges i `scope` Frågeparametern. Om användaren har samtyckt till **ingen** av dessa behörigheter uppmanas användaren att godkänna de behörigheter som krävs. Mer information finns i [behörigheter, medgivande och appar för flera klient organisationer](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner godkännandet, returnerar Microsoft Identity Platform-slutpunkten ett svar till din app på den `redirect_uri`angivna metoden med hjälp av metoden `response_mode` som anges i parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar `response_mode=fragment` med `response_type=id_token+token` och ser ut ungefär så här (med rad brytningar för läsbarhet):

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
| `access_token` |Ingår om `response_type` innehåller `token`. Den åtkomsttoken som appen begärde, i det här fallet för Microsoft Graph. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` |Ingår om `response_type` innehåller `token`. Är alltid `Bearer`. |
| `expires_in`|Ingår om `response_type` innehåller `token`. Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` |Ingår om `response_type` innehåller `token`. Anger de omfång som access_token ska vara giltiga för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md). <br> **Obs:** Anges endast om `openid` omfattning begärdes. |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

## <a name="validate-the-id_token"></a>Verifiera id_token

Det räcker bara att ta emot en id_token för att autentisera användaren. Du måste också verifiera id_token's-signaturen och verifiera anspråk i token baserat på appens krav. Slut punkten för Microsoft Identity Platform använder [JSON-Webbtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera tokens och kontrol lera att de är giltiga.

Du kan välja att validera `id_token` i-klient koden, men en vanlig metod är att `id_token` skicka till en backend-server och utföra verifieringen där. När du har verifierat signaturen för id_token måste du kontrol lera några anspråk. Se referensen för mer information, inklusive [validera token](id-tokens.md#validating-an-id_token) och [viktig information om förnyelse av signerings nyckel](active-directory-signing-key-rollover.md). [ `id_token` ](id-tokens.md) Vi rekommenderar att du använder ett bibliotek för att parsa och validera token – det finns minst en tillgänglig för de flesta språk och plattformar.

Du kanske också vill verifiera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet/privilegier.
* Att se till att en viss styrka autentisering har skett, t. ex. Multi-Factor Authentication.

När du har verifierat id_token kan du starta en-session med användaren och använda anspråken i id_token för att hämta information om användaren i din app. Den här informationen kan användas för visning, poster, anpassning med mera.

## <a name="get-access-tokens"></a>Hämta åtkomsttoken

Nu när du har loggat in användaren i en app med en enda sida kan du få åtkomsttoken för att anropa webb-API: er som skyddas av Microsoft Identity Platform, till exempel [Microsoft Graph](https://developer.microsoft.com/graph). Även om du redan har tagit emot en token med `token` response_type kan du använda den här metoden för att hämta tokens till ytterligare resurser utan att behöva omdirigera användaren för att logga in igen.

I det normala OpenID Connect/OAuth-flödet gör du detta genom att göra en begäran till Microsoft Identity Platform `/token` -slutpunkten. Slut punkten för Microsoft Identity Platform stöder dock inte CORS-begäranden, så gör AJAX-anrop för att hämta och uppdatera token utanför frågan. I stället kan du använda det implicita flödet i en dold iframe för att hämta nya token för andra webb-API: er: 

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

Information om frågeparametrar i URL: en finns i [Skicka inloggnings förfrågan](#send-the-sign-in-request).

> [!TIP]
> Försök kopiera & att klistra in nedanstående begäran i en webbläsare-flik! (Glöm inte att ersätta `login_hint` värdena med rätt värde för din användare)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Tack vare parametern kommer den här begäran antingen att lyckas eller Miss lyckas omedelbart och återgå till ditt program. `prompt=none` Ett lyckat svar skickas till din app `redirect_uri`med den angivna metoden `response_mode` i parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar `response_mode=fragment` med ser ut så här:

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
| `access_token` |Ingår om `response_type` innehåller `token`. Den åtkomsttoken som appen begärde, i det här fallet för Microsoft Graph. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` | Är alltid `Bearer`. |
| `expires_in` | Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` | Anger de omfång som access_token ska vara giltiga för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Ingår om `response_type` innehåller `id_token`. Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [ `id_token` referensen](id-tokens.md). <br> **Obs:** Anges endast om `openid` omfattning begärdes. |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till `redirect_uri` så att appen kan hantera dem på rätt sätt. `prompt=none`Om ett förväntat fel är:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren interaktivt logga in igen för att hämta en ny token. Du kan välja att hantera det här fallet på det sätt som passar ditt program.

## <a name="refreshing-tokens"></a>Uppdatera token

Den implicita beviljandet tillhandahåller inte uppdaterade tokens. Både `id_token` och`access_token`s upphör att gälla efter en kort tids period, så din app måste vara för beredd för att uppdatera dessa token regelbundet. Om du vill uppdatera någon av typerna av token kan du utföra samma dolda iframe-begäran från `prompt=none` ovan med parametern för att styra identitets plattformens beteende. Om du vill ta emot en ny `id_token`måste du använda `response_type=id_token` och `scope=openid`, samt en `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning

OpenID Connect `end_session_endpoint` gör att din app kan skicka en begäran till Microsoft Identity Platform-slutpunkten för att avsluta en användares session och Rensa cookies som anges av Microsoft Identity Platform-slutpunkten. För att fullständigt signera en användare från ett webb program bör din app avsluta sin egen session med användaren (vanligt vis genom att rensa en token-cache eller släppa cookies) och sedan omdirigera webbläsaren till:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` |obligatorisk |`{tenant}` Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` `consumers`, `organizations`, och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | rekommenderas | URL: en som användaren ska returneras till när utloggning har slutförts. Värdet måste matcha en av de omdirigerings-URI: er som registrerats för programmet. Om detta inte anges visas ett allmänt meddelande för användaren av Microsoft Identity Platform-slutpunkten. |

## <a name="next-steps"></a>Nästa steg

* Gå över [MSAL JS](sample-v2-code.md) -exemplen för att komma igång med kodning.
