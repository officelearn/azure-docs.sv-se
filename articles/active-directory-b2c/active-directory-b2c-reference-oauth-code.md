---
title: Flöde för auktoriseringskod – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du skapar webbappar med hjälp av Azure AD B2C-och OpenID Connect-autentiseringsprotokollet.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e813a16416343954ea92813cb53bbd81f1977320
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290129"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2,0 Authorization Code Flow i Azure Active Directory B2C

Du kan använda OAuth 2,0-auktoriseringskod i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. Genom att använda Azure Active Directory B2C-implementeringen (Azure AD B2C) av OAuth 2,0 kan du lägga till registrering, inloggning och andra identitets hanterings uppgifter i dina mobila och Station ära appar. Den här artikeln är språk oberoende. I artikeln beskriver vi hur du skickar och tar emot HTTP-meddelanden utan att använda bibliotek med öppen källkod.

OAuth 2,0 Authorization Code Flow beskrivs i [avsnittet 4,1 i oauth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749). Du kan använda den för autentisering och auktorisering i de flesta [program typer](active-directory-b2c-apps.md), inklusive webb program och internt installerade program. Du kan använda OAuth 2,0-auktoriseringskod för att på ett säkert sätt Hämta åtkomsttoken och uppdatera tokens för dina program, som kan användas för att få åtkomst till resurser som skyddas av en [Authorization Server](active-directory-b2c-reference-protocols.md).  Med uppdateringstoken kan klienten erhålla nya åtkomst-och uppdaterings-token när åtkomsttoken upphör att gälla, vanligt vis efter en timme.

Den här artikeln fokuserar på de **offentliga klienterna** OAuth 2,0 Authorization Code Flow. En offentlig klient är ett klient program som inte är betrott att säkert upprätthålla integriteten för ett hemligt lösen ord. Detta inkluderar mobilappar, skriv bords program och i stort sett alla program som körs på en enhet och som behöver hämta åtkomsttoken.

> [!NOTE]
> Om du vill lägga till identitets hantering i en webbapp med hjälp av Azure AD B2C använder du [OpenID Connect](active-directory-b2c-reference-oidc.md) i stället för OAuth 2,0.

Azure AD B2C utökar standard OAuth 2,0-flöden för att göra mer än enkel autentisering och auktorisering. Det introducerar [användar flödet](active-directory-b2c-reference-policies.md). Med användar flöden kan du använda OAuth 2,0 för att lägga till användar upplevelser till ditt program, till exempel registrering, inloggning och profil hantering. Identitets leverantörer som använder OAuth 2,0-protokollet är [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md)och [LinkedIn](active-directory-b2c-setup-li-app.md).

För att testa HTTP-begärandena i den här artikeln:

1. Ersätt `{tenant}` med namnet på din Azure AD B2C-klient.
1. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID: t för ett program som du tidigare har registrerat i Azure AD B2C klient organisationen.
1. Ersätt `{policy}` med namnet på en princip som du har skapat i din klient, till exempel `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. Hämta en auktoriseringskod
Kod flödet för auktorisering börjar med klienten som dirigerar användaren till `/authorize` slut punkten. Det här är den interaktiva delen av flödet, där användaren vidtar åtgärder. I den här förfrågan anger klienten i `scope` parametern de behörigheter som krävs för att hämta från användaren. Följande tre exempel (med rad brytningar för läsbarhet) var och en använder ett annat användar flöde.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|innehav| Krävs | Namnet på din Azure AD B2C-klient|
| politik | Krävs | Det användar flöde som ska köras. Ange namnet på ett användar flöde som du har skapat i Azure AD B2C klient organisationen. Till exempel: `b2c_1_sign_in`, `b2c_1_sign_up`eller `b2c_1_edit_profile`. |
| client_id |Krävs |Det program-ID som har tilldelats din app i [Azure Portal](https://portal.azure.com). |
| response_type |Krävs |Svars typen, som måste innehålla `code` för flödets auktoriseringskod. |
| redirect_uri |Krävs |Omdirigerings-URI för appen, där autentiseringsbegäranden skickas och tas emot av din app. Det måste exakt matcha en av de omdirigerings-URI: er som du har registrerat i portalen, förutom att den måste vara URL-kodad. |
| omfång |Krävs |En blankstegsavgränsad lista över omfång. Ett enda omfattnings värde indikerar att Azure Active Directory (Azure AD) båda de behörigheter som begärs. Med hjälp av klient-ID som omfånget anger att din app behöver en åtkomsttoken som kan användas för din egen tjänst eller ditt webb-API som representeras av samma klient-ID.  `offline_access` omfånget indikerar att appen behöver en uppdateringstoken för långvarig åtkomst till resurser. Du kan också använda `openid` omfång för att begära en ID-token från Azure AD B2C. |
| response_mode |Rekommenderad |Den metod som du använder för att skicka den resulterande auktoriseringskod tillbaka till din app. Det kan vara `query`, `form_post`eller `fragment`. |
| state |Rekommenderad |Ett värde som ingår i begäran som kan vara en sträng med innehåll som du vill använda. Normalt används ett slumpmässigt genererat unikt värde för att förhindra förfalsknings attacker på begäran från en webbplats. Det här läget används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat. Till exempel sidan användaren var på eller det användar flöde som kördes. |
| visas |Valfritt |Typ av användar interaktion som krävs. För närvarande är det enda giltiga värdet `login`, vilket tvingar användaren att ange sina autentiseringsuppgifter för begäran. Enkel inloggning träder inte i kraft. |

Nu uppmanas användaren att slutföra användar flödets arbets flöde. Detta kan innebära att användaren anger sitt användar namn och lösen ord, loggar in med en social identitet, registrerar sig för katalogen eller något annat antal steg. Användar åtgärder är beroende av hur användar flödet definieras.

När användaren har slutfört användar flödet returnerar Azure AD ett svar till din app med det värde som du använde för `redirect_uri`. Den metod som anges i parametern `response_mode` används. Svaret är exakt detsamma för var och en av användar åtgärds scenarierna, oberoende av det användar flöde som kördes.

Ett lyckat svar som använder `response_mode=query` ser ut så här:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beskrivning |
| --- | --- |
| code |Den auktoriseringskod som appen begärde. Appen kan använda auktoriseringskod för att begära en åtkomsttoken för en mål resurs. Auktoriseringsregler är mycket kort. Normalt går de ut efter cirka 10 minuter. |
| state |Se den fullständiga beskrivningen i tabellen i föregående avsnitt. Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör verifiera att `state` värden i begäran och svaret är identiska. |

Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem på rätt sätt:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som du kan använda för att klassificera de typer av fel som inträffar. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i föregående tabell. Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör verifiera att `state` värden i begäran och svaret är identiska. |

## <a name="2-get-a-token"></a>2. Hämta en token
Nu när du har skaffat en auktoriseringskod kan du lösa in `code` för en token till den avsedda resursen genom att skicka en POST-begäran till `/token`-slutpunkten. I Azure AD B2C kan du [begära åtkomsttoken för andra API:](active-directory-b2c-access-tokens.md#request-a-token) er som vanligt genom att ange deras omfång i begäran.

Du kan också begära en åtkomsttoken för appens egna Server dels webb-API genom att använda appens klient-ID som begärda omfång (vilket leder till en åtkomsttoken med klient-ID: t som mål grupp):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|innehav| Krävs | Namnet på din Azure AD B2C-klient|
|politik| Krävs| Det användar flöde som användes för att hämta auktoriseringskod. Du kan inte använda ett annat användar flöde i denna begäran. |
| client_id |Krävs |Det program-ID som har tilldelats din app i [Azure Portal](https://portal.azure.com).|
| client_secret | Ja, i Web Apps | Den program hemlighet som genererades i [Azure Portal](https://portal.azure.com/). Klient hemligheter används i det här flödet för scenarier med webb program, där klienten kan lagra en klient hemlighet på ett säkert sätt. För interna app-scenarier (offentliga klienter) kan inte klient hemligheter lagras på ett säkert sätt och används därför inte i det här anropet. Om du använder en klient hemlighet måste du ändra den regelbundet. |
| grant_type |Krävs |Typ av beviljande. För Authorization Code Flow måste tilldelnings typen `authorization_code`. |
| omfång |Rekommenderad |En blankstegsavgränsad lista över omfång. Ett enda omfattnings värde indikerar Azure AD båda de behörigheter som begärs. Med hjälp av klient-ID som omfånget anger att din app behöver en åtkomsttoken som kan användas för din egen tjänst eller ditt webb-API som representeras av samma klient-ID.  `offline_access` omfånget indikerar att appen behöver en uppdateringstoken för långvarig åtkomst till resurser.  Du kan också använda `openid` omfång för att begära en ID-token från Azure AD B2C. |
| code |Krävs |Den auktoriseringskod som du förvärvade i den första delen av flödet. |
| redirect_uri |Krävs |Omdirigerings-URI för programmet där du fick auktoriseringskod. |

Ett lyckat token-svar ser ut så här:

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
| --- | --- |
| not_before |Tiden då token anses vara giltig, i epok tid. |
| token_type |Värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. |
| access_token |Den signerade JSON Web Token (JWT) som du har begärt. |
| omfång |De omfattningar som token är giltigt för. Du kan också använda omfattningar för att cachelagra token för senare användning. |
| expires_in |Den tids längd som token är giltig (i sekunder). |
| refresh_token |En OAuth 2,0-uppdateringstoken. Appen kan använda denna token för att hämta ytterligare token när aktuell token upphör att gälla. Uppdateringstoken är lång. Du kan använda dem för att behålla åtkomst till resurser under långa tids perioder. Mer information finns i referens för [Azure AD B2C-token](active-directory-b2c-reference-tokens.md). |

Fel svar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som du kan använda för att klassificera de typer av fel som inträffar. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

## <a name="3-use-the-token"></a>3. Använd token
Nu när du har skaffat en åtkomsttoken kan du använda token i begär anden till dina Server dels webb-API: er genom att inkludera den i `Authorization` rubriken:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. uppdatera token
Åtkomsttoken och ID-token är korta. När de har gått ut måste du uppdatera dem för att fortsätta att få åtkomst till resurser. Det gör du genom att skicka en ny POST-begäran till `/token` slut punkten. Nu anger du `refresh_token` i stället för `code`:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|innehav| Krävs | Namnet på din Azure AD B2C-klient|
|politik |Krävs |Det användar flöde som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda ett annat användar flöde i denna begäran. |
| client_id |Krävs |Det program-ID som har tilldelats din app i [Azure Portal](https://portal.azure.com). |
| client_secret | Ja, i Web Apps | Den program hemlighet som genererades i [Azure Portal](https://portal.azure.com/). Klient hemligheter används i det här flödet för scenarier med webb program, där klienten kan lagra en klient hemlighet på ett säkert sätt. För interna app-scenarier (offentliga klienter) kan inte klient hemligheter lagras på ett säkert sätt och används därför inte i det här anropet. Om du använder en klient hemlighet måste du ändra den regelbundet. |
| grant_type |Krävs |Typ av beviljande. För den här delen av koden för auktoriseringskod måste tilldelnings typen `refresh_token`. |
| omfång |Rekommenderad |En blankstegsavgränsad lista över omfång. Ett enda omfattnings värde indikerar Azure AD båda de behörigheter som begärs. Med hjälp av klient-ID som omfånget anger att din app behöver en åtkomsttoken som kan användas för din egen tjänst eller ditt webb-API som representeras av samma klient-ID.  `offline_access` omfånget indikerar att appen behöver en uppdateringstoken för långvarig åtkomst till resurser.  Du kan också använda `openid` omfång för att begära en ID-token från Azure AD B2C. |
| redirect_uri |Valfritt |Omdirigerings-URI för programmet där du fick auktoriseringskod. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du förvärvade i den andra delen av flödet. |

Ett lyckat token-svar ser ut så här:

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
| --- | --- |
| not_before |Tiden då token anses vara giltig, i epok tid. |
| token_type |Värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. |
| access_token |Det signerade JWT som du begärde. |
| omfång |De omfattningar som token är giltigt för. Du kan också använda omfattningarna för att cachelagra tokens för senare användning. |
| expires_in |Den tids längd som token är giltig (i sekunder). |
| refresh_token |En OAuth 2,0-uppdateringstoken. Appen kan använda denna token för att hämta ytterligare token när aktuell token upphör att gälla. Uppdaterade token är långvariga och kan användas för att bevara åtkomsten till resurser under längre tids perioder. Mer information finns i referens för [Azure AD B2C-token](active-directory-b2c-reference-tokens.md). |

Fel svar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Använda din egen Azure AD B2C katalog
Utför följande steg för att testa dessa begär Anden själv. Ersätt de exempel värden som vi använde i den här artikeln med dina egna värden.

1. [Skapa en Azure AD B2C katalog](active-directory-b2c-get-started.md). Använd namnet på din katalog i begär Anden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) för att hämta ett program-ID och en omdirigerings-URI. Inkludera en intern klient i din app.
3. [Skapa dina användar flöden](active-directory-b2c-reference-policies.md) för att hämta dina användar flödes namn.
