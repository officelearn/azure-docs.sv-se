---
title: Inloggning med en enda sida med implicit flöde
titleSuffix: Azure AD B2C
description: Lär dig hur du lägger till inloggning med en enda sida med hjälp av det implicita flödet för OAuth 2,0 med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e77e597fbd33a1f1358ecaa2d2aea3fe075a70f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187737"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Logga in på en enda sida med det implicita flödet för OAuth 2,0 i Azure Active Directory B2C

Många moderna program har en app-klient med en enda sida som skrivits främst i Java Script. Appen skrivs ofta med hjälp av ett ramverk som reagerar, vinkel eller Vue. js. Appar med en sida och andra JavaScript-appar som körs främst i en webbläsare har några ytterligare utmaningar för autentisering:

- Säkerhets egenskaperna för dessa appar skiljer sig från traditionella serverbaserade webb program.
- Många auktoriseringsregler och identitets leverantörer har inte stöd för frågor för resurs delning mellan ursprung (CORS).
- Hel Webbs Ides webbläsare omdirigeras bort från appen kan vara invasiv till användar upplevelsen.

För att stödja dessa program använder Azure Active Directory B2C (Azure AD B2C) det implicita flödet för OAuth 2,0. Det implicita beviljande flödet för OAuth 2,0-auktorisering beskrivs i [avsnittet 4,2 i OAuth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749). I implicit flöde tar appen emot token direkt från Azure Active Directory (Azure AD) auktorisera slut punkt, utan server-till-Server-utbyte. All autentiserings-och sessionshantering utförs helt i JavaScript-klienten med antingen en Omdirigerad sida eller en popup-ruta.

Azure AD B2C utökar standard OAuth 2,0-det implicita flödet till mer än enkel autentisering och auktorisering. Azure AD B2C introducerar [princip parametern](user-flow-overview.md). Med princip parametern kan du använda OAuth 2,0 för att lägga till principer till appen, till exempel registrering, inloggning och profil hantering användar flöden. I exempel-HTTP-begärandena i den här artikeln används **{Tenant}. onmicrosoft. com** som exempel. Ersätt `{tenant}` med namnet på din klient om du har ett sådant och har även skapat ett användar flöde.

Det implicita inloggnings flödet ser ut ungefär som på följande bild. Varje steg beskrivs i detalj senare i artikeln.

![Diagram över swimlanes-format som visar OpenID Connect implicita flödet](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden

När ditt webb program behöver autentisera användaren och köra ett användar flöde, kan användaren dirigera användaren till `/authorize` slut punkten. Användaren vidtar åtgärder beroende på användar flödet.

I den här förfrågan anger klienten de behörigheter som krävs för att hämta från användaren i `scope`-parametern och användar flödet som ska köras. För att få en känsla för hur begäran fungerar kan du försöka att klistra in begäran i en webbläsare och köra den. Ersätt `{tenant}` med namnet på din Azure AD B2C-klient. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID: t för det program som du tidigare har registrerat i din klient organisation. Ersätt `{policy}` med namnet på en princip som du har skapat i din klient, till exempel `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
|innehav| Ja | Namnet på din Azure AD B2C-klient|
|politik| Ja| Det användar flöde som ska köras. Ange namnet på ett användar flöde som du har skapat i Azure AD B2C klient organisationen. Till exempel: `b2c_1_sign_in`, `b2c_1_sign_up`eller `b2c_1_edit_profile`. |
| client_id | Ja | Det program-ID som [Azure Portal](https://portal.azure.com/) tilldelats till ditt program. |
| response_type | Ja | Måste innehålla `id_token` för OpenID Connect-inloggning. Den kan även innehålla svars typen `token`. Om du använder `token`kan appen omedelbart ta emot en åtkomsttoken från tillstånds slut punkten, utan att göra en andra begäran till behörighets slut punkten.  Om du använder `token` svars typen måste `scope`-parametern innehålla ett definitions område som anger vilken resurs som ska utfärda token för. |
| redirect_uri | Nej | Omdirigerings-URI för appen, där autentiseringsbegäranden kan skickas och tas emot av din app. Det måste exakt matcha en av de omdirigerings-URI: er som du har registrerat i portalen, förutom att den måste vara URL-kodad. |
| response_mode | Nej | Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app.  Använd `fragment`för implicita flöden. |
| omfång | Ja | En blankstegsavgränsad lista över omfång. Ett enda omfattnings värde indikerar Azure AD båda de behörigheter som begärs. `openid`s omfånget anger en behörighet för att logga in användaren och hämta data om användaren i form av ID-token. `offline_access` omfång är valfritt för Web Apps. Det anger att appen behöver en uppdateringstoken för att få åtkomst till resurser med lång livs längd. |
| state | Nej | Ett värde som ingår i begäran som också returneras i token-svaret. Det kan vara en sträng med innehåll som du vill använda. Vanligt vis används ett slumpmässigt genererat unikt värde för att förhindra förfalsknings attacker på begäran från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan. |
| Nnär | Ja | Ett värde som ingår i begäran (genereras av appen) som ingår i det resulterande ID-token som ett anspråk. Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Normalt är värdet en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. |
| visas | Nej | Typ av användar interaktion som krävs. För närvarande är det enda giltiga värdet `login`. Den här parametern tvingar användaren att ange sina autentiseringsuppgifter för denna begäran. Enkel inloggning träder inte i kraft. |

Nu uppmanas användaren att slutföra principens arbets flöde. Användaren kan behöva ange sitt användar namn och lösen ord, logga in med en social identitet, registrera dig för katalogen eller något annat antal steg. Användar åtgärder är beroende av hur användar flödet definieras.

När användaren har slutfört användar flödet returnerar Azure AD ett svar till din app med det värde som du använde för `redirect_uri`. Den metod som anges i parametern `response_mode` används. Svaret är exakt detsamma för var och en av användar åtgärds scenarierna, oberoende av det användar flöde som kördes.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar som använder `response_mode=fragment` och `response_type=id_token+token` ser ut ungefär så här, med rad brytningar för läsbarhet:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parameter | Beskrivning |
| --------- | ----------- |
| access_token | Den åtkomsttoken som appen begärde. |
| token_type | Värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. |
| expires_in | Hur lång tid som åtkomsttoken är giltig (i sekunder). |
| omfång | De omfattningar som token är giltigt för. Du kan också använda omfattningar för att cachelagra token för senare användning. |
| id_token | Den ID-token som appen begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. Mer information om ID-token och deras innehåll finns i [referens för Azure AD B2C-token](tokens-overview.md). |
| state | Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör verifiera att `state` värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar
Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem på rätt sätt:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som används för att klassificera typer av fel som inträffar. |
| error_description | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |
| state | Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör verifiera att `state` värden i begäran och svaret är identiska.|

## <a name="validate-the-id-token"></a>Validera ID-token

Det räcker inte att ta emot en ID-token för att autentisera användaren. Verifiera signaturen för ID-token och verifiera anspråk i token enligt appens krav. Azure AD B2C använder [JSON-Webbtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera tokens och kontrol lera att de är giltiga.

Många bibliotek med öppen källkod är tillgängliga för validering av JWTs, beroende på vilket språk du föredrar att använda. Överväg att utforska tillgängliga bibliotek med öppen källkod i stället för att implementera din egen verifierings logik. Du kan använda informationen i den här artikeln för att få hjälp att ta reda på hur du använder dessa bibliotek på rätt sätt.

Azure AD B2C har en slut punkt för OpenID Connect-metadata. En app kan använda slut punkten för att hämta information om Azure AD B2C vid körning. Den här informationen omfattar slut punkter, token innehåll och signerings nycklar för token. Det finns ett JSON-Metadatadokumentet för varje användar flöde i din Azure AD B2C klient. Till exempel finns Metadatadokumentet för det b2c_1_sign_in användar flödet i fabrikamb2c.onmicrosoft.com-klienten på:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

En av egenskaperna för detta konfigurations dokument är `jwks_uri`. Värdet för samma användar flöde skulle vara:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

För att avgöra vilket användar flöde som användes för att signera en ID-token (och var du hämtar metadata från) har du två alternativ. Först ingår användar flödes namnet i `acr`-anspråk i `id_token`. Information om hur du tolkar anspråk från en ID-token finns i [referens för Azure AD B2C-token](tokens-overview.md). Ditt andra alternativ är att koda användar flödet i värdet för parametern `state` när du utfärdar begäran. Avkoda sedan parametern `state` för att avgöra vilket användar flöde som användes. Antingen är metoden giltig.

När du har köpt Metadatadokumentet från slut punkten för OpenID Connect-metadata kan du använda de offentliga RSA-256-nycklarna (finns i den här slut punkten) för att verifiera signaturen för ID-token. Det kan finnas flera nycklar i den här slut punkten vid en angiven tidpunkt, som var och en identifieras av en `kid`. Rubriken för `id_token` innehåller också ett `kid`-anspråk. Den visar vilken av dessa nycklar som användes för att signera ID-token. Mer information, inklusive information om [validering av tokens](tokens-overview.md), finns i referens för [Azure AD B2C-token](tokens-overview.md).
<!--TODO: Improve the information on this-->

När du har verifierat signaturen för ID-token kräver flera anspråk verifiering. Några exempel:

* Verifiera `nonce`-anspråk för att förhindra repetition av token-attacker. Värdet bör vara det du angav i inloggnings förfrågan.
* Verifiera `aud`-anspråk för att säkerställa att ID-token har utfärdats för din app. Värdet ska vara appens program-ID.
* Verifiera `iat`-och `exp`-anspråk för att säkerställa att ID-token inte har gått ut.

Flera fler verifieringar som du bör utföra beskrivs i detalj i [OpenID Connect Core-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill verifiera ytterligare anspråk, beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren eller organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet och behörighet.
* Se till att en viss styrka autentisering har skett, till exempel med hjälp av Azure Multi-Factor Authentication.

Mer information om anspråk i en ID-token finns i [referens för Azure AD B2C-token](tokens-overview.md).

När du har verifierat ID-token kan du starta en session med användaren. I din app använder du anspråken i ID-token för att hämta information om användaren. Den här informationen kan användas för visning, poster, auktorisering och så vidare.

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om de enda saker som dina webbappar behöver göra är att köra användar flöden kan du hoppa över de kommande avsnitten. Informationen i följande avsnitt gäller endast för webb program som behöver göra autentiserade anrop till ett webb-API och som skyddas av Azure AD B2C.

Nu när du har loggat in användaren i en app med en enda sida kan du hämta åtkomsttoken för att anropa webb-API: er som skyddas av Azure AD. Även om du redan har tagit emot en token med `token` svars typen kan du använda den här metoden för att hämta tokens för ytterligare resurser utan att omdirigera användaren att logga in igen.

I ett typiskt webb program flöde skulle du göra en begäran till `/token` slut punkten. Slut punkten stöder dock inte CORS-begäranden, så gör AJAX-anrop för att hämta en uppdateringstoken inte ett alternativ. I stället kan du använda det implicita flödet i ett dolt HTML iframe-element för att hämta nya token för andra webb-API: er. Här är ett exempel med rad brytningar för läsbarhet:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|innehav| Krävs | Namnet på din Azure AD B2C-klient|
politik| Krävs| Det användar flöde som ska köras. Ange namnet på ett användar flöde som du har skapat i Azure AD B2C klient organisationen. Till exempel: `b2c_1_sign_in`, `b2c_1_sign_up`eller `b2c_1_edit_profile`. |
| client_id |Krävs |Det program-ID som har tilldelats din app i [Azure Portal](https://portal.azure.com). |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect-inloggning.  Den kan även innehålla svars typen `token`. Om du använder `token` här kan din app omedelbart ta emot en åtkomsttoken från tillstånds slut punkten, utan att göra en andra begäran till behörighets slut punkten. Om du använder `token` svars typen måste `scope`-parametern innehålla ett definitions område som anger vilken resurs som ska utfärda token för. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för appen, där autentiseringsbegäranden kan skickas och tas emot av din app. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i portalen, förutom att den måste vara URL-kodad. |
| omfång |Krävs |En blankstegsavgränsad lista över omfång.  Ta med alla omfattningar som krävs för den avsedda resursen för att hämta tokens. |
| response_mode |Rekommenderas |Anger den metod som används för att skicka den resulterande token tillbaka till din app. Använd `fragment`för implicit flöde. Två andra lägen kan anges, `query` och `form_post`, men fungerar inte i det implicita flödet. |
| state |Rekommenderas |Ett värde som ingår i begäran som returneras i token-svaret.  Det kan vara en sträng med innehåll som du vill använda.  Vanligt vis används ett slumpmässigt genererat unikt värde för att förhindra förfalsknings attacker på begäran från en annan plats.  Det här läget används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat. Till exempel visar sidan eller visar användaren. |
| Nnär |Krävs |Ett värde som ingår i begäran, som genereras av appen, som ingår i den resulterande ID-token som ett anspråk.  Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Normalt är värdet en slumpmässig, unik sträng som identifierar ursprunget för begäran. |
| visas |Krävs |Om du vill uppdatera och hämta tokens i en dold iframe använder du `prompt=none` för att se till att iframe inte fastnar på inloggnings sidan och returnerar omedelbart. |
| login_hint |Krävs |Om du vill uppdatera och hämta tokens i en dold iframe inkluderar du användar namnet för användaren i det här tipset för att skilja mellan flera sessioner som användaren kan ha vid en angiven tidpunkt. Du kan extrahera användar namnet från en tidigare inloggning genom att använda `preferred_username`-anspråk (`profile` omfånget krävs för att ta emot `preferred_username`-anspråk). |
| domain_hint |Krävs |Det kan vara `consumers` eller `organizations`.  För att uppdatera och hämta tokens i en dold iframe, inkludera `domain_hint`-värdet i begäran.  Extrahera `tid`-anspråk från ID-token för en tidigare inloggning för att avgöra vilket värde som ska användas (`profile` omfånget krävs för att ta emot `tid`-anspråk). Om värdet `tid` anspråk är `9188040d-6c67-4c5b-b112-36a304b66dad`använder du `domain_hint=consumers`.  Annars använder du `domain_hint=organizations`. |

Genom att ange parametern `prompt=none` Miss lyckas eller Miss lyckas denna begäran omedelbart och återgår till ditt program.  Ett lyckat svar skickas till din app vid angiven omdirigerings-URI, genom att använda metoden som anges i parametern `response_mode`.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med `response_mode=fragment` ser ut som i det här exemplet:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den token som appen begärde. |
| token_type |Tokentypen får alltid vara Bearer. |
| state |Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör verifiera att `state` värden i begäran och svaret är identiska. |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| omfång |De omfattningar som åtkomsttoken är giltig för. |

### <a name="error-response"></a>Fel svar
Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem på rätt sätt.  För `prompt=none`ser ett förväntat fel ut som det här exemplet:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren interaktivt logga in igen för att hämta en ny token.

## <a name="refresh-tokens"></a>Uppdatera token
ID-token och åtkomsttoken upphör att gälla efter en kort tids period. Din app måste vara beredd på att regelbundet uppdatera dessa token.  Om du vill uppdatera någon av typerna av token utför du samma dolda iframe-begäran som vi använde i ett tidigare exempel, genom att använda `prompt=none` parameter för att kontrol lera Azure AD-steg.  Om du vill ta emot ett nytt `id_token`-värde måste du använda `response_type=id_token` och `scope=openid`och en `nonce`-parameter.

## <a name="send-a-sign-out-request"></a>Skicka en inloggningsbegäran
När du vill signera användaren från appen omdirigerar du användaren till Azure AD för att logga ut. Om du inte omdirigerar användaren kan de kunna autentisera till din app igen utan att ange sina autentiseringsuppgifter igen eftersom de har en giltig enkel inloggnings-session med Azure AD.

Du kan helt enkelt omdirigera användaren till den `end_session_endpoint` som anges i samma OpenID för Connect-metadata som beskrivs i [validera ID-token](#validate-the-id-token). Några exempel:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| innehav | Ja | Namnet på din Azure AD B2C-klient |
| politik | Ja | Det användar flöde som du vill använda för att signera användaren från ditt program. |
| post_logout_redirect_uri | Nej | URL: en som användaren ska omdirigeras till efter en lyckad utloggning. Om den inte är inkluderad visar Azure AD B2C användaren ett allmänt meddelande. |
| state | Nej | Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrol lera att `state` värden i begäran och svaret är identiska. |


> [!NOTE]
> Genom att dirigera användaren till `end_session_endpoint` rensar du en del av användarens läge för enkel inloggning med Azure AD B2C. Den signerar dock inte användaren från användarens session med den sociala identitetsprovider. Om användaren väljer samma identitets leverantör under en efterföljande inloggning, autentiseras användaren igen utan att ange sina autentiseringsuppgifter. Om en användare vill logga ut från ditt Azure AD B2C-program, innebär det inte nödvändigt vis att de helt inte vill logga ut från sitt Facebook-konto, till exempel. För lokala konton avslutas dock användarens session på rätt sätt.
>

## <a name="next-steps"></a>Nästa steg

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Kod exempel: Hej. js med Azure AD B2C

[En Enkels Ides applikation som bygger på Hello. js med Azure AD B2C][github-hello-js-example] (GitHub)

Det här exemplet på GitHub är avsett att hjälpa dig att komma igång med Azure AD B2C i ett enkelt webb program som bygger på [Hello. js][github-hello-js] och använder autentisering med popup-format.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
