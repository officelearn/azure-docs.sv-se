---
title: Webb-logga in med OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Skapa webbprogram med hjälp av autentiseringsprotokollet OpenID Connect i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4137360fadab0206c6569b58d6a9a0519ce74450
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703936"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webbinloggning med OpenID Connect i Azure Active Directory B2C

OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0, som kan användas på ett säkert sätt logga användare in webbprogram. Genom att använda den Azure Active Directory B2C (Azure AD B2C) implementeringen av OpenID Connect, du kan lägga ut registrering, inloggning och andra Identitetshantering upplevelser i dina webbprogram till Azure Active Directory (AD Azure). Den här guiden visar hur du gör på ett språkoberoende sätt. Den beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av våra bibliotek med öppen källkod.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0 *auktorisering* protokoll för användning som en *autentisering* protokoll. Det här autentiseringsprotokollet kan du utföra enkel inloggning. Det introducerar konceptet för en *ID-token*, vilket gör att klienten att verifiera användarens identitet och få grundläggande profilinformation om användaren.

Eftersom den utökar OAuth 2.0 kan också program på ett säkert sätt hämta *åtkomsttoken*. Du kan använda åtkomsttoken för att få åtkomst till resurser som skyddas av en [auktoriseringsservern](active-directory-b2c-reference-protocols.md). OpenID Connect rekommenderas om du skapar ett webbprogram som har finns på en server och öppnas via en webbläsare. Om du vill lägga till Identitetshantering till din mobilapp eller i program med hjälp av Azure AD B2C bör du använda [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i stället för OpenID Connect. Mer information om token finns i den [översikt över token i Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C utökar standard OpenID Connect-protokollet för att göra mer än enkel autentisering och auktorisering. Det introducerar den [user flow-parameter](active-directory-b2c-reference-policies.md), vilket gör att du kan använda OpenID Connect för att lägga till användare inträffar i ditt program, till exempel registrering, inloggning och profilhantering.

## <a name="send-authentication-requests"></a>Skicka begäranden om autentisering

När ditt webbprogram måste autentisera användaren och kör ett användarflöde, det kan dirigera användare till den `/authorize` slutpunkt. Användaren vidtar åtgärder beroende på användarflödet.

I den här begäran klienten anger de behörigheter som krävs för att hämta för användaren i den `scope` parametern och användarflödet ska köras den `p` parametern. Tre exempel finns i avsnitten nedan (med radbrytningar för läsbarhet), med en annan användare-flöde. Få en bild för hur varje begäran fungerar, försök att klistra in begäran i en webbläsare och kör den. Du kan ersätta `fabrikamb2c` med namnet på din klient om du har en och har skapat ett användarflöde.

#### <a name="use-a-sign-in-user-flow"></a>Använd en inloggning användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Använd en registrerings användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Använda en Redigera profil användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats till ditt program. |
| response_type | Ja | Måste innehålla en ID-token för OpenID Connect. Om ditt webbprogram måste också token för att anropa ett webb-API, kan du använda `code+id_token`. |
| redirect_uri | Nej | Den `redirect_uri` -parametern för ditt program, där autentiseringssvar kan skickas och tas emot av ditt program. Det måste exakt matcha en av de `redirect_uri` parametrar som du registrerade i Azure-portalen, förutom att det måste vara URL-kodas. |
| omfång | Ja | En blankstegsavgränsad lista med omfattningar. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Den `offline_access` omfånget är valfritt för webbprogram. Anger det att ditt program behöver en *uppdateringstoken* för utökad åtkomst till resurser. |
| response_mode | Nej | Den metod som används för att skicka resulterande Auktoriseringskoden tillbaka till programmet. Det kan vara antingen `query`, `form_post`, eller `fragment`.  Den `form_post` Svarsläge rekommenderas för bästa säkerhet. |
| state | Nej | Ett värde i begäran som returneras också i token-svaret. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för att förhindra attacker med förfalskning av begäran. Tillstånd används också för att koda information om användarens status i programmet innan autentiseringsbegäran inträffade, till exempel gruppsidan de befann sig i. |
| nonce | Ja | Ett värde i begäran (genereras av programmet) som ingår i den resulterande ID-token som ett anspråk. Programmet kan kontrollera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig unik sträng som kan användas för att fastställa ursprunget för begäran. |
| p | Ja | Användarflödet som körs. Det är namnet på ett användarflöde som skapas i din Azure AD B2C-klient. Namnet på användarflödet ska börja med `b2c\_1\_`. |
| fråga | Nej | Typ av interaktion från användaren som krävs. Det enda giltiga värdet just nu är `login`, vilket Tvingar användaren att ange sina autentiseringsuppgifter i begäran. |

Nu kan uppmanas användaren att slutföra arbetsflödet. Användaren kan behöva ange sina användarnamn och lösenord, logga in med en sociala eller registrera för katalogen. Det kan finnas andra många steg beroende på hur användarflödet har definierats.

När du är klar användarflödet ett svar returneras till ditt program med den angivna `redirect_uri` parameter med hjälp av metoden som anges i den `response_mode` parametern. Svaret är detsamma för var och en av de föregående fall, oberoende av användarflödet.

Ett lyckat svar med `response_mode=fragment` skulle se ut:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| id_token | ID-token som programmet har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. |
| Kod | Auktoriseringskod som programmet har begärt, om du har använt `response_type=code+id_token`. Programmet kan använda Auktoriseringskoden för att begära en åtkomsttoken för en målresurs. Auktoriseringskoder vanligtvis upphör att gälla efter cirka 10 minuter. |
| state | Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Programmet bör kontrollera att den `state` värden i begäran och svar är identiska. |

Felsvar kan också skickas till den `redirect_uri` parametern så att programmet kan hantera dem på rätt sätt:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typerna av fel som uppstår. |
| error_description | Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |
| state | Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Programmet bör kontrollera att den `state` värden i begäran och svar är identiska. |

## <a name="validate-the-id-token"></a>Verifiera ID-token

Bara få ett ID-token är inte tillräckligt för att autentisera användaren. Verifiera signaturen för ID-token och verifiera anspråken i token enligt ditt programs krav. Azure AD B2C använder [JSON Web token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga. Det finns många bibliotek för öppen källkod som är tillgängliga för att verifiera JWTs, beroende på ditt språk för inställningar. Vi rekommenderar att utforska dessa alternativ i stället för att implementera dina egna validering av logik. 

Azure AD B2C har OpenID Connect metadataslutpunkt, vilket gör att ett program för att få information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Det finns ett JSON-dokument för metadata för varje användarflöde i din B2C-klient. Till exempel metadatadokument för den `b2c_1_sign_in` användarflödet i `fabrikamb2c.onmicrosoft.com` finns på:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En av egenskaperna för den här konfigurationsdokumentet är `jwks_uri`, vars värdet för samma användarflödet skulle vara:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

För att avgöra vilka användarflödet har används vid signering av ett ID-token (och var du kan hämta metadata), har du två alternativ. Först userjourney-namnet som ingår i den `acr` anspråk i ID-token. Ett annat alternativ är att koda användarflödet i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilka användarflödet har använts. Någon av metoderna är giltig.

När du har köpt metadatadokument från metadataslutpunkt OpenID Connect kan använda du de offentliga nycklarna för RSA-256 för att verifiera signaturen för ID-token. Det kan finnas flera tangenterna som anges i den här slutpunkten, alla identifierade med en `kid` anspråk. Rubriken för ID-token innehåller också en `kid` anspråk, vilket anger vilka av dessa nycklar används för att logga ID-token.

När du har verifierat signaturen för ID-token, finns det flera anspråk som du behöver verifiera. Exempel:

- Verifiera den `nonce` anspråk som ska förhindra token repetitionsattacker. Värdet ska vara du angav i inloggning-begäran.
- Verifiera den `aud` anspråk som ska se till att ID-token har utfärdats för ditt program. Dess värde bör vara program-ID för ditt program.
- Verifiera den `iat` och `exp` utger sig för att se till att ID-token inte har gått ut.

Det finns också flera mer verifieringar som du bör utföra. Validering beskrivs i detalj i de [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Vissa vanliga verifieringar är:

- Se till att användaren/organisationen har registrerat dig för programmet.
- Se till att användaren har rätt auktorisering/privilegier.
- Se till att en viss styrkan hos autentisering har inträffat, till exempel Azure Multi-Factor Authentication.

När du har validerat ID-token kan du börja en session med användaren. Du kan använda anspråken i ID-token för att hämta information om användaren i ditt program. Användningsområden för den här informationen omfattar visning, poster och auktorisering.

## <a name="get-a-token"></a>Hämta en token

Om du behöver ditt webbprogram för att endast köra användarflöden kan du hoppa över nästa avsnitt. Dessa avsnitt gäller endast för program som behöver göra autentiserade anrop till ett webb-API och också skyddas av Azure AD B2C.

Kan du lösa in den auktoriseringskod som du har köpt (med hjälp av `response_type=code+id_token`) för en token till önskad resurs genom att skicka en `POST` begäran till den `/token` slutpunkt. För närvarande är den enda resurs som du kan begära en token för ditt programs egen backend-webb-API. Konventionen för att begära en token till dig själv är att använda programmets klient-ID som omfång:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| p | Ja | Det användarflöde som användes för att hämta Auktoriseringskoden. Du kan inte använda en annan användarflödet i den här begäran. Lägg till den här parametern i frågesträngen, inte till i brödtexten för INLÄGGET. |
| client_id | Ja | Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats till ditt program. |
| _typ av beviljande | Ja | Typ av anslag som måste vara `authorization_code` för auktoriseringskodsflödet. |
| omfång | Nej | En blankstegsavgränsad lista med omfattningar. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av id_token parametrar. Det kan användas för att hämta token för ditt programs egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång innebär att ditt program måste en uppdateringstoken för utökad åtkomst till resurser. |
| Kod | Ja | Auktoriseringskod som du hämtade i början av användarflödet. |
| redirect_uri | Ja | Den `redirect_uri` -parametern för programmet som du fick Auktoriseringskoden. |
| client_secret | Ja | Programhemlighet som har genererats i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift-artefakt. Du bör lagra den på ett säkert sätt på din server. Ändra den här klienthemlighet på regelbunden basis. |

Det ser ut som ett lyckat svar för token:

```
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
| not_before | Den tid då token betraktas som giltigt i epoktid. |
| token_type | Typ tokenu-värde. `Bearer` är den enda typ som stöds. |
| access_token | Den signera JWT-token som du har begärt. |
| omfång | Scope som token är giltig. |
| expires_in | Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token | OAuth 2.0-uppdateringstoken. Programmet kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token kan användas för att behålla åtkomst till resurser i längre tid. Omfånget `offline_access` måste ha använts i både auktorisering och token-förfrågningar för att få en uppdateringstoken. |

Felsvar se ut:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som uppstår. |
| error_description | Ett meddelande som kan identifiera de grundläggande orsakerna till ett autentiseringsfel. |

## <a name="use-the-token"></a>Använda token

Nu när du har har skaffat en åtkomsttoken, du kan använda token i begäranden till ditt backend-webb-API: er genom att inkludera den i den `Authorization` rubrik:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Uppdatera token

ID-token går ut inom en kort tidsperiod. Uppdatera token när de går ut om du vill fortsätta att kunna komma åt resurser. Du kan uppdatera en token genom att skicka in en annan `POST` begäran till den `/token` slutpunkt. Den här gången den `refresh_token` parameter i stället för den `code` parameter:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| p | Ja | Det användarflöde som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda en annan användarflödet i den här begäran. Lägg till den här parametern i frågesträngen, inte till i brödtexten för INLÄGGET. |
| client_id | Ja | Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats till ditt program. |
| _typ av beviljande | Ja | Typ av beviljande, vilket måste vara en uppdateringstoken för den här delen av auktoriseringskodsflödet. |
| omfång | Nej | En blankstegsavgränsad lista med omfattningar. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Den kan användas för att skicka token till ditt programs egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång innebär att ditt program måste en uppdateringstoken för utökad åtkomst till resurser. |
| redirect_uri | Nej | Den `redirect_uri` -parametern för programmet som du fick Auktoriseringskoden. |
| refresh_token | Ja | Den ursprungliga uppdateringstoken som har köpts i den andra delen av flödet. Den `offline_access` omfång måste användas i både auktoriserings- och tokenbegäranden för att få en uppdateringstoken. |
| client_secret | Ja | Programhemlighet som har genererats i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift-artefakt. Du bör lagra den på ett säkert sätt på din server. Ändra den här klienthemlighet på regelbunden basis. |

Det ser ut som ett lyckat svar för token:

```
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
| not_before | Den tid då token betraktas som giltigt i epoktid. |
| token_type | Typ tokenu-värde. `Bearer` är den enda typ som stöds. |
| access_token | Den signera JWT-token som begärdes. |
| omfång | Omfattningen som token är giltig. |
| expires_in | Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token | OAuth 2.0-uppdateringstoken. Programmet kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token kan användas för att behålla åtkomst till resurser i längre tid. |

Felsvar se ut:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som uppstår. |
| error_description | Ett meddelande som kan identifiera de grundläggande orsakerna till ett autentiseringsfel. |

## <a name="send-a-sign-out-request"></a>Skicka en förfrågan om utloggning

När du vill logga ut användaren från programmet, det räcker inte att rensa programmets cookies eller på annat sätt avsluta sessionen med användaren. Omdirigera användaren till Azure AD B2C för att logga ut. Om du inte göra det, kan användaren eventuellt att autentiseras på nytt till programmet utan att behöva ange sina autentiseringsuppgifter igen.

Du kan helt enkelt omdirigera användaren till den `end_session` slutpunkt som anges i metadata OpenID Connect-dokumentet som beskrivs ovan:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| p | Ja | Användarflödet som du vill använda för att logga ut användaren från ditt program. |
| post_logout_redirect_uri | Nej | Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om det inte är inkluderat visar Azure AD B2C användaren ett allmänt meddelande. |

Dirigera användare till den `end_session` endpoint tar bort några av användarens inloggning tillstånd med Azure AD B2C, men det inte logga ut användaren från sina sociala provider (IDP)-session. Om användaren väljer samma IDP under en efterföljande inloggning, är de autentisera, utan att behöva ange sina autentiseringsuppgifter. Om en användare vill logga ut från programmet, att inte nödvändigtvis de vill logga ut från deras Facebook-konto. Men om lokala konton som används, avslutas användarens session korrekt.

