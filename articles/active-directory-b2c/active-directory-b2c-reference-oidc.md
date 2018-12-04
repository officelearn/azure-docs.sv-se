---
title: Webb-logga in med OpenID Connect i Azure Active Directory B2C | Microsoft Docs
description: Att skapa webbprogram med hjälp av Azure Active Directory-implementeringen av autentiseringsprotokollet OpenID Connect.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 41f6027378e48b525345e29e1d1e08dd2c48aaa5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843758"
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: Webbinloggning med OpenID Connect
OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0, som kan användas på ett säkert sätt logga användare in webbprogram. Genom att använda den Azure Active Directory B2C (Azure AD B2C) implementeringen av OpenID Connect, du kan lägga ut registrering, inloggning och andra Identitetshantering upplevelser i dina webbprogram till Azure Active Directory (AD Azure). Den här guiden visar hur du gör på ett språkoberoende sätt. Den beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av våra bibliotek med öppen källkod.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0 *auktorisering* protokoll för användning som en *autentisering* protokoll. På så sätt kan du utföra enkel inloggning med hjälp av OAuth. Det introducerar konceptet för en *ID-token*, vilket är en säkerhetstoken som gör att klienten att verifiera användarens identitet och få grundläggande profilinformation om användaren.

Eftersom den utökar OAuth 2.0 kan också appar på ett säkert sätt hämta *åtkomsttoken*. Du kan använda access_tokens att komma åt resurser som skyddas av en [auktoriseringsservern](active-directory-b2c-reference-protocols.md#the-basics). Vi rekommenderar OpenID Connect om du skapar ett webbprogram som finns på en server och öppnas via en webbläsare. Om du vill lägga till Identitetshantering i dina mobila eller stationära program med hjälp av Azure AD B2C bör du använda [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i stället för OpenID Connect.

Azure AD B2C utökar standard OpenID Connect-protokollet för att göra mer än enkel autentisering och auktorisering. Det inför den [user flow-parameter](active-directory-b2c-reference-policies.md), vilket gör att du kan använda OpenID Connect för att lägga till användarupplevelser – till exempel registrering, inloggning och profilhantering--till din app. Här kan visar vi hur du använder OpenID Connect och användaren flöden för att implementera var och en av dessa upplevelser i dina webbprogram. Vi kommer också visar hur du hämtar åtkomsttoken för att komma åt webb API: er.

Exempel HTTP-begäranden i nästa avsnitt använda vår exempel B2C-katalog, fabrikamb2c.onmicrosoft.com samt vårt exempelprogram https://aadb2cplayground.azurewebsites.net, och användarflöden. Du är kostnadsfritt att testa begäranden själv med hjälp av dessa värden eller du kan ersätta dem med dina egna.
Lär dig hur du [hämta dina egna B2C-klientorganisation, program och användare flöden](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Skicka begäranden om autentisering
När webbappen behöver autentisera användaren och kör ett användarflöde, det kan dirigera användare till den `/authorize` slutpunkt. Det här är den interaktiva delen av flödet, där användaren vidtar åtgärder, beroende på användarflödet.

I den här begäran klienten anger de behörigheter som krävs för att hämta för användaren i den `scope` parametern och användarflödet i för att köra den `p` parametern. Tre exempel finns i avsnitten nedan (med radbrytningar för läsbarhet), med en annan användare-flöde. Få en bild för hur varje begäran fungerar, försök att klistra in begäran i en webbläsare och kör den.

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

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| response_type |Krävs |Svarstypen som måste innehålla en ID-token för OpenID Connect. Om webbappen behöver också token för att anropa ett webb-API, kan du använda `code+id_token`, som vi visat här. |
| redirect_uri |Rekommenderas |Den `redirect_uri` parametern för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av de `redirect_uri` parametrar som du registrerade i portalen, förutom att det måste vara URL-kodas. |
| omfång |Krävs |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda behörigheter som tas emot. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token (fler är på gång på detta senare i artikeln). Den `offline_access` omfånget är valfritt för web apps. Anger det att din app behöver en *uppdateringstoken* för långlivade åtkomst till resurser. |
| response_mode |Rekommenderas |Den metod som ska användas för att skicka resulterande Auktoriseringskoden tillbaka till din app. Det kan vara antingen `query`, `form_post`, eller `fragment`.  Den `form_post` Svarsläge rekommenderas för bästa säkerhet. |
| state |Rekommenderas |Ett värde i begäran som returneras också i token-svaret. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för att förhindra attacker med förfalskning av begäran. Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel gruppsidan de befann sig i. |
| nonce |Krävs |Ett värde i begäran (genereras av appen) som ska tas med i den resulterande ID-token som ett anspråk. Appen kan sedan att verifiera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig unik sträng som kan användas för att fastställa ursprunget för begäran. |
| p |Krävs |Det användarflöde som körs. Det är namnet på ett användarflöde som skapas i din B2C-klient. Namnvärdet för användaren flow ska inledas med `b2c\_1\_`. Mer information om principer och [utökningsbara flow framework](active-directory-b2c-reference-policies.md). |
| fråga |Valfri |Typ av interaktion från användaren som krävs. Det enda giltiga värdet just nu är `login`, vilket Tvingar användaren att ange sina autentiseringsuppgifter i begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra den användarflödet arbetsflöde. Detta kan handla om användaren skriver sitt användarnamn och lösenord, logga in med en sociala identitet, registrera dig för katalogen, eller en annan siffra steg, beroende på hur användarflödet har definierats.

När du är klar användarflödet Azure AD returnerar ett svar till din app på den angivna `redirect_uri` parameter med hjälp av metoden som anges i den `response_mode` parametern. Svaret är detsamma för var och en av de föregående fall, oberoende av det användarflöde som körs.

Ett lyckat svar med `response_mode=fragment` skulle se ut:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll som ingår i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| Kod |Auktoriseringskod som appen har begärt, om du har använt `response_type=code+id_token`. Appen kan använda Auktoriseringskoden för att begära en åtkomsttoken för en målresurs. Auktoriseringskoder är mycket tillfällig. Vanligtvis de går ut efter 10 minuter. |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |

Felsvar kan också skickas till den `redirect_uri` parametern så att appen kan hantera dem på rätt sätt:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkod sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i den första tabellen i det här avsnittet. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |

## <a name="validate-the-id-token"></a>Verifiera ID-token
Bara få ett ID-token är inte tillräckligt för att autentisera användaren. Du måste verifiera signaturen för ID-token och verifiera anspråken i token enligt krav för din app. Azure AD B2C använder [JSON Web token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Det finns många bibliotek för öppen källkod som är tillgängliga för att verifiera JWTs, beroende på ditt språk för inställningar. Vi rekommenderar att utforska dessa alternativ i stället för att implementera dina egna validering av logik. Informationen här kan vara användbart i att använda dessa bibliotek normalt.

Azure AD B2C har OpenID Connect metadataslutpunkt, vilket gör att en app för att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Det finns ett JSON-dokument för metadata för varje användarflöde i din B2C-klient. Till exempel metadatadokument för den `b2c_1_sign_in` användarflödet i `fabrikamb2c.onmicrosoft.com` finns på:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En av egenskaperna för den här konfigurationsdokumentet är `jwks_uri`, vars värdet för samma användarflödet skulle vara:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

För att avgöra vilka användarflödet har används vid signering av ett ID-token (och varifrån att hämta metadata), har du två alternativ. Först userjourney-namnet som ingår i den `acr` anspråk i ID-token. Information om hur du Parsar anspråk från en ID-token som finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). Ett annat alternativ är att koda användarflödet i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilka användarflödet har använts. Någon av metoderna är giltig.

När du har köpt metadatadokument från metadataslutpunkt OpenID Connect kan du använda de offentliga nycklarna för RSA-256 (som finns i den här slutpunkten) att verifiera signaturen för ID-token. Det kan finnas flera nycklar som anges i den här slutpunkten vid en given tidpunkt i tid, alla identifierade med en `kid` anspråk. Rubriken för ID-token innehåller också en `kid` anspråk, vilket anger vilka av dessa nycklar används för att logga ID-token. Mer information finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md) (i avsnittet om [verifiera token](active-directory-b2c-reference-tokens.md#token-validation), i synnerhet).
<!--TODO: Improve the information on this-->

När du har verifierat signaturen för ID-token, finns det flera anspråk som du behöver verifiera. Exempel:

* Du bör verifiera den `nonce` anspråk som ska förhindra token repetitionsattacker. Värdet ska vara du angav i inloggning-begäran.
* Du bör verifiera den `aud` anspråk som ska se till att ID-token har utfärdats för din app. Dess värde bör vara program-ID för din app.
* Du bör verifiera den `iat` och `exp` utger sig för att se till att ID-token inte har gått ut.

Det finns också flera mer verifieringar som du bör utföra. Dessa beskrivs i detalj i de [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html).  Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Vissa vanliga verifieringar är:

* Se till att användaren/organisationen har registrerat dig för appen.
* Se till att användaren har rätt auktorisering/privilegier.
* Se till att en viss styrkan hos autentisering har inträffat, till exempel Azure Multi-Factor Authentication.

Mer information om anspråken i en ID-token finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).

När du har godkänt ID-token, kan du börja en session med användaren. Du kan använda anspråken i ID-token för att hämta information om användaren i din app. Användningsområden för den här informationen omfattar visning, poster och auktorisering.

## <a name="get-a-token"></a>Hämta en token
Om du behöver din webbapp att endast köra användarflöden kan du hoppa över nästa avsnitt. Dessa avsnitt gäller endast för appar som behöver göra autentiserade anrop till ett webb-API och också skyddas av Azure AD B2C.

Kan du lösa in den auktoriseringskod som du har köpt (med hjälp av `response_type=code+id_token`) för en token till önskad resurs genom att skicka en `POST` begäran till den `/token` slutpunkt. För närvarande är den enda resurs som du kan begära en token för din Apps egen backend-webb-API. Konventionen för att begära en token till dig själv är att använda appens klient-ID som omfång:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Krävs |Det användarflöde som användes för att hämta Auktoriseringskoden. Du kan inte använda en annan användarflödet i den här begäran. Observera att du lägger till den här parametern i frågesträngen inte till den `POST` brödtext. |
| client_id |Krävs |Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| _typ av beviljande |Krävs |Typ av anslag som måste vara `authorization_code` för auktoriseringskodsflödet. |
| omfång |Rekommenderas |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda behörigheter som tas emot. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av id_token parametrar. Det kan användas för att hämta token till din Apps egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång anger att din app behöver en uppdateringstoken för långlivade åtkomst till resurser. |
| Kod |Krävs |Auktoriseringskod som du har köpt i den första delen i flödet. |
| redirect_uri |Krävs |Den `redirect_uri` -parametern för programmet som du fick Auktoriseringskoden. |
| client_secret |Krävs |Programhemlighet som du skapade i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift-artefakt. Du bör lagra den på ett säkert sätt på din server. Du bör också rotera den här klienthemlighet på regelbunden basis. |

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
| --- | --- |
| not_before |Den tid då token betraktas som giltigt i epoktid. |
| token_type |Typ tokenu-värde. Den enda typen som har stöd för Azure AD är `Bearer`. |
| access_token |Den signera JWT-token som du har begärt. |
| omfång |Scope som token är giltig. Dessa kan användas för cachelagring av token för senare användning. |
| expires_in |Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token |OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. Mer information finns i den [B2C tokenreferens](active-directory-b2c-reference-tokens.md). Observera att du måste ha använt omfånget `offline_access` i både auktorisering och token-förfrågningar för att få en uppdateringstoken. |

Felsvar se ut:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkod sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

## <a name="use-the-token"></a>Använda token
Nu när du har har skaffat en åtkomsttoken, du kan använda token i begäranden till ditt backend-webb-API: er genom att inkludera den i den `Authorization` rubrik:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Uppdatera token
ID-token är tillfällig. Du måste uppdatera dem när de går ut om du vill fortsätta att kunna komma åt resurser. Du kan göra det genom att skicka in en annan `POST` begäran till den `/token` slutpunkt. Den här gången den `refresh_token` parameter i stället för den `code` parameter:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parameter | Krävs | Beskrivning |
| --- | --- | --- |
| p |Krävs |Det användarflöde som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda en annan användarflödet i den här begäran. Observera att du lägger till den här parametern i frågesträngen, inte till i brödtexten för INLÄGGET. |
| client_id |Krävs |Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| _typ av beviljande |Krävs |Typ av beviljande, vilket måste vara en uppdateringstoken för den här delen i auktoriseringskodsflödet. |
| omfång |Rekommenderas |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda behörigheter som tas emot. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Det kan användas för att hämta token till din Apps egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång anger att din app behöver en uppdateringstoken för långlivade åtkomst till resurser. |
| redirect_uri |Rekommenderas |Den `redirect_uri` -parametern för programmet som du fick Auktoriseringskoden. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du har köpt i den andra delen i flödet. Observera att du måste ha använt omfånget `offline_access` i både auktorisering och token-förfrågningar för att få en uppdateringstoken. |
| client_secret |Krävs |Programhemlighet som du skapade i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift-artefakt. Du bör lagra den på ett säkert sätt på din server. Du bör också rotera den här klienthemlighet på regelbunden basis. |

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
| --- | --- |
| not_before |Den tid då token betraktas som giltigt i epoktid. |
| token_type |Typ tokenu-värde. Den enda typen som har stöd för Azure AD är `Bearer`. |
| access_token |Den signera JWT-token som du har begärt. |
| omfång |Omfattningen som token är giltig för, som kan användas för cachelagring av token för senare användning. |
| expires_in |Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token |OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla.  Uppdatera token är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. Mer information finns i den [B2C tokenreferens](active-directory-b2c-reference-tokens.md). |

Felsvar se ut:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkod sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

## <a name="send-a-sign-out-request"></a>Skicka en förfrågan om utloggning
När du vill logga ut användaren från appen, räcker det inte att rensa appens cookies eller på annat sätt slutet sessionen med användaren. Du måste också omdirigera användaren till Azure AD för att logga ut. Om du inte göra det, kan användaren eventuellt att autentiseras på nytt till din app utan att behöva ange sina autentiseringsuppgifter igen. Det beror på att de har en giltig inloggnings-session med Azure AD.

Du kan helt enkelt omdirigera användaren till den `end_session` slutpunkt som visas i metadatadokument OpenID Connect som beskrivs tidigare i den ”verifiera ID-token” avsnittet:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Krävs |Användarflödet som du vill använda för att logga ut användaren från ditt program. |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om det inte finns, Azure AD B2C visar användaren ett allmänt meddelande. |

> [!NOTE]
> Även om dirigera användare till den `end_session` endpoint rensas några av användarens inloggning tillstånd med Azure AD B2C, signerar inte ut användaren från sina sociala provider (IDP)-session. Om användaren väljer samma IDP under en efterföljande inloggning, kommer de att autentisera, utan att behöva ange sina autentiseringsuppgifter. Om en användare vill logga ut från ditt B2C-program, betyder det inte att logga ut från deras Facebook-konto. Men när det gäller lokala konton avslutas användarens session korrekt.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Använda din egen B2C-klientorganisation
Om du vill prova dessa begäranden på egen hand måste du först göra följande tre steg och Ersätt sedan de exempelvärden som beskrivs ovan med dina egna:

1. [Skapa en B2C-klient](active-directory-b2c-get-started.md), och använda namnet på din klient i begäranden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) att hämta en program-ID. Inkludera en web app/webb-API i din app. Du kan också skapa en programhemlighet.
3. [Skapa din användarflöden](active-directory-b2c-reference-policies.md) att hämta din användare namn för flödet.

