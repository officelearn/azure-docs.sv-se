---
title: Inloggning på en sida med implicit flöde
titleSuffix: Azure AD B2C
description: Lär dig hur du lägger till ensidig inloggning med det implicita OAuth 2.0-flödet med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399005"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Inloggning på en sida med det implicita flödet OAuth 2.0 i Azure Active Directory B2C

Många moderna program har en enda sida app front end som är skriven främst i JavaScript. Ofta skrivs appen med hjälp av ett ramverk som React, Angular eller Vue.js. Ensidiga appar och andra JavaScript-appar som körs främst i en webbläsare har ytterligare några utmaningar för autentisering:

- Säkerhetsegenskaperna för dessa appar skiljer sig från traditionella serverbaserade webbprogram.
- Många auktoriseringsservrar och identitetsleverantörer stöder inte CORS-begäranden (Cross-Origin Resource Sharing).
- Helsideswebbläsare omdirigerar bort från appen kan vara invasiva för användarupplevelsen.

För att stödja dessa program använder Azure Active Directory B2C (Azure AD B2C) implicit flöde för OAuth 2.0. OAuth 2.0-auktoriseringen implicita bidragsflödet beskrivs i [avsnitt 4.2 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). I implicit flöde tar appen emot token direkt från Azure Active Directory (Azure AD) auktorisering av slutpunkt, utan något server-till-server-utbyte. All autentiseringslogik och sessionshantering sker helt i JavaScript-klienten med antingen en sidomdirigering eller en popup-ruta.

Azure AD B2C utökar standard OAuth 2.0 implicit flöde till mer än enkel autentisering och auktorisering. Azure AD B2C introducerar [principparametern](user-flow-overview.md). Med principparametern kan du använda OAuth 2.0 för att lägga till principer i din app, till exempel användarflöden för registrering, inloggning och profilhantering. I exemplet HTTP-begäranden i den här artikeln används **{tenant}.onmicrosoft.com** som exempel. Ersätt `{tenant}` med namnet på din klient om du har en och har också skapat ett användarflöde.

Det implicita inloggningsflödet ser ut ungefär som följande bild. Varje steg beskrivs i detalj senare i artikeln.

![Swimlane-stil diagram som visar OpenID Connect implicit flöde](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden

När webbprogrammet behöver autentisera användaren och köra ett användarflöde kan `/authorize` det dirigera användaren till slutpunkten. Användaren vidtar åtgärder beroende på användarflödet.

I den här begäran anger klienten de behörigheter som den `scope` behöver för att hämta från användaren i parametern och användarflödet för att köras. För att få en känsla för hur begäran fungerar, försök att klistra in begäran i en webbläsare och köra den. Ersätt `{tenant}` med namnet på din Azure AD B2C-klient. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID:t för det program som du tidigare har registrerat i din klientorganisation. Ersätt `{policy}` med namnet på en princip som du har `b2c_1_sign_in`skapat i din klientorganisation, till exempel .

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
|{klient}| Ja | Namn på din Azure AD B2C-klient|
|{princip}| Ja| Användarflödet som ska köras. Ange namnet på ett användarflöde som du har skapat i din Azure AD B2C-klient. Till `b2c_1_sign_in`exempel: `b2c_1_sign_up`, `b2c_1_edit_profile`eller . |
| client_id | Ja | Program-ID som [Azure-portalen](https://portal.azure.com/) tilldelats ditt program. |
| response_type | Ja | Måste `id_token` inkludera för OpenID Connect-inloggning. Den kan också innehålla `token`svarstypen . Om du `token`använder kan din app omedelbart få en åtkomsttoken från den auktoriserade slutpunkten, utan att göra en andra begäran till den auktoriserade slutpunkten.  Om du `token` använder svarstypen måste parametern `scope` innehålla ett scope som anger vilken resurs som token ska utfärdas för. |
| redirect_uri | Inga | Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen, förutom att den måste vara URL-kodad. |
| response_mode | Inga | Anger vilken metod som ska användas för att skicka tillbaka den resulterande token till din app.  Använd för implicita flöden `fragment`. |
| omfång | Ja | En utrymmesavskiljande lista över scope. Ett enda scopevärde anger för Azure AD båda de behörigheter som begärs. Scopet `openid` anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Omfattningen `offline_access` är valfri för webbappar. Det indikerar att din app behöver en uppdateringstoken för långlivad åtkomst till resurser. |
| state | Inga | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill använda. Vanligtvis används ett slumpmässigt genererat, unikt värde för att förhindra förfalskningsattacker mellan webbplatser. Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan de var på. |
| Nonce | Ja | Ett värde som ingår i begäran (som genereras av appen) som ingår i den resulterande ID-token som ett anspråk. Appen kan sedan verifiera det här värdet för att minska token reprisattacker. Vanligtvis är värdet en randomiserad, unik sträng som kan användas för att identifiera begärans ursprung. |
| Snabb | Inga | Den typ av användarinteraktion som krävs. För närvarande är `login`det enda giltiga värdet . Den här parametern tvingar användaren att ange sina autentiseringsuppgifter på den begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra principens arbetsflöde. Användaren kan behöva ange sitt användarnamn och lösenord, logga in med en social identitet, registrera dig för katalogen eller något annat antal steg. Användaråtgärder beror på hur användarflödet definieras.

När användaren har slutfört användarflödet returnerar Azure AD ett svar till `redirect_uri`din app till det värde som du använde för . Den använder den metod `response_mode` som anges i parametern. Svaret är exakt detsamma för var och en av användaråtgärdsscenarier, oberoende av användarflödet som utfördes.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat `response_mode=fragment` svar `response_type=id_token+token` som använder och ser ut som följande, med radbrytningar för läsbarhet:

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
| access_token | Åtkomsttoken som appen begärde. |
| token_type | Tokentypsvärdet. Den enda typen som Azure AD stöder är Bärare. |
| expires_in | Den tid som åtkomsttoken är giltig (i sekunder). |
| omfång | Scope som token är giltig för. Du kan också använda scope för att cachelagra token för senare användning. |
| id_token | ID-token som appen begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. Mer information om ID-token och deras innehåll finns i [Azure AD B2C-tokenreferensen](tokens-overview.md). |
| state | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera `state` att värdena i begäran och svaret är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigera URI så att appen kan hantera dem på rätt sätt:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som används för att klassificera typer av fel som uppstår. |
| error_description | Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |
| state | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera `state` att värdena i begäran och svaret är identiska.|

## <a name="validate-the-id-token"></a>Verifiera ID-token

Det räcker inte att ta emot en ID-token för att autentisera användaren. Verifiera ID-tokens signatur och verifiera anspråken i token enligt appens krav. Azure AD B2C använder [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera token och verifiera att de är giltiga.

Många bibliotek med öppen källkod är tillgängliga för validering av JV: er, beroende på vilket språk du föredrar att använda. Överväg att utforska tillgängliga bibliotek med öppen källkod i stället för att implementera din egen valideringslogik. Du kan använda informationen i den här artikeln för att lära dig hur du använder biblioteken på rätt sätt.

Azure AD B2C har en Endpoint för OpenID Connect-metadata. En app kan använda slutpunkten för att hämta information om Azure AD B2C vid körning. Den här informationen omfattar slutpunkter, tokeninnehåll och tokensigneringsnycklar. Det finns ett JSON-metadatadokument för varje användarflöde i din Azure AD B2C-klientorganisation. Metadatadokumentet för b2c_1_sign_in användarflödet i fabrikamb2c.onmicrosoft.com-klienten finns till exempel på:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

En av egenskaperna för det `jwks_uri`här konfigurationsdokumentet är . Värdet för samma användarflöde skulle vara:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

För att avgöra vilket användarflöde som användes för att signera en ID-token (och var metadata ska hämtas från) har du två alternativ. Först inkluderas användarnamnet i `acr` anspråket `id_token`i . Information om hur du tolkar anspråk från en ID-token finns i [Azure AD B2C-tokenreferensen](tokens-overview.md). Det andra alternativet är att koda användarflödet `state` i parameterns värde när du utfärdar begäran. Avkoda sedan `state` parametern för att avgöra vilket användarflöde som användes. Båda metoderna är giltiga.

När du har skaffat metadatadokumentet från slutpunkten för OpenID Connect-metadata kan du använda de offentliga RSA-256-nycklarna (som finns vid den här slutpunkten) för att validera signaturen för ID-token. Det kan finnas flera nycklar som anges vid den här `kid`slutpunkten vid en given tidpunkt, var och en identifieras av en . Rubriken `id_token` på innehåller också `kid` ett anspråk. Den anger vilken av dessa nycklar som användes för att signera ID-token. Mer information, inklusive information om [hur du validerar token,](tokens-overview.md)finns i [Azure AD B2C-tokenreferensen](tokens-overview.md).
<!--TODO: Improve the information on this-->

När du har validerat id-tokens signatur kräver flera anspråk verifiering. Ett exempel:

* Validera `nonce` anspråket för att förhindra attacker för tokenuppspelning. Dess värde ska vara det du angav i inloggningsbegäran.
* Verifiera `aud` anspråket för att säkerställa att ID-token har utfärdats för din app. Dess värde ska vara program-ID för din app.
* Verifiera `iat` och `exp` anspråk för att säkerställa att ID-token inte har upphört att gälla.

Flera fler valideringar som du bör utföra beskrivs i detalj i [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren eller organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet och privilegier.
* Se till att en viss styrka av autentisering har inträffat, till exempel genom att använda Azure Multi-Factor Authentication.

Mer information om anspråken i en ID-token finns i [Azure AD B2C-tokenreferensen](tokens-overview.md).

När du har validerat ID-token kan du starta en session med användaren. I din app använder du anspråken i ID-token för att få information om användaren. Den här informationen kan användas för visning, poster, auktorisering och så vidare.

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om det enda dina webbappar behöver göra är att köra användarflöden kan du hoppa över de närmaste avsnitten. Informationen i följande avsnitt gäller endast för webbappar som behöver ringa autentiserade anrop till ett webb-API och som skyddas av Azure AD B2C.

Nu när du har signerat användaren i din ensidiga app kan du få åtkomsttoken för att anropa webb-API:er som skyddas av Azure AD. Även om du redan har fått `token` en token med hjälp av svarstypen kan du använda den här metoden för att hämta token för ytterligare resurser utan att omdirigera användaren att logga in igen.

I ett vanligt webbappflöde gör du `/token` en begäran till slutpunkten. Slutpunkten stöder dock inte CORS-begäranden, så att göra AJAX-anrop för att få en uppdateringstoken är inte ett alternativ. I stället kan du använda det implicita flödet i ett dolt HTML-iframe-element för att hämta nya token för andra webb-API:er. Här är ett exempel, med radbrytningar för läsbarhet:

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
|{klient}| Krävs | Namn på din Azure AD B2C-klient|
{princip}| Krävs| Användarflödet som ska köras. Ange namnet på ett användarflöde som du har skapat i din Azure AD B2C-klient. Till `b2c_1_sign_in`exempel: `b2c_1_sign_up`, `b2c_1_edit_profile`eller . |
| client_id |Krävs |Program-ID som tilldelats din app i [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Måste `id_token` inkludera för OpenID Connect-inloggning.  Det kan också innehålla `token`svarstypen . Om du `token` använder här kan din app omedelbart få en åtkomsttoken från den auktoriserade slutpunkten, utan att göra en andra begäran till den auktoriserade slutpunkten. Om du `token` använder svarstypen måste parametern `scope` innehålla ett scope som anger vilken resurs som token ska utfärdas för. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen, förutom att den måste vara URL-kodad. |
| omfång |Krävs |En utrymmesavskiljande lista över scope.  För att hämta token, inkludera alla scope som du behöver för den avsedda resursen. |
| response_mode |Rekommenderas |Anger den metod som används för att skicka tillbaka den resulterande token till din app. Använd för implicit `fragment`flöde . Två andra lägen kan `query` anges `form_post`och , men fungerar inte i det implicita flödet. |
| state |Rekommenderas |Ett värde som ingår i begäran som returneras i tokensvaret.  Det kan vara en sträng av allt innehåll som du vill använda.  Vanligtvis används ett slumpmässigt genererat, unikt värde för att förhindra förfalskningsattacker mellan webbplatser.  Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Sidan eller visa användaren var till exempel på. |
| Nonce |Krävs |Ett värde som ingår i begäran, som genereras av appen, som ingår i den resulterande ID-token som ett anspråk.  Appen kan sedan verifiera det här värdet för att minska token reprisattacker. Vanligtvis är värdet en randomiserad, unik sträng som identifierar begärans ursprung. |
| Snabb |Krävs |Om du vill uppdatera och hämta token `prompt=none` i en dold iframe använder du för att se till att iframe inte fastnar på inloggningssidan och returnerar omedelbart. |
| login_hint |Krävs |Om du vill uppdatera och hämta token i en dold iframe inkluderar du användarens användarnamn i den här ledtråden för att skilja mellan flera sessioner som användaren kan ha vid en viss tidpunkt. Du kan extrahera användarnamnet från en tidigare `preferred_username` inloggning `profile` med hjälp av anspråket `preferred_username` (omfattningen krävs för att få anspråket). |
| domain_hint |Krävs |Det kan vara `consumers` eller `organizations`.  För att uppdatera och hämta token i `domain_hint` en dold iframe, inkludera värdet i begäran.  Extrahera `tid` anspråket från ID-token för en tidigare inloggning för `profile` att avgöra vilket värde `tid` som ska användas (omfånget krävs för att ta emot anspråket). Om `tid` anspråksvärdet `9188040d-6c67-4c5b-b112-36a304b66dad`är `domain_hint=consumers`använder du .  Annars kan `domain_hint=organizations`du använda . |

Genom att `prompt=none` ange parametern lyckas eller misslyckas begäran omedelbart och återgår till ditt program.  Ett lyckat svar skickas till din app på den angivna omdirigera URI, med hjälp av den metod som anges i parametern. `response_mode`

### <a name="successful-response"></a>Lyckat svar
Ett lyckat `response_mode=fragment` svar med hjälp av ser ut så här exemplet:

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
| token_type |Tokentypen kommer alltid att vara Bärare. |
| state |Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera `state` att värdena i begäran och svaret är identiska. |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| omfång |Scope som åtkomsttoken är giltig för. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigera URI så att appen kan hantera dem på rätt sätt.  För `prompt=none`ser ett förväntat fel ut så här:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som kan användas för att klassificera typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren logga in interaktivt igen för att hämta en ny token.

## <a name="refresh-tokens"></a>Uppdatera token
ID-token och åtkomsttoken upphör båda att gälla efter en kort tidsperiod. Din app måste vara beredd att uppdatera dessa token med jämna mellanrum.  Om du vill uppdatera någon typ av token utför du samma dolda `prompt=none` iframe-begäran som vi använde i ett tidigare exempel genom att använda parametern för att styra Azure AD-steg.  Om du `id_token` vill ta emot `response_type=id_token` ett `scope=openid`nytt `nonce` värde måste du använda och och en parameter.

## <a name="send-a-sign-out-request"></a>Skicka en ut signeringsbegäran
När du vill logga ut användaren från appen omdirigerar du användaren till Azure AD för att logga ut. Om du inte omdirigerar användaren kanske de kan ge ut om till din app utan att ange sina autentiseringsuppgifter igen eftersom de har en giltig enkel inloggningssession med Azure AD.

Du kan helt enkelt `end_session_endpoint` omdirigera användaren till det som visas i samma OpenID Connect-metadatadokument som beskrivs i [Verifiera ID-token](#validate-the-id-token). Ett exempel:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| {klient} | Ja | Namn på din Azure AD B2C-klient |
| {princip} | Ja | Det användarflöde som du vill använda för att logga ut användaren från programmet. |
| post_logout_redirect_uri | Inga | URL:en som användaren ska omdirigeras till efter lyckad utloggning. Om det inte ingår visar Azure AD B2C användaren ett allmänt meddelande. |
| state | Inga | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrollera `state` att värdena i begäran och svaret är identiska. |


> [!NOTE]
> Styra användaren till `end_session_endpoint` rensar en del av användarens enda inloggningstillstånd med Azure AD B2C. Den signerar dock inte användaren från användarens session för leverantör av social identitet. Om användaren väljer samma identitetsprovider under en efterföljande inloggning autentiseras användaren på nytt utan att ange sina autentiseringsuppgifter. Om en användare vill logga ut från ditt Azure AD B2C-program betyder det inte nödvändigtvis att de vill logga ut helt från sitt Facebook-konto, till exempel. För lokala konton avslutas dock användarens session korrekt.
>

## <a name="next-steps"></a>Nästa steg

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Kodexempel: Azure AD B2C med Microsoft Authentication Library for JavaScript

[Ensidigt program byggt med msal.js för Azure AD B2C][github-msal-js-example] (GitHub)

Det här exemplet på GitHub är avsett att hjälpa dig att komma igång med Azure AD B2C i ett enkelt webbprogram som skapats med [msal.js][github-msal-js] och med hjälp av popup-autentisering.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
