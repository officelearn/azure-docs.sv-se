---
title: Webbsigna-in med OpenID Connect - Azure Active Directory B2C
description: Skapa webbprogram med OpenID Connect-autentiseringsprotokollet i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264393"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webbinformation med OpenID Connect i Azure Active Directory B2C

OpenID Connect är ett autentiseringsprotokoll, byggt ovanpå OAuth 2.0, som kan användas för att logga in användare på ett säkert sätt i webbprogram. Genom att använda Azure Active Directory B2C-implementeringen (Azure AD B2C) av OpenID Connect kan du lägga ut registrerings-, inloggnings- och andra identitetshanteringsupplevelser i dina webbprogram till Azure Active Directory (Azure AD). Den här guiden visar hur du gör det på ett språkoberoende sätt. Den beskriver hur du skickar och ta emot HTTP-meddelanden utan att använda något av våra bibliotek med öppen källkod.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0-auktoriseringsprotokollet för användning som ett *authorization* *autentiseringsprotokoll.* Med det här autentiseringsprotokollet kan du utföra enkel inloggning. Det introducerar begreppet en *ID-token*, som gör det möjligt för klienten att verifiera användarens identitet och få grundläggande profilinformation om användaren.

Eftersom det utökar OAuth 2.0, gör det också att program kan hämta *åtkomsttoken*på ett säkert sätt . Du kan använda åtkomsttoken för att komma åt resurser som skyddas av en [auktoriseringsserver](protocols-overview.md). OpenID Connect rekommenderas om du skapar ett webbprogram som finns på en server och nås via en webbläsare. Mer information om token finns i [översikten över token i Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C utökar standard OpenID Connect-protokollet för att göra mer än enkel autentisering och auktorisering. Den introducerar [parametern användarflöde](user-flow-overview.md), som gör att du kan använda OpenID Connect för att lägga till användarupplevelser i ditt program, till exempel registrering, inloggning och profilhantering.

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden

När webbprogrammet behöver autentisera användaren och köra ett användarflöde kan `/authorize` det dirigera användaren till slutpunkten. Användaren vidtar åtgärder beroende på användarflödet.

I den här begäran anger klienten de behörigheter som den `scope` behöver hämta från användaren i parametern och anger vilket användarflöde som ska köras. För att få en känsla för hur begäran fungerar, försök att klistra in begäran i en webbläsare och köra den. Ersätt `{tenant}` med namnet på din klient. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID:t för det program som du tidigare har registrerat i din klientorganisation. Ändra också principnamnet`{policy}`( ) till det principnamn som `b2c_1_sign_in`du har i din klient, till exempel .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| {klient} | Ja | Namn på din Azure AD B2C-klient |
| {princip} | Ja | Användarflödet som ska köras. Ange namnet på ett användarflöde som du har skapat i din Azure AD B2C-klient. Till `b2c_1_sign_in`exempel: `b2c_1_sign_up`, `b2c_1_edit_profile`eller . |
| client_id | Ja | Program-ID som [Azure-portalen](https://portal.azure.com/) tilldelats ditt program. |
| Nonce | Ja | Ett värde som ingår i begäran (genereras av programmet) som ingår i den resulterande ID-token som ett anspråk. Programmet kan sedan verifiera det här värdet för att minska token repris attacker. Värdet är vanligtvis en randomiserad unik sträng som kan användas för att identifiera begärans ursprung. |
| response_type | Ja | Måste innehålla en ID-token för OpenID Connect. Om webbprogrammet också behöver token för att anropa `code+id_token`ett webb-API kan du använda . |
| omfång | Ja | En utrymmesavskiljande lista över scope. Scopet `openid` anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Scopet `offline_access` är valfritt för webbprogram. Det indikerar att ditt program behöver en *uppdateringstoken* för utökad åtkomst till resurser. |
| Snabb | Inga | Den typ av användarinteraktion som krävs. Det enda giltiga värdet `login`just nu är , vilket tvingar användaren att ange sina autentiseringsuppgifter på den begäran. |
| redirect_uri | Inga | Parametern `redirect_uri` för ditt program, där autentiseringssvar kan skickas och tas emot av ditt program. Den måste exakt matcha `redirect_uri` en av de parametrar som du registrerade i Azure-portalen, förutom att den måste vara URL-kodad. |
| response_mode | Inga | Den metod som används för att skicka tillbaka den resulterande auktoriseringskoden till ditt program. Det kan `query`vara `form_post`antingen `fragment`, , eller .  Svarsläget `form_post` rekommenderas för bästa säkerhet. |
| state | Inga | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för att förhindra förfalskningsattacker mellan webbplatser. Tillståndet används också för att koda information om användarens tillstånd i programmet innan autentiseringsbegäran inträffade, till exempel sidan de var på. |

Nu uppmanas användaren att slutföra arbetsflödet. Användaren kan behöva ange sitt användarnamn och lösenord, logga in med en social identitet eller registrera sig för katalogen. Det kan finnas ett annat antal steg beroende på hur användarflödet definieras.

När användaren har slutfört användarflödet returneras ett svar till ditt `redirect_uri` program med den angivna parametern med `response_mode` den metod som anges i parametern. Svaret är detsamma för vart och ett av de föregående fallen, oberoende av användarflödet.

Ett lyckat `response_mode=fragment` svar med skulle se ut:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| id_token | ID-token som programmet begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. |
| kod | Auktoriseringskoden som programmet `response_type=code+id_token`begärde, om du använde . Programmet kan använda auktoriseringskoden för att begära en åtkomsttoken för en målresurs. Auktoriseringskoder upphör vanligtvis att gälla efter cirka 10 minuter. |
| state | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrollera `state` att värdena i begäran och svaret är identiska. |

Felsvar kan också skickas `redirect_uri` till parametern så att programmet kan hantera dem på rätt sätt:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera de typer av fel som uppstår. |
| error_description | Ett specifikt felmeddelande som kan hjälpa till att identifiera orsaken till ett autentiseringsfel. |
| state | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrollera `state` att värdena i begäran och svaret är identiska. |

## <a name="validate-the-id-token"></a>Verifiera ID-token

Att bara ta emot en ID-token räcker inte för att autentisera användaren. Verifiera ID-tokens signatur och verifiera anspråken i token enligt programmets krav. Azure AD B2C använder [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera token och verifiera att de är giltiga. Det finns många bibliotek med öppen källkod som är tillgängliga för att validera JWTs, beroende på vilket språk du föredrar. Vi rekommenderar att du utforskar dessa alternativ i stället för att implementera din egen valideringslogik.

Azure AD B2C har en Endpoint för OpenID Connect-metadata, vilket gör att ett program kan hämta information om Azure AD B2C vid körning. Den här informationen omfattar slutpunkter, tokeninnehåll och tokensigneringsnycklar. Det finns ett JSON-metadatadokument för varje användarflöde i din B2C-klientorganisation. Metadatadokumentet för användarflödet `b2c_1_sign_in` finns `fabrikamb2c.onmicrosoft.com` till exempel på:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

En av egenskaperna för det `jwks_uri`här konfigurationsdokumentet är , vars värde för samma användarflöde skulle vara:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

För att avgöra vilket användarflöde som användes vid signering av en ID-token (och varifrån metadata ska hämtas) har du två alternativ. Först inkluderas användarnamnet i `acr` anspråket i ID-token. Det andra alternativet är att koda användarflödet `state` i parameterns värde när du utfärdar begäran och sedan avkoda det för att avgöra vilket användarflöde som användes. Båda metoderna är giltiga.

När du har skaffat metadatadokumentet från slutpunkten för OpenID Connect-metadata kan du använda de offentliga RSA 256-nycklarna för att verifiera signaturen för ID-token. Det kan finnas flera nycklar som anges vid `kid` den här slutpunkten, var och en identifieras av ett anspråk. Huvudet på ID-token innehåller `kid` också ett anspråk, vilket anger vilka av dessa nycklar som användes för att signera ID-token.

För att verifiera token från Azure AD B2C måste du generera den offentliga nyckeln med exponent(e) och modulus(n). Du måste bestämma hur du ska göra detta i ditt respektive programmeringsspråk därefter. Den officiella dokumentationen om public key-generering med RSA-protokollet hittar du här:https://tools.ietf.org/html/rfc3447#section-3.1

När du har validerat signaturen för ID-token finns det flera anspråk som du måste verifiera. Till exempel:

- Validera `nonce` anspråket för att förhindra attacker för tokenuppspelning. Dess värde ska vara det du angav i inloggningsbegäran.
- Verifiera `aud` anspråket för att säkerställa att ID-token har utfärdats för ditt program. Dess värde bör vara programmets ID för ditt program.
- Verifiera `iat` och `exp` anspråk för att se till att ID-token inte har upphört att gälla.

Det finns också flera fler valideringar som du bör utföra. Valideringarna beskrivs i detalj i [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Några vanliga valideringar är:

- Se till att användaren/organisationen har registrerat sig för programmet.
- Se till att användaren har rätt behörighet/behörighet.
- Se till att en viss styrka av autentisering har inträffat, till exempel Azure Multi-Factor Authentication.

När du har validerat ID-token kan du påbörja en session med användaren. Du kan använda anspråken i ID-token för att få information om användaren i ditt program. Användningsområden för den här informationen inkluderar visning, poster och auktorisering.

## <a name="get-a-token"></a>Skaffa en token

Om du behöver webbprogrammet för att bara köra användarflöden kan du hoppa över de närmaste avsnitten. Dessa avsnitt är endast tillämpliga på webbprogram som behöver ringa autentiserade anrop till ett webb-API och skyddas även av Azure AD B2C.

Du kan lösa in auktoriseringskoden som du har fått (med hjälp `response_type=code+id_token`av) för en token till önskad resurs genom att skicka en `POST` begäran till `/token` slutpunkten. I Azure AD B2C kan du [begära åtkomsttoken för andra API:er](access-tokens.md#request-a-token) som vanligt genom att ange deras scope i begäran.

Du kan också begära en åtkomsttoken för appens eget webb-API via en konvention om att använda appens klient-ID som begärt scope (vilket resulterar i en åtkomsttoken med klient-ID som "målgrupp"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| {klient} | Ja | Namn på din Azure AD B2C-klient |
| {princip} | Ja | Användarflödet som användes för att hämta auktoriseringskoden. Du kan inte använda ett annat användarflöde i den här begäran. Lägg till den här parametern i frågesträngen, inte i post-brödtexten. |
| client_id | Ja | Program-ID som [Azure-portalen](https://portal.azure.com/) tilldelats ditt program. |
| client_secret | Ja, i webbappar | Programhemligheten som genererades i [Azure-portalen](https://portal.azure.com/). Klienthemligheter används i det här flödet för Web App-scenarier, där klienten säkert kan lagra en klienthemlighet. För scenarier för native app (offentlig klient) kan klienthemligheter inte lagras på ett säkert sätt, threfore används inte i det här flödet. Om du använder en klienthemlighet, ändra den regelbundet. |
| kod | Ja | Auktoriseringskoden som du fick i början av användarflödet. |
| grant_type | Ja | Den typ av bidrag `authorization_code` som måste vara för auktoriseringskoden flödet. |
| redirect_uri | Ja | Parametern `redirect_uri` för programmet där du tog emot auktoriseringskoden. |
| omfång | Inga | En utrymmesavskiljande lista över scope. Scopet `openid` anger en behörighet att logga in användaren och hämta data om användaren i form av id_token parametrar. Den kan användas för att hämta token till programmets egna backend-webb-API, som representeras av samma program-ID som klienten. Scopet `offline_access` anger att ditt program behöver en uppdateringstoken för utökad åtkomst till resurser. |

Ett lyckat tokensvar ser ut:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| not_before | Den tidpunkt då token anses giltig, i epoktid. |
| token_type | Tokentypsvärdet. `Bearer`är den enda typ som stöds. |
| access_token | Den signerade JWT-token som du begärde. |
| omfång | De scope som token är giltig för. |
| expires_in | Den tid som åtkomsttoken är giltig (i sekunder). |
| refresh_token | En OAuth 2.0-uppdateringstoken. Programmet kan använda den här token för att hämta ytterligare token efter att den aktuella token har upphört att gälla. Uppdatera token kan användas för att behålla åtkomsten till resurser under längre tidsperioder. Scopet `offline_access` måste ha använts i både auktoriserings- och tokenbegäranden för att kunna ta emot en uppdateringstoken. |

Felsvar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som uppstår. |
| error_description | Ett meddelande som kan hjälpa till att identifiera orsaken till ett autentiseringsfel. |

## <a name="use-the-token"></a>Använd token

Nu när du har skaffat en åtkomsttoken kan du använda token i begäranden till `Authorization` dina backend-webb-API:er genom att inkludera den i sidhuvudet:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Uppdatera token

ID-token upphör att gälla på kort tid. Uppdatera token efter att de upphör att gälla för att fortsätta att kunna komma åt resurser. Du kan uppdatera en token `POST` genom `/token` att skicka en annan begäran till slutpunkten. Den här gången anger du parametern `refresh_token` i stället för parametern: `code`

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| {klient} | Ja | Namn på din Azure AD B2C-klient |
| {princip} | Ja | Användarflödet som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda ett annat användarflöde i den här begäran. Lägg till den här parametern i frågesträngen, inte i post-brödtexten. |
| client_id | Ja | Program-ID som [Azure-portalen](https://portal.azure.com/) tilldelats ditt program. |
| client_secret | Ja, i webbappar | Programhemligheten som genererades i [Azure-portalen](https://portal.azure.com/). Klienthemligheter används i det här flödet för Web App-scenarier, där klienten säkert kan lagra en klienthemlighet. För scenarier för native app (offentlig klient) kan klienthemligheter inte lagras på ett säkert sätt, threfore används inte i det här samtalet. Om du använder en klienthemlighet, ändra den regelbundet. |
| grant_type | Ja | Typen av bidrag, som måste vara en uppdateringstoken för den här delen av auktoriseringskodflödet. |
| refresh_token | Ja | Den ursprungliga uppdateringstoken som förvärvades i den andra delen av flödet. Scopet `offline_access` måste användas i både auktoriserings- och tokenbegäranden för att kunna ta emot en uppdateringstoken. |
| redirect_uri | Inga | Parametern `redirect_uri` för programmet där du tog emot auktoriseringskoden. |
| omfång | Inga | En utrymmesavskiljande lista över scope. Scopet `openid` anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Den kan användas för att skicka token till programmets egna backend-webb-API, som representeras av samma program-ID som klienten. Scopet `offline_access` anger att ditt program behöver en uppdateringstoken för utökad åtkomst till resurser. |

Ett lyckat tokensvar ser ut:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| not_before | Den tidpunkt då token anses giltig, i epoktid. |
| token_type | Tokentypsvärdet. `Bearer`är den enda typ som stöds. |
| access_token | Den signerade JWT-token som begärdes. |
| omfång | Det scope som token är giltig för. |
| expires_in | Den tid som åtkomsttoken är giltig (i sekunder). |
| refresh_token | En OAuth 2.0-uppdateringstoken. Programmet kan använda den här token för att hämta ytterligare token efter att den aktuella token har upphört att gälla. Uppdatera token kan användas för att behålla åtkomsten till resurser under längre tidsperioder. |

Felsvar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som uppstår. |
| error_description | Ett meddelande som kan hjälpa till att identifiera orsaken till ett autentiseringsfel. |

## <a name="send-a-sign-out-request"></a>Skicka en ut signeringsbegäran

När du vill logga ut användaren från programmet räcker det inte att rensa programmets cookies eller på annat sätt avsluta sessionen med användaren. Omdirigera användaren till Azure AD B2C för att logga ut. Om du inte gör det kan användaren ha autentisering om till ditt program utan att ange sina autentiseringsuppgifter igen.

Om du vill logga ut användaren `end_session` omdirigerar du användaren till slutpunkten som visas i det OpenID Connect-metadatadokument som beskrivits tidigare:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| {klient} | Ja | Namn på din Azure AD B2C-klient |
| {princip} | Ja | Det användarflöde som du vill använda för att logga ut användaren från programmet. |
| id_token_hint| Inga | En tidigare utfärdad ID-token som ska överföras till utloggningsslutpunkten som en ledtråd om slutanvändarens aktuella autentiserade session med klienten. Den `id_token_hint` säkerställer `post_logout_redirect_uri` att det är en registrerad svars-URL i dina Azure AD B2C-programinställningar. |
| client_id | Nej* | Program-ID som [Azure-portalen](https://portal.azure.com/) tilldelats ditt program.<br><br>\**Detta krävs när `Application` isolering SSO-konfiguration och _Kräv ID-token_ i utloggningsbegäran är inställd på `No`.* |
| post_logout_redirect_uri | Inga | URL:en som användaren ska omdirigeras till efter lyckad utloggning. Om det inte ingår visar Azure AD B2C användaren ett allmänt meddelande. Om du inte `id_token_hint`anger en bör du inte registrera den här URL:en som svars-URL i dina Azure AD B2C-programinställningar. |
| state | Inga | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrollera `state` att värdena i begäran och svaret är identiska. |

### <a name="secure-your-logout-redirect"></a>Säkra din omdirigering av utloggning

Efter utloggning omdirigeras användaren till den URI `post_logout_redirect_uri` som anges i parametern, oavsett vilka svarsadresser som har angetts för programmet. Men om ett `id_token_hint` giltigt skickas, Azure AD B2C verifierar att värdet för `post_logout_redirect_uri` matchar en av programmets konfigurerade omdirigera URI:er innan omdirigera. Om ingen matchande svars-URL har konfigurerats för programmet visas ett felmeddelande och användaren omdirigeras inte.

### <a name="external-identity-provider-sign-out"></a>Ut logga ut för extern identitetsprovider

Om du dirigerar `end_session` användaren till slutpunkten rensas en del av användarens enskilda inloggningstillstånd med Azure AD B2C, men användaren signerar inte användaren från deras IDP-session (Social Identity Provider). Om användaren väljer samma IDP under en efterföljande inloggning, omuthyds de utan att ange sina autentiseringsuppgifter. Om en användare vill logga ut från programmet betyder det inte nödvändigtvis att de vill logga ut från sitt Facebook-konto. Men om lokala konton används avslutas användarens session korrekt.
