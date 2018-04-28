---
title: Skydda en sida av program med Azure AD v2.0 implicita flödet | Microsoft Docs
description: Skapa webbprogram med hjälp av Azure AD v2.0 implementering av implicita flödet för appar med enstaka sidor.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: b855dcaae99e16aa21a0e19ad37d933cb18c678a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protokoll - SPA med det implicita flödet
Med v2.0-slutpunkten du loggar in användare i din ensidesappar med både personliga och arbete/skola konton från Microsoft. Sida och andra JavaScript-appar som körs i första hand i en webbläsare yta några intressanta utmaningar när det gäller att autentisering:

* Säkerhetsegenskaper för dessa appar är skiljer sig från traditionella serverbaserade webbprogram.
* Många servrar för auktorisering och identitetsleverantörer har inte stöd för CORS-begäranden.
* Helsida webbläsare omdirigerar från appen blir särskilt inkräktande av användarupplevelsen.

För dessa program (tror: AngularJS, Ember.js, React.js, osv) Azure AD stöder OAuth 2.0 Implicit Grant-flödet.  Implicita flödet beskrivs i den [OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749#section-4.2).  Den största fördelen är att tillåter appen att hämta token från Azure AD utan att utföra en backend-servern autentiseringsuppgifter exchange.  Detta tillåter appen att logga in användaren, underhålla sessionen och hämta token till andra webb-API: er inuti klienten JavaScript-kod.  Det finns några viktiga säkerhetsaspekter ta hänsyn till när du använder det implicita flödet - specifikt runt [klienten](http://tools.ietf.org/html/rfc6749#section-10.3) och [personifiering](http://tools.ietf.org/html/rfc6749#section-10.3).

Om du vill använda implicita flödet och Azure AD för att lägga till autentisering i appen JavaScript vi rekommenderar att du använder våra öppen källkod JavaScript-bibliotek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Det finns några AngularJS självstudier [här](active-directory-appmodel-v2-overview.md#getting-started) som hjälper dig att komma igång.  

Om du inte vill använda ett bibliotek i appens sida och skicka protokollmeddelanden själv, följer du de allmänna stegen nedan.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="protocol-diagram"></a>Protokollet diagram
Hela implicit inloggning flödet ser ut ungefär så här: varje steg beskrivs i detalj nedan.

![OpenId Connect spaltformat som illustrerar](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Skicka begäran om inloggning
Om du vill logga in användaren ursprungligen på din app, kan du skicka en [OpenID Connect](active-directory-v2-protocols-oidc.md) auktoriseringsbegäran och få en `id_token` från v2.0-slutpunkten:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Klicka på länken nedan för att utföra den här begäran! När du har registrerat webbläsaren ska omdirigeras till `https://localhost/myapp/` med en `id_token` i adressfältet.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare.  Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| client_id |Krävs |Programmet ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app. |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning.  Det kan också innehålla response_type `token`. Med hjälp av `token` här kommer att din app kan ta emot en åtkomst-token direkt från slutpunkten för auktorisering utan att behöva göra en andra begäran till slutpunkten för auktorisering. Om du använder den `token` response_type, den `scope` parameter måste innehålla ett scope som anger vilken resurs för att utfärda token för. |
| redirect_uri |Rekommenderas |Redirect_uri för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en redirect_uris som du har registrerat i portalen, förutom det måste vara url-kodade. |
| omfång |Krävs |En blankstegsavgränsad lista över scope. Det måste innehålla omfånget för OpenID Connect `openid`, vilket innebär att behörigheten ”logga in dig på” i medgivande Användargränssnittet. Alternativt kan du också inkludera den `email` eller `profile` [scope](active-directory-v2-scopes.md) för att få åtkomst till ytterligare användardata. Du kan också omfatta andra scope i den här förfrågan för att begära tillstånd till olika resurser. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Bör vara `fragment` för implicita flödet. |
| state |Rekommenderas |Ett värde som ingår i denna begäran kommer också att returneras i token svaret. Det kan vara en sträng med innehåll som du vill.  Ett slumpmässigt genererat unikt värde används vanligtvis för [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| temporärt ID |Krävs |Ett värde som ingår i denna begäran som genererats av den app som ska inkluderas i den resulterande id_token som ett anspråk.  Appen kan sedan kontrollera värdet för att minimera token replay-attacker. Värdet är vanligtvis en slumpmässig, unik sträng som används för att identifiera ursprunget för begäran. |
| kommandotolk |valfri |Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena just nu är ”inloggning” none, och 'godkännande'.  `prompt=login` Tvingar användaren att ange sina autentiseringsuppgifter på begäran, giltigt att negera enkel inloggning på.  `prompt=none` är motsatsen - säkerställer att användaren inte visas med en interaktiv prompt som helst. Om begäran inte kan slutföras utan meddelanden via enkel inloggning på returneras ett fel v2.0-slutpunkten.  `prompt=consent` dialogrutan OAuth-medgivande utlöses när användaren loggar in, be användaren att tilldela behörigheter till appen. |
| login_hint |valfri |Kan användas till att fylla före adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sitt lösenord i förväg. Ofta appar kommer att använda den här parametern under återautentiseringen redan har extraherats användarnamnet från en tidigare inloggning med hjälp av den `preferred_username` anspråk. |
| domain_hint |valfri |Kan vara något av `consumers` eller `organizations`. Om den hoppar över e-postbaserad identifieringsprocessen användaren som passerar på v2.0 inloggningssidan, vilket leder till en något mer effektiv användarupplevelse.  Ofta appar kommer att använda den här parametern under återautentiseringen genom att extrahera den `tid` anspråk från id_token.  Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, bör du använda `domain_hint=consumers`.  Annars använder `domain_hint=organizations`. |

Nu ombeds användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen.  V2.0-slutpunkten säkerställer också att användaren har godkänt för de behörigheter som anges i den `scope` Frågeparametern.  Om användaren inte har godkänt till någon av dessa behörigheter, uppmanas användaren att godkänna behörigheterna som krävs.  Information om [behörigheter, medgivande och flera innehavare appar finns här](active-directory-v2-scopes.md).

När användaren autentiserar och ger ditt medgivande, v2.0-slutpunkten returnerar ett svar på din app på den angivna `redirect_uri`, med hjälp av den metod som anges i den `response_mode` parameter.

#### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med `response_mode=fragment` och `response_type=id_token+token` ser ut som följande, med radbrytningar för läsbarhet:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Inkluderade om `response_type` innehåller `token`. Åtkomsttoken som appen begärt i det här fallet för Microsoft Graph. Åtkomst-token ska inte avkodas eller annars kontrolleras, den kan hanteras som en täckande sträng. |
| token_type |Inkluderade om `response_type` innehåller `token`. Kommer alltid att `Bearer`. |
| expires_in |Inkluderade om `response_type` innehåller `token`. Anger antalet sekunder som denna token är giltig för cachelagring. |
| omfång |Inkluderade om `response_type` innehåller `token`.  Anger område(n) som access_token ska vara giltig. |
| id_token |Id_token som begärts av appen. Du kan använda id_token för att verifiera användarens identitet och starta en session med användaren.  Mer information om id_tokens och deras innehåll ingår i den [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska. |

#### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

## <a name="validate-the-idtoken"></a>Verifiera id_token
Ta bara emot en id_token räcker inte att autentisera användaren. Du måste verifiera signaturen för den id_token och kontrollera anspråk i token per krav som din app.  V2.0-slutpunkten använder [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Du kan välja att validera den `id_token` i klienten koden, men en vanlig metod är att skicka den `id_token` till backend-servern och utföra valideringen det.  När du har validerat signaturen för id_token, finns det några anspråk uppmanas du att verifiera.  Finns det [v2.0 tokenreferens](active-directory-v2-tokens.md) mer information inklusive [verifierar token](active-directory-v2-tokens.md#validating-tokens) och [viktig Information om signering nyckeln förnyelse](active-directory-v2-tokens.md#validating-tokens).  Vi rekommenderar utnyttjar ett bibliotek av parsning och validera token - det finns minst en tillgänglig för de flesta språk och plattformar.
<!--TODO: Improve the information on this-->

Du kan också kontrollera ytterligare anspråk beroende på ditt scenario.  Några vanliga verifieringar inkluderar:

* Att säkerställa användarorganisation har registrerat dig för appen.
* Att användaren har rätt behörighet/behörighet
* Säkerställa en vissa styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

Mer information om anspråk i en id_token finns i [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md).

När du har fullständigt validerat id_token, kan du starta en session med användaren och använda anspråk i id_token för att hämta information om användare i din app.  Den här informationen kan användas för att visa, poster, tillstånd och så vidare.

## <a name="get-access-tokens"></a>Få åtkomst-token
Nu när du har registrerat användaren i din app för enstaka sida, du kan hämta åtkomsttoken för anropande webb-API: er som skyddas av Azure AD, som den [Microsoft Graph](https://graph.microsoft.io).  Även om du redan fått en token med hjälp av den `token` response_type, du kan använda den här metoden för att hämta token till ytterligare resurser utan att omdirigera användaren att logga in igen.

I det normala flödet för OpenID Connect/OAuth, ska du göra detta genom att göra en begäran till v2.0 `/token` slutpunkt.  V2.0-slutpunkten stöder dock inte CORS begäranden så AJAX-anrop för att hämta och uppdatera token är utanför frågan.  Du kan i stället använda implicita flödet i en dold iframe för att hämta ny token för andra webb-API: er: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Försök att kopiera och klistra in den nedan begäran till en ny flik i webbläsaren. (Glöm inte att ersätta den `domain_hint` och `login_hint` värden med korrekta värden för din användare)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare.  Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| client_id |Krävs |Programmet ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app. |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning.  Den kan också omfatta andra response_types som `code`. |
| redirect_uri |Rekommenderas |Redirect_uri för din app, där autentisering svar kan skickas och tas emot av din app.  Den måste matcha en redirect_uris som du har registrerat i portalen, förutom det måste vara url-kodade. |
| omfång |Krävs |En blankstegsavgränsad lista över scope.  För att hämta token omfattar alla [scope](active-directory-v2-scopes.md) du behöver för resursen av intresse. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app.  Kan vara något av `query`, `form_post`, eller `fragment`. |
| state |Rekommenderas |Ett värde som ingår i denna begäran kommer också att returneras i token svaret.  Det kan vara en sträng med innehåll som du vill.  Ett slumpmässigt genererat unikt värde används vanligtvis för att förhindra attacker med förfalskning av begäran.  Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| temporärt ID |Krävs |Ett värde som ingår i denna begäran som genererats av den app som ska inkluderas i den resulterande id_token som ett anspråk.  Appen kan sedan kontrollera värdet för att minimera token replay-attacker.  Värdet är vanligtvis en slumpmässig, unik sträng som används för att identifiera ursprunget för begäran. |
| kommandotolk |Krävs |Du bör använda för att uppdatera och hämta token i en dold iframe `prompt=none` så att iframe inte låser sig på inloggningssidan v2.0 och returnerar omedelbart. |
| login_hint |Krävs |För att uppdatera och hämta token i en dold iframe måste du inkludera användarnamnet för användaren i det här tipset för att kunna skilja mellan flera sessioner som användaren kan ha vid en viss tidpunkt. Användarnamnet kan extraheras från ett tidigare logga in med den `preferred_username` anspråk. |
| domain_hint |Krävs |Kan vara något av `consumers` eller `organizations`.  Uppdatera och hämta token i en dold iframe måste du inkludera domain_hint i begäran.  Du bör extrahera den `tid` anspråk från id_token av en tidigare inloggning att avgöra vilket värde som ska användas.  Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, bör du använda `domain_hint=consumers`.  Annars använder `domain_hint=organizations`. |

Tack till den `prompt=none` parameter för denna begäran antingen lyckas eller misslyckas omedelbart och återgå till ditt program.  Ett lyckat svar skickas till din app på den angivna `redirect_uri`, med hjälp av den metod som anges i den `response_mode` parameter.

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
| access_token |Den token som begärdes av appen. |
| token_type |Kommer alltid att `Bearer`. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska. |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| omfång |Scope som gäller för åtkomst-token. |

#### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på lämpligt sätt.  I fall av `prompt=none`, är ett förväntat fel:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

Om du får detta felmeddelande i iframe begäran måste användaren interaktivt logga in igen att hämta en ny token.  Du kan välja att hantera den här situationen i sättet som passar bäst för ditt program.

## <a name="validating-access-tokens"></a>Verifiera åtkomsttoken

När du har fått en access_token, se till att verifiera signaturen för token som följande anspråk. Du kan också välja att validera ytterligare anspråk baserat på ditt scenario. 

* **målgruppen** anspråk så att token är avsedd för att tilldelas till din app
* **utfärdaren** anspråk för att verifiera att token har utfärdats till din app av v2.0-slutpunkten
* **inte före** och **förfallotid** anspråk för att verifiera att token inte har gått ut

Mer information om anspråk finns i åtkomsttoken finns det [tokenreferens för v2.0-slutpunkten](active-directory-v2-tokens.md)

## <a name="refreshing-tokens"></a>Uppdatera token
Båda `id_token`s och `access_token`s upphör att gälla efter en kort tidsperiod, så måste du förbereda din app att uppdatera dessa säkerhetstoken med jämna mellanrum.  Om du vill uppdatera båda typerna av token kan du utföra samma dolda iframe begäran ovan med hjälp av den `prompt=none` parametern för att styra beteendet för Azure AD.  Om du vill få en ny `id_token`, måste du använda `response_type=id_token` och `scope=openid`, samt en `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Skicka en logga ut begäran
OpenIdConnect `end_session_endpoint` kan din app att skicka en begäran till v2.0-slutpunkten för att avsluta en användarsession och rensar cookies som angetts av v2.0-slutpunkten.  Om du vill registrera en användare utanför ett webbprogram fullständigt, ska din app avslutar sin egen session med användaren (vanligtvis genom att avmarkera en token-cache eller släppa cookies) och sedan dirigera webbläsaren om du vill:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare.  Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | Rekommenderas | Den URL som användaren ska returneras till när logga ut har slutförts. Det här värdet måste matcha en omdirigerings-URI: er som registrerats för programmet. Om inte ingår, visas användaren ett allmänt meddelande av v2.0-slutpunkten. |
