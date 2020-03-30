---
title: Arbetsflöde för auktoriseringskod – Azure Active Directory B2C | Microsoft-dokument
description: Lär dig hur du skapar webbappar med hjälp av Azure AD B2C och OpenID Connect autentiseringsprotokoll.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260896"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0 auktoriseringskodflöde i Azure Active Directory B2C

Du kan använda auktoriseringskoden OAuth 2.0 i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API:er. Genom att använda Implementeringen av OAuth 2.0 (Azure Active Directory B2C) (Azure ACTIVE DIRECTORY B2C) kan du lägga till registrerings-, inloggnings- och andra identitetshanteringsuppgifter i dina mobila och stationära appar. Denna artikel är språkoberoende. I artikeln beskriver vi hur du skickar och ta emot HTTP-meddelanden utan att använda några bibliotek med öppen källkod.

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). Du kan använda den för autentisering och auktorisering i de flesta [programtyper,](application-types.md)inklusive webbprogram och inbyggda installerade program. Du kan använda Auktoriseringskodflödet OAuth 2.0 för att på ett säkert sätt hämta åtkomsttoken och uppdatera token för dina program, som kan användas för att komma åt resurser som skyddas av en [auktoriseringsserver](protocols-overview.md).  Uppdateringstoken gör det möjligt för klienten att hämta nya åtkomsttoken (och uppdatera) när åtkomsttoken upphör att gälla, vanligtvis efter en timme.

Den här artikeln fokuserar på de **offentliga klienterna** OAuth 2.0 auktoriseringskod flöde. En offentlig klient är ett klientprogram som inte kan lita på att säkert upprätthålla integriteten för ett hemligt lösenord. Detta inkluderar mobilappar, skrivbordsprogram och i huvudsak alla program som körs på en enhet och behöver för att få åtkomsttoken.

> [!NOTE]
> Om du vill lägga till identitetshantering i en webbapp med Hjälp av Azure AD B2C använder du [OpenID Connect](openid-connect.md) i stället för OAuth 2.0.

Azure AD B2C utökar standard-OAuth 2.0-flödena för att göra mer än enkel autentisering och auktorisering. Det introducerar [användarflödet](user-flow-overview.md). Med användarflöden kan du använda OAuth 2.0 för att lägga till användarupplevelser i ditt program, till exempel registrering, inloggning och profilhantering. Identitetsleverantörer som använder OAuth 2.0-protokollet är [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)och [LinkedIn](identity-provider-linkedin.md).

Så här provar du HTTP-begäranden i den här artikeln:

1. Ersätt `{tenant}` med namnet på din Azure AD B2C-klient.
1. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID:t för ett program som du tidigare har registrerat i din Azure AD B2C-klient.
1. Ersätt `{policy}` med namnet på en princip som du har `b2c_1_sign_in`skapat i din klientorganisation, till exempel .

## <a name="1-get-an-authorization-code"></a>1. Få en auktoriseringskod
Auktoriseringskodflödet börjar med att klienten dirigerar användaren till `/authorize` slutpunkten. Detta är den interaktiva delen av flödet, där användaren vidtar åtgärder. I den här begäran anger `scope` klienten i parametern de behörigheter som den behöver hämta från användaren. Följande tre exempel (med radbrytningar för läsbarhet) använder var och en ett annat användarflöde.


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
|{klient}| Krävs | Namn på din Azure AD B2C-klient|
| {princip} | Krävs | Användarflödet som ska köras. Ange namnet på ett användarflöde som du har skapat i din Azure AD B2C-klient. Till `b2c_1_sign_in`exempel: `b2c_1_sign_up`, `b2c_1_edit_profile`eller . |
| client_id |Krävs |Program-ID som tilldelats din app i [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Svarstypen, som `code` måste inkluderas för auktoriseringskodflödet. |
| redirect_uri |Krävs |Omdirigerings-URI för din app, där autentiseringssvar skickas och tas emot av din app. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen, förutom att den måste vara URL-kodad. |
| omfång |Krävs |En utrymmesavskiljande lista över scope. Ett enda scopevärde anger för Azure Active Directory (Azure AD) båda behörigheterna som begärs. Använda klient-ID som scope anger att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, representerad av samma klient-ID.  Scopet `offline_access` anger att din app behöver en uppdateringstoken för långlivad åtkomst till resurser. Du kan också `openid` använda scopet för att begära en ID-token från Azure AD B2C. |
| response_mode |Rekommenderas |Den metod som du använder för att skicka tillbaka den resulterande auktoriseringskoden till din app. Det kan `query` `form_post`vara `fragment`, , eller . |
| state |Rekommenderas |Ett värde som ingår i begäran och som kan vara en sträng med allt innehåll som du vill använda. Vanligtvis används ett slumpmässigt genererat unikt värde för att förhindra förfalskningsattacker mellan webbplatser. Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Till exempel sidan som användaren var på eller användarflödet som kördes. |
| Snabb |Valfri |Den typ av användarinteraktion som krävs. För närvarande är `login`det enda giltiga värdet , vilket tvingar användaren att ange sina autentiseringsuppgifter på den begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra arbetsflödet för användarflödet. Detta kan innebära att användaren anger sitt användarnamn och lösenord, loggar in med en social identitet, registrerar sig för katalogen eller något annat antal steg. Användaråtgärder beror på hur användarflödet definieras.

När användaren har slutfört användarflödet returnerar Azure AD ett svar till `redirect_uri`din app till det värde som du använde för . Den använder den metod `response_mode` som anges i parametern. Svaret är exakt detsamma för var och en av användaråtgärdsscenarier, oberoende av användarflödet som utfördes.

Ett lyckat `response_mode=query` svar som använder ser ut så här:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beskrivning |
| --- | --- |
| kod |Auktoriseringskoden som appen begärde. Appen kan använda auktoriseringskoden för att begära en åtkomsttoken för en målresurs. Tillståndskoder är mycket kortlivade. Vanligtvis upphör de att gälla efter ca 10 minuter. |
| state |Se hela beskrivningen i tabellen i föregående avsnitt. Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera `state` att värdena i begäran och svaret är identiska. |

Felsvar kan också skickas till omdirigera URI så att appen kan hantera dem på rätt sätt:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som du kan använda för att klassificera de typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |
| state |Se hela beskrivningen i tabellen föregående. Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera `state` att värdena i begäran och svaret är identiska. |

## <a name="2-get-a-token"></a>2. Få en token
Nu när du har fått en auktoriseringskod kan du lösa in `code` för `/token` en token till den avsedda resursen genom att skicka en POST-begäran till slutpunkten. I Azure AD B2C kan du [begära åtkomsttoken för andra API:er](access-tokens.md#request-a-token) som vanligt genom att ange deras scope i begäran.

Du kan också begära en åtkomsttoken för appens eget webb-API via en konvention om att använda appens klient-ID som begärt scope (vilket resulterar i en åtkomsttoken med klient-ID som "målgrupp"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|{klient}| Krävs | Namn på din Azure AD B2C-klient|
|{princip}| Krävs| Användarflödet som användes för att hämta auktoriseringskoden. Du kan inte använda ett annat användarflöde i den här begäran. |
| client_id |Krävs |Program-ID som tilldelats din app i [Azure-portalen](https://portal.azure.com).|
| client_secret | Ja, i webbappar | Programhemligheten som genererades i [Azure-portalen](https://portal.azure.com/). Klienthemligheter används i det här flödet för Web App-scenarier, där klienten säkert kan lagra en klienthemlighet. För scenarier för native app (offentlig klient) kan klienthemligheter inte lagras på ett säkert sätt och används därför inte i det här anropet. Om du använder en klienthemlighet, ändra den regelbundet. |
| grant_type |Krävs |Typ av bidrag. För auktoriseringskodflödet måste `authorization_code`bidragstypen vara . |
| omfång |Rekommenderas |En utrymmesavskiljande lista över scope. Ett enda scopevärde anger för Azure AD båda de behörigheter som begärs. Använda klient-ID som scope anger att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, representerad av samma klient-ID.  Scopet `offline_access` anger att din app behöver en uppdateringstoken för långlivad åtkomst till resurser.  Du kan också `openid` använda scopet för att begära en ID-token från Azure AD B2C. |
| kod |Krävs |Auktoriseringskoden som du fick i den första delen av flödet. |
| redirect_uri |Krävs |Omdirigerings-URI för programmet där du tog emot auktoriseringskoden. |

Ett lyckat tokensvar ser ut så här:

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
| not_before |Den tidpunkt då token anses giltig, i epoktid. |
| token_type |Tokentypsvärdet. Den enda typen som Azure AD stöder är Bärare. |
| access_token |Den signerade JSON Web Token (JWT) som du begärde. |
| omfång |Scope som token är giltig för. Du kan också använda scope för att cachelagra token för senare användning. |
| expires_in |Den tid som token är giltig (i sekunder). |
| refresh_token |En OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token efter att den aktuella token har gått ut. Uppdateringstoken är långlivade. Du kan använda dem för att behålla åtkomsten till resurser under längre tidsperioder. Mer information finns i [Azure AD B2C-tokenreferensen](tokens-overview.md). |

Felsvar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som du kan använda för att klassificera de typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

## <a name="3-use-the-token"></a>3. Använd token
Nu när du har skaffat en åtkomsttoken kan du använda token i begäranden till `Authorization` dina backend-webb-API:er genom att inkludera den i sidhuvudet:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Uppdatera token
Åtkomsttoken och ID-token är kortlivade. När de har upphört att gälla måste du uppdatera dem så att de kan fortsätta att komma åt resurser. Det gör du genom att `/token` skicka en annan POST-begäran till slutpunkten. Den här gången, ge `refresh_token` i stället `code`för:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
|{klient}| Krävs | Namn på din Azure AD B2C-klient|
|{princip} |Krävs |Användarflödet som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda ett annat användarflöde i den här begäran. |
| client_id |Krävs |Program-ID som tilldelats din app i [Azure-portalen](https://portal.azure.com). |
| client_secret | Ja, i webbappar | Programhemligheten som genererades i [Azure-portalen](https://portal.azure.com/). Klienthemligheter används i det här flödet för Web App-scenarier, där klienten säkert kan lagra en klienthemlighet. För scenarier för native app (offentlig klient) kan klienthemligheter inte lagras på ett säkert sätt och används därför inte i det här anropet. Om du använder en klienthemlighet, ändra den regelbundet. |
| grant_type |Krävs |Typ av bidrag. För den här delen av auktoriseringskodflödet måste bidragstypen vara `refresh_token`. |
| omfång |Rekommenderas |En utrymmesavskiljande lista över scope. Ett enda scopevärde anger för Azure AD båda de behörigheter som begärs. Använda klient-ID som scope anger att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, representerad av samma klient-ID.  Scopet `offline_access` anger att din app behöver en uppdateringstoken för långlivad åtkomst till resurser.  Du kan också `openid` använda scopet för att begära en ID-token från Azure AD B2C. |
| redirect_uri |Valfri |Omdirigerings-URI för programmet där du tog emot auktoriseringskoden. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du har förvärvat i den andra delen av flödet. |

Ett lyckat tokensvar ser ut så här:

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
| not_before |Den tidpunkt då token anses giltig, i epoktid. |
| token_type |Tokentypsvärdet. Den enda typen som Azure AD stöder är Bärare. |
| access_token |Den signerade JWT som du begärde. |
| omfång |Scope som token är giltig för. Du kan också använda scope för att cachelagra token för senare användning. |
| expires_in |Den tid som token är giltig (i sekunder). |
| refresh_token |En OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token efter att den aktuella token har gått ut. Uppdatera token är långlivade och kan användas för att behålla åtkomsten till resurser under längre tidsperioder. Mer information finns i [Azure AD B2C-tokenreferensen](tokens-overview.md). |

Felsvar ser ut så här:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som du kan använda för att klassificera typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Använda din egen Azure AD B2C-katalog
Om du vill prova dessa begäranden själv följer du följande steg. Ersätt de exempelvärden som vi använde i den här artikeln med dina egna värden.

1. [Skapa en Azure AD B2C-katalog](tutorial-create-tenant.md). Använd namnet på katalogen i begäranden.
2. [Skapa ett program](tutorial-register-applications.md) för att hämta ett program-ID och en omdirigera URI. Inkludera en inbyggd klient i appen.
3. [Skapa dina användarflöden](user-flow-overview.md) för att få dina användarnamn.
