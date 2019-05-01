---
title: Auktoriseringskodflödet - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du skapar webbappar med hjälp av Azure AD B2C och OpenID Connect-autentiseringsprotokollet.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 72111bc54691b340bcb0d8af8ef52bf0bd103a21
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703592"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0-auktoriseringskodflödet i Azure Active Directory B2C

Du kan använda OAuth 2.0-auktoriseringskod i appar som installerats på en enhet för att få åtkomst till skyddade resurser, till exempel webb API: er. Genom att använda den Azure Active Directory B2C (Azure AD B2C) implementering av OAuth 2.0, du kan lägga till registrering, inloggning och andra Identitetshantering uppgifter till dina appar och program. Den här artikeln är språkoberoende. I artikeln beskrivs hur du skickar och tar emot HTTP-meddelanden utan att använda alla bibliotek med öppen källkod.

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). Du kan använda för autentisering och auktorisering i de flesta [programtyper](active-directory-b2c-apps.md), inklusive webb- och internt installerade program. Du kan använda OAuth 2.0-auktoriseringskodflödet på ett säkert sätt hämta åtkomsttoken och uppdateringstoken för dina program, som kan användas för att komma åt resurser som skyddas av en [auktoriseringsservern](active-directory-b2c-reference-protocols.md).  Uppdateringstoken gör att klienten kan få nya åtkomst (och uppdatera) token när åtkomsttoken upphör att gälla, vanligtvis efter en timme.

Den här artikeln fokuserar på de **offentliga klienter** OAuth 2.0-auktoriseringskodflödet. En offentlig klient är klientprogram som inte är betrott för att upprätthålla integriteten hos ett hemligt lösenord på ett säkert sätt. Detta inkluderar mobilappar, program och i stort sett alla program som körs på en enhet och behöver få åtkomst-token. 

> [!NOTE]
> Lägg till Identitetshantering till en webbapp med hjälp av Azure AD B2C genom att använda [OpenID Connect](active-directory-b2c-reference-oidc.md) i stället för OAuth 2.0.

Azure AD B2C utökar standard OAuth 2.0 flöden för att göra mer än enkel autentisering och auktorisering. Det introducerar den [användaren flow parametern](active-directory-b2c-reference-policies.md). Med användarflöden, du kan använda OAuth 2.0 att lägga till användarupplevelser i ditt program, till exempel registrering, inloggning och profilhantering. Identitetsleverantörer som använder OAuth 2.0-protokollet är bland annat [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [ Google](active-directory-b2c-setup-goog-app.md), och [LinkedIn](active-directory-b2c-setup-li-app.md).

I exemplet HTTP-begäranden i den här artikeln använder vi vår exempel Azure AD B2C-katalog **fabrikamb2c.onmicrosoft.com**. Vi kan också använda våra exempel och flöden. Du kan också försöka begäranden med hjälp av dessa värden eller ersätta dem med dina egna värden.
Lär dig hur du [hämta din egen Azure AD B2C-katalog, program och användare flöden](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Hämta en auktoriseringskod
Auktoriseringskodsflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. Det här är den interaktiva delen av flödet, där användaren vidtar åtgärder. I den här begäran anger klienten i den `scope` parametern de behörigheter som krävs för att hämta från användaren. I den `p` parametern indikerar användarflödet att köra. I följande tre exempel (med radbrytningar för läsbarhet) varje används en annan användare-flöde.

### <a name="use-a-sign-in-user-flow"></a>Använd en inloggning användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Använd en registrerings användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Använda en Redigera profil användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| client_id |Krävs |Program-ID som tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Svarstypen som måste innehålla `code` för auktoriseringskodsflödet. |
| redirect_uri |Krävs |Omdirigerings-URI för din app, där autentiseringssvaren skickas och tas emot av din app. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodade. |
| omfång |Krävs |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure Active Directory (Azure AD) båda av de behörigheter som tas emot. Med klient-ID som omfattningen visar att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, som representeras av samma klient-ID.  Den `offline_access` omfång visar att din app behöver en uppdateringstoken för långlivade åtkomst till resurser. Du kan också använda den `openid` omfattning att begära en ID-token från Azure AD B2C. |
| response_mode |Rekommenderas |Den metod som används för att skicka resulterande Auktoriseringskoden tillbaka till din app. Det kan vara `query`, `form_post`, eller `fragment`. |
| state |Rekommenderas |Ett värde i begäran som kan vara en sträng med innehåll som du vill använda. Vanligtvis är används ett slumpmässigt genererat unikt värde till att förhindra attacker med förfalskning av begäran. Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Exempelvis kan användaren var på sidan eller det användarflöde som kördes. |
| p |Krävs |Användarflödet som körs. Det är namnet på ett användarflöde som skapas i din Azure AD B2C-katalog. Namnvärdet för användaren flow ska inledas med **b2c\_1\_**. Läs mer om användarflöden i [Azure AD B2C-användarflöden](active-directory-b2c-reference-policies.md). |
| fråga |Valfri |Typ av interaktion från användaren som krävs. Det enda giltiga värdet är för närvarande `login`, vilket Tvingar användaren att ange sina autentiseringsuppgifter i begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra den användarflödet arbetsflöde. Detta kan handla om användaren skriver sitt användarnamn och lösenord, logga in med en sociala identitet, registrera dig för katalogen, eller en annan siffra steg. Användaråtgärder beror på hur användarflödet har definierats.

När du är klar användarflödet Azure AD returnerar ett svar till din app på värdet som du använde för `redirect_uri`. Den använder den metod som beskrivs i den `response_mode` parametern. Svaret är exakt detsamma för alla användare åtgärd scenarion, oberoende av det användarflöde som kördes.

Ett lyckat svar som använder `response_mode=query` ser ut så här:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beskrivning |
| --- | --- |
| Kod |Auktoriseringskod som appen har begärt. Appen kan använda Auktoriseringskoden för att begära en åtkomsttoken för en målresurs. Auktoriseringskoder är mycket tillfällig. Vanligtvis de går ut efter 10 minuter. |
| state |Se den fullständiga beskrivningen i tabellen i föregående avsnitt. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |

Felsvar kan också skickas till omdirigeringen-URI så att appen kan hantera dem på rätt sätt:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typerna av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i tabellen ovan. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |

## <a name="2-get-a-token"></a>2. Hämta en token
Nu när du har skaffat en auktoriseringskod, kan du lösa in den `code` för en token för den avsedda resursen genom att skicka en POST-begäran till den `/token` slutpunkt. I Azure AD B2C är den enda resurs som du kan begära en token för din Apps egen backend-webb-API. Konventionen som används för att begära en token till dig själv är att använda appens klient-ID som omfång:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Krävs |Det användarflöde som användes för att hämta Auktoriseringskoden. Du kan inte använda en annan användarflödet i den här begäran. Observera att du lägger till den här parametern till den *frågesträng*, inte i själva INLÄGGET. |
| client_id |Krävs |Program-ID som tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| _typ av beviljande |Krävs |Typ av beviljande. För auktoriseringskodflödet beviljandetypen måste vara `authorization_code`. |
| omfång |Rekommenderas |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda av de behörigheter som tas emot. Med klient-ID som omfattningen visar att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, som representeras av samma klient-ID.  Den `offline_access` omfång visar att din app behöver en uppdateringstoken för långlivade åtkomst till resurser.  Du kan också använda den `openid` omfattning att begära en ID-token från Azure AD B2C. |
| Kod |Krävs |Auktoriseringskod som du har köpt i den första delen i flödet. |
| redirect_uri |Krävs |Omdirigerings-URI för programmet som du fick Auktoriseringskoden. |

Ett lyckat svar för token som ser ut så här:

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
| token_type |Typ tokenu-värde. Den enda typen som har stöd för Azure AD är ägar. |
| access_token |Den signerade JSON Web Token (JWT) som du har begärt. |
| omfång |Scope som token är giltig för. Du kan också använda omfång cache-tokens för senare användning. |
| expires_in |Hur lång tid som token är giltig (i sekunder). |
| refresh_token |OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdateringstoken är långlivade. Du kan använda dem om du vill få åtkomst till resurser för längre tid. Mer information finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Felsvar ut så här:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typerna av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |

## <a name="3-use-the-token"></a>3. Använda token
Nu när du har har skaffat en åtkomsttoken, du kan använda token i begäranden till ditt backend-webb-API: er genom att inkludera den i den `Authorization` rubrik:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Uppdatera token
Åtkomsttoken och ID-token är tillfällig. När de går ut måste du uppdatera dem om du vill fortsätta att få åtkomst till resurser. Gör detta genom att skicka in en annan POST-begäran till den `/token` slutpunkt. Den här gången den `refresh_token` i stället för den `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Krävs |Det användarflöde som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda en annan användarflödet i den här begäran. Observera att du lägger till den här parametern till den *frågesträng*, inte i själva INLÄGGET. |
| client_id |Krävs |Program-ID som tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| client_secret |Krävs |Client_secret som är kopplad till din client_id i den [Azure-portalen](https://portal.azure.com). |
| _typ av beviljande |Krävs |Typ av beviljande. För den här delen i auktoriseringskodsflödet beviljandetypen måste vara `refresh_token`. |
| omfång |Rekommenderas |En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda av de behörigheter som tas emot. Med klient-ID som omfattningen visar att din app behöver en åtkomsttoken som kan användas mot din egen tjänst eller webb-API, som representeras av samma klient-ID.  Den `offline_access` omfång anger att din app behöver en uppdateringstoken för långlivade åtkomst till resurser.  Du kan också använda den `openid` omfattning att begära en ID-token från Azure AD B2C. |
| redirect_uri |Valfri |Omdirigerings-URI för programmet som du fick Auktoriseringskoden. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du har köpt i den andra delen i flödet. |

Ett lyckat svar för token som ser ut så här:

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
| token_type |Typ tokenu-värde. Den enda typen som har stöd för Azure AD är ägar. |
| access_token |Den signerade JWT som du har begärt. |
| omfång |Scope som token är giltig för. Du kan också använda omfång till token i cacheminnet för senare användning. |
| expires_in |Hur lång tid som token är giltig (i sekunder). |
| refresh_token |OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. Mer information finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Felsvar ut så här:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Använd din egen Azure AD B2C-katalog
Utför följande steg om du vill prova de här begärandena själv. Ersätt exempelvärden vi använde i den här artikeln med dina egna värden.

1. [Skapa en Azure AD B2C-katalog](active-directory-b2c-get-started.md). Använd namnet på din katalog i begäranden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) att hämta ett program-ID och omdirigerings-URI. Inkludera en intern klient i din app.
3. [Skapa din användarflöden](active-directory-b2c-reference-policies.md) att hämta din användare namn för flödet.

