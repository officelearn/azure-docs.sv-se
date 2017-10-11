---
title: Webb-inloggning med OpenID Connect - Azure AD B2C | Microsoft Docs
description: "Skapa webbprogram med hjälp av Azure Active Directory-implementeringen av autentiseringsprotokollet OpenID Connect"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: b0c33a47dd0cae79eab32ac578448fae8bf59be5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: Web inloggning med OpenID Connect
OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0 som kan användas för säker inloggning användare till webbprogram. Med hjälp av Azure Active Directory B2C (Azure AD B2C) implementering av OpenID Connect, du kan flytta över registrering, inloggning och andra Identitetshantering upplever i ditt webbprogram till Azure Active Directory (AD Azure). Den här guiden visar hur du gör en språkoberoende sätt. Det beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av våra bibliotek med öppen källkod.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0 *auktorisering* protokoll som ska användas som en *autentisering* protokoll. På så sätt kan du utföra enkel inloggning med hjälp av OAuth. Den introducerar konceptet för en *ID token*, vilket är en säkerhetstoken som gör att klienten kan verifiera användarens identitet och få grundläggande profilinformation om användaren.

Eftersom den utökar OAuth 2.0 kan också appar att på ett säkert sätt hämta *åtkomst till token*. Du kan använda access_tokens åtkomst till resurser som skyddas av en [auktorisering server](active-directory-b2c-reference-protocols.md#the-basics). Vi rekommenderar OpenID Connect om du utvecklar ett program som finns på en server och öppnas via en webbläsare. Om du vill lägga till Identitetshantering i dina mobila eller stationära program med Azure AD B2C, bör du använda [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i stället för OpenID Connect.

Azure AD B2C utökar OpenID Connect standardprotokollet för att göra mer än enkel autentisering och auktorisering. Det inför den [parametern](active-directory-b2c-reference-policies.md), som gör att du kan använda OpenID Connect för att lägga till användarupplevelser – till exempel registrering, inloggning och profilhantering--till din app. Här kan hur vi du använder OpenID Connect och principer för att implementera var och en av dessa upplevelser i ditt webbprogram. Vi också lära dig hur du kan få åtkomst-token för åtkomst till webb-API: er.

Exempel HTTP-förfrågningar i nästa avsnitt använda våra exempel B2C-katalog, fabrikamb2c.onmicrosoft.com, samt våra exempelprogrammet, https://aadb2cplayground.azurewebsites.net och principer. Kan du prova att använda begäranden själv med hjälp av dessa värden eller kan du ersätta dem med din egen.
Lär dig hur du [kommer B2C-klient, program och principer](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden
När ditt webbprogram måste autentisera användaren och köra en princip, den kan dirigera användare till den `/authorize` slutpunkt. Det här är den interaktiva delen av flödet, där användaren vidtar åtgärder beroende på principen.

I den här förfrågan klienten anger de behörigheter som krävs för att hämta för användaren i den `scope` parameter och principen för att köras i den `p` parameter. Tre exemplen finns i följande avsnitt (med radbrytningar för att läsa), varje med hjälp av en annan princip. Att få en bild av hur fungerar varje begäran, försök att klistra in begäran i en webbläsare och kör den.

#### <a name="use-a-sign-in-policy"></a>Använda en princip för inloggning
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Använda en princip för registrering
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Använda en princip för Redigera-profil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Krävs |Programmet ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| response_type |Krävs |Svarstyp som måste innehålla en ID-token för OpenID Connect. Om ditt webbprogram måste också token för att anropa ett webb-API, kan du använda `code+id_token`eftersom vi har gjort här. |
| redirect_uri |Rekommenderas |Den `redirect_uri` parameter för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en av de `redirect_uri` parametrar som du har registrerat i portalen, förutom att det måste vara URL-kodade. |
| Omfång |Krävs |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD både behörigheter som krävs. Den `openid` omfång anger behörighet att logga in användaren och hämta data om användaren i form av ID-token (mer komma på den här senare i artikeln). Den `offline_access` scope är valfritt för webbprogram. Anger det att din app måste en *uppdateringstoken* för långlivade åtkomst till resurser. |
| response_mode |Rekommenderas |Den metod som ska användas för att skicka den resulterande Auktoriseringskoden tillbaka till din app. Det kan vara antingen `query`, `form_post`, eller `fragment`.  Den `form_post` svar läge rekommenderas för bästa säkerhet. |
| state |Rekommenderas |Ett värde som ingår i denna begäran returneras också token svar. Det kan vara en sträng med innehåll som du vill använda. Ett slumpmässigt genererat unikt värde används vanligtvis för att förhindra attacker med förfalskning av begäran. Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis de var på sidan. |
| temporärt ID |Krävs |Ett värde som ingår i denna begäran (genereras av appen) som ska tas med i den resulterande ID-token som ett anspråk. Appen kan sedan kontrollera värdet för att minimera token replay-attacker. Värdet är vanligtvis en slumpmässig unik sträng som används för att identifiera ursprunget för begäran. |
| P |Krävs |Den princip som kommer att utföras. Det är namnet på en princip som skapas i din B2C-klient. Princip för namn-värde ska inledas med `b2c\_1\_`. Mer information om principer och [expanderbara principramverk](active-directory-b2c-reference-policies.md). |
| kommandotolk |Valfri |Typ av användarinteraktion som krävs. Det enda giltiga värdet för tillfället är `login`, som tvingar användaren att ange sina autentiseringsuppgifter på begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra arbetsflödet för den principen. Detta kan handla om användaren att ange sina användarnamn och lösenord, logga in med en sociala identitet registrerar sig för katalogen, eller en annan siffra av steg, beroende på hur principen har definierats.

När användaren uppfyller principen, Azure AD tillbaka ett svar på din app på den angivna `redirect_uri` parameter med hjälp av metoden som anges i den `response_mode` parameter. Svaret är samma för var och en av de föregående fall, oberoende av den princip som körs.

Ett lyckat svar med `response_mode=fragment` skulle se ut:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll ingår i den [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). |
| Koden |Auktoriseringskoden som appen har begärt, om du har använt `response_type=code+id_token`. Appen kan använda Auktoriseringskoden för att begära en åtkomst-token för en målresurs. Auktoriseringskoder är mycket tillfällig. Vanligtvis de går ut efter 10 minuter. |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |

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
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i den första tabellen i det här avsnittet. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |

## <a name="validate-the-id-token"></a>Verifiera ID-token
Bara tar emot en token med ID: T är inte tillräckligt för att autentisera användaren. Du måste verifiera signaturen för ID-token och kontrollera anspråk i token per krav som din app. Azure AD B2C använder [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Det finns många öppen källkod bibliotek som är tillgängliga för att validera JWTs, beroende på ditt språk preferensordning. Vi rekommenderar att utforska alternativen i stället för att implementera din egen valideringslogik. Den här informationen kan vara användbart i räkna ut hur du använder dessa bibliotek korrekt.

Azure AD B2C har OpenID Connect metadata slutpunkt, vilket gör att en app att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Det finns en JSON-dokumentet för metadata för varje princip i din B2C-klient. Till exempel Metadatadokumentet för den `b2c_1_sign_in` princip i `fabrikamb2c.onmicrosoft.com` finns på:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En av egenskaperna för den här konfigurationsdokument är `jwks_uri`, vars värde för samma princip skulle vara:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

För att avgöra vilken princip som har använts i ett ID-signering token (och varifrån att hämta metadata), har du två alternativ. Först principnamnet ingår i den `acr` anspråk i ID-token. Information om hur du Parsar anspråk från en ID-token, finns det [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). Ett annat alternativ är att koda principen i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilken princip som har använts. Antingen metoden är giltig.

När du har skaffat Metadatadokumentet från metadataslutpunkten OpenID Connect, kan du använda de offentliga nycklarna för RSA-256 (som finns i den här slutpunkten) att verifiera signaturen för ID-token. Det kan finnas flera nycklar som anges i den här slutpunkten vid en viss tidpunkt, alla identifierade med ett `kid` anspråk. Rubriken för ID-token innehåller också en `kid` anspråk, vilket anger vilken av dessa nycklar användes för att signera ID-token. Mer information finns i [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md) (avsnittet [verifiera token](active-directory-b2c-reference-tokens.md#token-validation), i synnerhet).
<!--TODO: Improve the information on this-->

När du har verifierats signaturen för ID-token, finns det flera anspråk som du behöver verifiera. Exempel:

* Du bör verifiera den `nonce` anspråk att förhindra att token replay-attacker. Värdet ska du angav i begäran inloggning.
* Du bör verifiera den `aud` anspråk så att det ID-token har utfärdats för din app. Värdet ska vara program-ID för din app.
* Du bör verifiera den `iat` och `exp` utger sig för att säkerställa att ID-token inte har gått ut.

Det finns också flera mer verifieringar som ska utföras. Dessa beskrivs i detalj i den [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html).  Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Några vanliga verifieringar inkluderar:

* Se till att användarorganisation har registrerat dig för appen.
* Se till att användaren har rätt behörighet/privilegier.
* Se till att en viss styrkan hos autentisering har inträffat, till exempel Azure Multi-Factor Authentication.

Mer information om anspråk i en token med ID: T finns på [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md).

När du har validerat ID-token kan du börja en session med användaren. Du kan använda anspråken i ID-token för att hämta information om användaren i din app. Användningsområden för den här informationen omfattar bildskärm, poster och auktorisering.

## <a name="get-a-token"></a>Hämta en token
Om du behöver ditt webbprogram till att bara köra principer kan du hoppa över de följande avsnitten. Dessa avsnitt gäller endast för appar som behöver göra autentiserade anrop till ett webb-API och också skyddas av Azure AD B2C.

Du kan lösa Auktoriseringskoden som du har köpt (med hjälp av `response_type=code+id_token`) för en token för en resurs genom att skicka en `POST` begäran om att den `/token` slutpunkt. För närvarande är den enda resursen som du kan begära en token för din Apps egen backend-webb-API. Konventionen för att begära en token till dig själv är att använda appens klient-ID som scope:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| P |Krävs |Den princip som användes för att hämta Auktoriseringskoden. Du kan inte använda en annan princip i den här förfrågan. Observera att du lägger inte till den här parametern frågesträngen den `POST` brödtext. |
| client_id |Krävs |Programmet ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| grant_type |Krävs |Typ av bevilja som måste vara `authorization_code` för auktoriseringskodflödet. |
| Omfång |Rekommenderas |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD både behörigheter som krävs. Den `openid` omfång anger behörighet att logga in användaren och hämta data om användaren i form av id_token parametrar. Det kan användas för att hämta token till din Apps egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång anger att din app måste en uppdateringstoken för långlivade åtkomst till resurser. |
| Koden |Krävs |Auktoriseringskoden som du har införskaffade i den första del av flödet. |
| redirect_uri |Krävs |Den `redirect_uri` parametern för programmet som du fick Auktoriseringskoden. |
| client_secret |Krävs |Den hemlighet som programmet som du skapade i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift artefakt. Du bör lagra den på ett säkert sätt på din server. Du bör också rotera denna klienthemlighet regelbundet. |

Det ser ut som ett lyckat token svar:

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
| not_before |Den tid då token betraktas som giltigt epok tidpunkt. |
| token_type |Tokentypen-värde. Den enda typen som har stöd för Azure AD är `Bearer`. |
| access_token |Den signera JWT-token som du begärde. |
| Omfång |Scope token är giltig. Dessa kan användas för att cachelagra token för senare användning. |
| expires_in |Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token |En token för uppdatering av OAuth 2.0. Appen kan använda denna token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token är långlivade och kan användas för att få åtkomst till resurser för längre tid. Mer information finns i den [B2C tokenreferens](active-directory-b2c-reference-tokens.md). Observera att du måste ha använt omfånget `offline_access` i auktoriserings- och token-förfrågningar för att kunna ta emot en uppdateringstoken. |

Felsvar som liknar:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkod sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

## <a name="use-the-token"></a>Använda token
Nu när du har har skaffat ett åtkomsttoken, kan du använda token i begäranden till din backend-webb-API: er genom att inkludera den i den `Authorization` huvud:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Uppdatera token
ID-token är tillfällig. Du måste uppdatera dem när de går ut om du vill fortsätta att kunna komma åt resurser. Du kan göra det genom att skicka in en annan `POST` begäran om att den `/token` slutpunkt. Den här gången den `refresh_token` parameter i stället för den `code` parameter:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parameter | Krävs | Beskrivning |
| --- | --- | --- |
| P |Krävs |Den princip som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda en annan princip i den här förfrågan. Observera att du lägger till den här parametern frågesträngen, inte efter innehållet. |
| client_id |Krävs |Programmet ID som den [Azure-portalen](https://portal.azure.com/) tilldelats din app. |
| grant_type |Krävs |Typ av bevilja som måste vara en uppdateringstoken för denna del av auktoriseringskodflödet. |
| Omfång |Rekommenderas |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD både behörigheter som krävs. Den `openid` omfång anger behörighet att logga in användaren och hämta data om användaren i form av ID-token. Det kan användas för att hämta token till din Apps egen backend-webb-API, som representeras av samma program-ID som klienten. Den `offline_access` omfång anger att din app måste en uppdateringstoken för långlivade åtkomst till resurser. |
| redirect_uri |Rekommenderas |Den `redirect_uri` parametern för programmet som du fick Auktoriseringskoden. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du har införskaffade i andra del av flödet. Observera att du måste ha använt omfånget `offline_access` i auktoriserings- och token-förfrågningar för att kunna ta emot en uppdateringstoken. |
| client_secret |Krävs |Den hemlighet som programmet som du skapade i den [Azure-portalen](https://portal.azure.com/). Den här programhemligheten är en viktig säkerhetsuppgift artefakt. Du bör lagra den på ett säkert sätt på din server. Du bör också rotera denna klienthemlighet regelbundet. |

Det ser ut som ett lyckat token svar:

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
| not_before |Den tid då token betraktas som giltigt epok tidpunkt. |
| token_type |Tokentypen-värde. Den enda typen som har stöd för Azure AD är `Bearer`. |
| access_token |Den signera JWT-token som du begärde. |
| Omfång |Omfattningen som token är giltig för, som kan användas för att cachelagra token för senare användning. |
| expires_in |Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| refresh_token |En token för uppdatering av OAuth 2.0. Appen kan använda denna token för att hämta ytterligare token när den aktuella token upphör att gälla.  Uppdatera token är långlivade och kan användas för att få åtkomst till resurser för längre tid. Mer information finns i den [B2C tokenreferens](active-directory-b2c-reference-tokens.md). |

Felsvar som liknar:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkod sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning
När du vill logga ut från appen användaren räcker det inte att rensa din app cookies eller på annat sätt slutet sessionen med användaren. Du måste också dirigerar användaren till Azure AD för att logga ut. Om du inte göra det, kanske användaren kan autentiseras i appen utan att ange sina autentiseringsuppgifter igen. Det beror på att de har en giltig inloggning session med Azure AD.

Du kan bara dirigera användare till den `end_session` slutpunkt som anges i Metadatadokumentet OpenID Connect beskrivs tidigare i det ”verifiera ID-token” avsnittet:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| P |Krävs |Den princip som du vill använda för att signera användare utanför tillämpningsprogrammet. |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om det inte finns, Azure AD B2C visar användaren ett allmänt meddelande. |

> [!NOTE]
> Även om dirigera användare till den `end_session` endpoint tar bort vissa av användarens inloggning tillstånd med Azure AD B2C, signerar inte användaren utanför sin sociala identitet provider (IDP)-session. Om användaren väljer samma IDP under en efterföljande inloggning, kommer de att autentisera, utan att behöva ange sina autentiseringsuppgifter. Om en användare vill logga ut från tillämpningsprogrammet B2C betyder den inte de vill logga ut från sitt Facebook-konto. Men för lokala konton avslutas användarens session korrekt.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Använda en egen B2C-klient
Om du vill prova dessa begäranden på egen hand måste du först göra följande tre steg och Ersätt exempelvärden som beskrivs ovan med dina egna:

1. [Skapa en B2C-klient](active-directory-b2c-get-started.md), och använda namnet på din klient i begäranden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) att hämta ett-ID. Inkludera en web app/webb-API i din app. Du kan också skapa ett programhemlighet.
3. [Skapa dina principer](active-directory-b2c-reference-policies.md) att hämta principens namn.

