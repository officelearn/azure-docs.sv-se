---
title: 'Azure Active Directory B2C: Single-page appar med implicita flödet | Microsoft Docs'
description: Lär dig hur du skapar en sida appar direkt genom att använda implicita flödet för OAuth 2.0 med Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: ac0351ce220da5194d3a447e51185409b7368f21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: Single-page app logga in med hjälp av implicita flödet för OAuth 2.0

> [!NOTE]
> Den här funktionen är i förhandsgranskningen.
> 

Många moderna appar innehåller en enda sida app-klientdel som främst är skriven i JavaScript. Ofta skrivs appen med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal. En sida appar och andra JavaScript-appar som körs i första hand i en webbläsare har några ytterligare utmaningar för autentisering:

* Säkerhetsegenskaper för dessa appar är skiljer sig från traditionella serverbaserade webbprogram.
* Många servrar för auktorisering och identitetsleverantörer stöder inte resursdelning för korsande ursprung (CORS)-begäranden.
* Hela sidan webbläsaren omdirigeras från appen kan vara betydligt inkräktande av användarupplevelsen.

Att dessa program för Azure Active Directory B2C (Azure AD B2C) använder implicita flödet för OAuth 2.0. Flödet för OAuth 2.0 auktorisering implicit bevilja beskrivs i [avsnittet 4.2 av OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). I implicita flödet appen tar emot token direkt från Azure Active Directory (AD Azure) godkänna endpoint utan någon exchange servrar. Alla autentiseringslogiken och session hantering tar placera helt i JavaScript-klienten utan ytterligare sidomdirigeringar.

Azure AD B2C utökar OAuth 2.0 standard implicita flödet till mer än enkel autentisering och auktorisering. Azure AD B2C introducerar den [parametern](active-directory-b2c-reference-policies.md). Med parametern princip du kan använda OAuth 2.0 att lägga till användarupplevelser i appen, som registrering, inloggning och profilhantering. I den här artikeln hur vi du använda implicita flödet och Azure AD för att implementera var och en av dessa upplevelser i dina program på en sida. För att hjälpa dig att komma igång, ta en titt på vårt [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) och [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) prover.

I exempel HTTP-begäranden i den här artikeln använder vi våra exempel Azure AD B2C-katalog, **fabrikamb2c.onmicrosoft.com**. Vi använder också våra egna exempelprogrammet och principer. Du kan också försöka begäranden med hjälp av dessa värden eller ersätta dem med egna värden.
Lär dig hur du [hämta egna Azure AD B2C-katalog, program och principer](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Protokollet diagram

Implicita flödet inloggning ser ut ungefär som följande bild. Varje steg beskrivs i detalj senare i artikeln.

![OpenID Connect spaltformat som illustrerar](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden
När ditt webbprogram måste autentisera användaren och köra en princip, det leder användaren till den `/authorize` slutpunkt. Det här är den interaktiva delen av flödet, där användaren vidtar åtgärder beroende på principen. Användaren får ett ID-token från Azure AD-slutpunkten.

I den här förfrågan klienten anger i den `scope` parametern de behörigheter som krävs för att hämta från användaren. I den `p` parametern anger principen som ska köras. Följande tre exempel (med radbrytningar för att läsa) varje använda en annan princip. Att få en bild av hur fungerar varje begäran, försök att klistra in begäran i en webbläsare och kör den.

### <a name="use-a-sign-in-policy"></a>Använda en princip för inloggning
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Använda en princip för registrering
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Använda en princip för Redigera-profil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Det program-ID som har tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning. Det kan också innehålla svarstypen `token`. Om du använder `token`, din app kan omedelbart ta emot en åtkomst-token från slutpunkten för auktorisering, utan att göra en andra begäran till slutpunkten för auktorisering.  Om du använder den `token` svarstyp den `scope` parameter måste innehålla en omfattning som anger vilken resurs för att utfärda token för. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen, förutom att det måste vara URL-kodade. |
| response_mode |Rekommenderas |Anger metoden du använder för att skicka den resulterande token tillbaka till din app.  Implicit flöden, Använd `fragment`. |
| Omfång |Krävs |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD båda de behörigheter som krävs. Den `openid` omfång anger behörighet att logga in användaren och hämta data om användaren i form av ID-token. (Du lär dig om detta mer senare i artikeln.) Den `offline_access` scope är valfritt för webbprogram. Det innebär att din app måste en uppdateringstoken för långlivade åtkomst till resurser. |
| state |Rekommenderas |Ett värde som ingår i denna begäran som också returneras i token svaret. Det kan vara en sträng med innehåll som du vill använda. Vanligtvis används ett slumpmässigt genererat, unikt värde för att förhindra attacker med förfalskning av begäran. Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade som de var på sidan. |
| temporärt ID |Krävs |Ett värde som ingår i denna begäran (genereras av appen) som ingår i den resulterande ID-token som ett anspråk. Appen kan sedan kontrollera värdet för att minimera token replay-attacker. Värdet är vanligtvis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. |
| p |Krävs |Principen för att köra. Det är namnet på en princip som skapas i din Azure AD B2C-klient. Princip för namn-värde ska inledas med **b2c\_1\_**. Mer information finns i [Azure AD B2C inbyggda principer](active-directory-b2c-reference-policies.md). |
| kommandotolk |Valfri |Typ av användarinteraktion som krävs. Det enda giltiga värdet är för närvarande `login`. Detta gör att användarna anger sina autentiseringsuppgifter på begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra arbetsflödet för den principen. Detta kan handla om användaren att ange sina användarnamn och lösenord, logga in med en sociala identitet registrerar sig för katalogen, eller en annan siffra steg. Användaråtgärder beror på hur principen har definierats.

När användaren uppfyller principen, Azure AD tillbaka ett svar på din app på värdet som du använde för `redirect_uri`. Den använder metoden som anges i den `response_mode` parameter. Svaret är exakt samma för alla användare åtgärd scenarier, oberoende av den princip som har utförts.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar som använder `response_mode=fragment` och `response_type=id_token+token` ser ut som följande, med radbrytningar för läsbarhet:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Åtkomsttoken som appen har begärt.  Åtkomst-token ska inte avkodas eller annars kontrolleras. Den kan hanteras som en täckande sträng. |
| token_type |Tokentypen-värde. Den enda typen som har stöd för Azure AD är ägar. |
| expires_in |Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| Omfång |Scope som gäller för token. Du kan också använda omfång cache-tokens för senare användning. |
| id_token |ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll finns i [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigerings-URI så att appen kan hantera dem på rätt sätt:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En kod felsträng används för att klassificera typer av fel som inträffar. Du kan också använda felkoden för felhantering. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i föregående tabell. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska.|

## <a name="validate-the-id-token"></a>Verifiera ID-token
Ta emot en token med ID: T är inte tillräckligt för att autentisera användaren. Du måste verifiera signaturen för ID-token och kontrollera anspråk i token per krav som din app. Azure AD B2C använder [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Många bibliotek med öppen källkod är tillgängliga för att validera JWTs, beroende på det språk som du föredrar att använda. Överväg att utforska tillgängliga bibliotek med öppen källkod i stället för att implementera din egen valideringslogik. Du kan använda informationen i den här artikeln för att du lär dig hur du ska använda dessa bibliotek.

Azure AD B2C har en slutpunkt för OpenID Connect metadata. En app kan använda slutpunkten för att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Det finns en JSON-dokumentet för metadata för varje princip i din Azure AD B2C-klient. Till exempel finns Metadatadokumentet för b2c_1_sign_in principen i fabrikamb2c.onmicrosoft.com klienten på:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En av egenskaperna för den här konfigurationsdokument är den `jwks_uri`. Värdet för samma princip skulle vara:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Att avgöra vilken princip som användes för att logga en ID-token (och var du ska hämta metadata från), har du två alternativ. Först principnamnet ingår i den `acr` anspråk i `id_token`. Information om hur du Parsar anspråk från en ID-token, finns det [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). Ett annat alternativ är att koda principen i värdet för den `state` parameter när du skickar en begäran. Avkoda sedan den `state` parametern för att avgöra vilken princip som har använts. Antingen metoden är giltig.

När du har köpt Metadatadokumentet från slutpunkten OpenID Connect metadata kan använda du de offentliga nycklarna för RSA-256 (som finns i den här slutpunkten) för att verifiera signaturen för ID-token. Det kan finnas flera nycklar som anges i den här slutpunkten vid en given tidpunkt, alla identifierade med en `kid`. Rubriken för `id_token` innehåller också en `kid` anspråk. Anger vilken av dessa nycklar användes för att signera ID-token. Mer information, inklusive lära dig mer om [verifiera token](active-directory-b2c-reference-tokens.md#token-validation), finns det [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

När du verifiera signaturen för ID-token, Kräv verifiering av flera anspråk. Exempel:

* Verifiera den `nonce` anspråk att förhindra att token replay-attacker. Värdet ska du angav i begäran inloggning.
* Verifiera den `aud` anspråk så att det ID-token har utfärdats för din app. Värdet ska vara program-ID för din app.
* Verifiera den `iat` och `exp` utger sig för att säkerställa att ID-token inte har gått ut.

Flera mer verifieringar som du bör utföra beskrivs i detalj i den [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Några vanliga verifieringar inkluderar:

* Se till att användaren eller organisationen har registrerat dig för appen.
* Se till att användaren har rätt behörighet och privilegier.
* Se till att en viss styrkan hos autentisering har uppstått, så som med Azure Multi-Factor Authentication.

Mer information om anspråk i en token med ID: T finns på [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md).

När du har fullständigt validerat ID-token, kan du börja en session med användaren. I din app att använda sig av anspråk i ID-token för att hämta information om användaren. Den här informationen kan användas för att visa, poster, auktorisering och så vidare.

## <a name="get-access-tokens"></a>Få åtkomst-token
Om det enda dina webbappar behöver köra principer, kan du hoppa över de följande avsnitten. Informationen i följande avsnitt gäller endast för webbprogram som behöver göra autentiserade anrop till ett webb-API och som skyddas av Azure AD B2C.

Nu när du är inloggad användaren till en sida-app, kan du få åtkomst-token för anropande webb-API: er som skyddas av Azure AD. Även om du redan har fått en token med hjälp av den `token` svarstypen, du kan använda den här metoden för att hämta token för ytterligare resurser utan att omdirigera användaren att logga in igen.

I en typisk web app-flöde, ska du göra detta genom att göra en begäran om att den `/token` slutpunkt.  Slutpunkten stöder dock inte CORS begäranden AJAX-anrop för att hämta och uppdatera token är inte ett alternativ. Du kan i stället använda implicita flödet i ett dolt HTML iframe-element för att hämta ny token för andra webb-API: er. Här är ett exempel med radbrytningar för läsbarhet:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Det program-ID som har tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning.  Det kan även innehålla svarstypen `token`. Om du använder `token` här appen kan omedelbart ta emot en åtkomst-token från slutpunkten för auktorisering, utan att göra en andra begäran till slutpunkten för auktorisering. Om du använder den `token` svarstyp den `scope` parameter måste innehålla en omfattning som anger vilken resurs för att utfärda token för. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen, förutom att det måste vara URL-kodade. |
| Omfång |Krävs |En blankstegsavgränsad lista över scope.  För att hämta token med alla omfattningar som du behöver för den avsedda resursen. |
| response_mode |Rekommenderas |Anger den metod som används för att skicka den resulterande token tillbaka till din app.  Kan vara `query`, `form_post`, eller `fragment`. |
| state |Rekommenderas |Ett värde som ingår i denna begäran som returneras i token svaret.  Det kan vara en sträng med innehåll som du vill använda.  Vanligtvis används ett slumpmässigt genererat, unikt värde för att förhindra attacker med förfalskning av begäran.  Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Sidan eller vyn kunde till exempel användaren på. |
| temporärt ID |Krävs |Ett värde som ingår i den begäran som skapats av appen, som ingår i den resulterande ID-token som ett anspråk.  Appen kan sedan kontrollera värdet för att minimera token replay-attacker. Värdet är vanligtvis en slumpmässig, unik sträng som identifierar begäran ursprung. |
| kommandotolk |Krävs |Om du vill uppdatera och hämta token i en dold iframe måste använda `prompt=none` så att iframe inte fastna på sidan logga in och returnerar omedelbart. |
| login_hint |Krävs |Om du vill uppdatera och hämta token i en dold iframe innehålla användarnamnet för användaren i det här tipset att skilja mellan flera sessioner som användaren kan ha vid en given tidpunkt. Användarnamnet kan extraheras från ett tidigare logga in med hjälp av den `preferred_username` anspråk. |
| domain_hint |Krävs |Det kan vara `consumers` eller `organizations`.  För att uppdatera och hämta token i en dold iframe, måste du inkludera den `domain_hint` värde i förfrågan.  Extrahera de `tid` anspråk från token ID för en tidigare inloggning att avgöra vilket värde som ska användas.  Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, Använd `domain_hint=consumers`.  Annars använder `domain_hint=organizations`. |

Genom att ange den `prompt=none` parameter för denna begäran antingen lyckas eller misslyckas omedelbart och återgår till programmet.  Ett lyckat svar skickas till din app på angivna omdirigerings-URI, med hjälp av metoden som anges i den `response_mode` parameter.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med hjälp av `response_mode=fragment` ser ut så här:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den token som begärdes av appen. |
| token_type |Tokentypen kommer alltid att innehavaren. |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| Omfång |Scope som gäller för åtkomst-token. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigerings-URI så att appen kan hantera dem på lämpligt sätt.  För `prompt=none`, ett förväntade fel ser ut så här:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar. Du kan också använda strängen att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

Om du får detta felmeddelande i iframe begäran måste användaren interaktivt logga in igen att hämta en ny token. Du kan hantera det på ett sätt som passar för ditt program.

## <a name="refresh-tokens"></a>Uppdatera token
ID-token och åtkomst-token upphör att gälla efter en kort tidsperiod. Appen måste förberedas för att uppdatera dessa token med jämna mellanrum.  Om du vill uppdatera båda typerna av token utför du samma dolda iframe-begäran som vi använde i ett tidigare exempel med hjälp av den `prompt=none` parametern för att styra Azure AD-stegen.  Ta emot en ny `id_token` värde, måste du använda `response_type=id_token` och `scope=openid`, och en `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning
När du vill logga ut från appen användaren omdirigeras användaren till Azure AD för att logga ut. Om du inte gör det, kanske användaren kan autentiseras i appen utan att ange sina autentiseringsuppgifter igen. Det beror på att de har en giltig inloggning session med Azure AD.

Du kan bara omdirigera användare till den `end_session_endpoint` som finns med i den samma OpenID att ansluta Metadatadokumentet beskrivs i [Validera token med ID](#validate-the-id-token). Exempel:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Krävs |Principen som ska använda för att signera användare utanför tillämpningsprogrammet. |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om det inte är inkluderad visar ett allmänt meddelande för användaren i Azure AD B2C. |

> [!NOTE]
> Dirigera användare till den `end_session_endpoint` tar bort vissa av användarens inloggning tillstånd med Azure AD B2C. Dock logga inte den användare utanför sociala identitet providern användarsessionen. Om användaren väljer samma identifiera providern under en efterföljande inloggning, användaren autentiseras utan att behöva ange sina autentiseringsuppgifter. Om en användare vill logga ut från ditt program för Azure AD B2C betyder den inte de vill helt logga ut från sina Facebook-konto, t.ex. Men för lokala konton avslutas användarens session korrekt.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Använda din egen Azure AD B2C-klient
Slutför följande tre steg om du vill testa dessa begäranden själv. Ersätt exempelvärden som vi använder i den här artikeln med egna värden:

1. [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). Använd namn för din klient i begäranden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) att hämta ett program-ID och ett `redirect_uri` värde. Inkludera en webbapp eller webb-API i din app. Du kan också kan du skapa en programhemlighet.
3. [Skapa dina principer](active-directory-b2c-reference-policies.md) att hämta principens namn.

## <a name="samples"></a>Exempel

* [Skapa en enkel sida-app med Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Skapa en enkel sida-app med hjälp av .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

