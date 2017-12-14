---
title: "Auktoriseringskodflödet - Azure AD B2C | Microsoft Docs"
description: "Lär dig mer om att skapa webbprogram med hjälp av autentiseringsprotokollet Azure AD B2C och OpenID Connect."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: 99a292c6be66016264e528525a5920667207b605
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: OAuth 2.0-auktoriseringskodflödet
Du kan använda OAuth 2.0 auktorisering kod bevilja i appar som installeras på en enhet för att få åtkomst till skyddade resurser, till exempel web API: er. Med hjälp av Azure Active Directory B2C (Azure AD B2C) implementering av OAuth 2.0, kan du lägga till registrering, inloggning och andra Identitetshantering uppgifter till dina appar och program. Den här artikeln är språkoberoende. I artikeln beskrivs hur du skickar och tar emot HTTP-meddelanden utan att använda alla bibliotek med öppen källkod.

<!-- TODO: Need link to libraries -->

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnittet 4.1 i OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Du kan använda för autentisering och auktorisering i de flesta apptyper, inklusive [webbappar](active-directory-b2c-apps.md#web-apps) och [internt installerade appar](active-directory-b2c-apps.md#mobile-and-native-apps). Du kan använda OAuth 2.0-auktoriseringskodflödet att på ett säkert sätt hämta *åtkomst till token* för dina appar som kan användas för att komma åt resurser som skyddas av en [auktorisering server](active-directory-b2c-reference-protocols.md#the-basics).

Den här artikeln fokuserar på de **offentliga klienter** OAuth 2.0-auktoriseringskodflödet. En offentlig klient är alla klientprogram som inte är betrott på ett säkert sätt Underhåll integriteten hos ett hemligt lösenord. Detta inkluderar mobilappar och skrivbordsappar i stort sett alla program som körs på en enhet och behöver få åtkomst-token. 

> [!NOTE]
> Lägg till Identitetshantering i ett webbprogram med hjälp av Azure AD B2C genom att använda [OpenID Connect](active-directory-b2c-reference-oidc.md) i stället för OAuth 2.0.

Azure AD B2C utökar standard OAuth 2.0 flödar om du vill göra mer än enkel autentisering och auktorisering. Det inför den [parametern](active-directory-b2c-reference-policies.md). Med inbyggda principer, du kan använda OAuth 2.0 att lägga till användarupplevelser i din app som registrering, inloggning och profilhantering. I den här artikeln hur vi du använder OAuth 2.0 och principer för att implementera var och en av dessa upplevelser i dina interna program. Vi också hur du få åtkomst-token för åtkomst till webb-API: er.

I exempel HTTP-begäranden i den här artikeln använder vi våra exempel Azure AD B2C-katalog, **fabrikamb2c.onmicrosoft.com**. Vi använder också våra exempelprogrammet och principer. Du kan också försöka begäranden med hjälp av dessa värden eller ersätta dem med egna värden.
Lär dig hur du [hämta egna Azure AD B2C-katalog, program och principer](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Hämta en auktoriseringskod för
Auktoriseringskodflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. Detta är den interaktiva delen av flödet, där användaren vidtar åtgärder. I den här förfrågan klienten anger i den `scope` parametern de behörigheter som krävs för att hämta från användaren. I den `p` parametern anger principen som ska köras. Följande tre exempel (med radbrytningar för att läsa) varje använda en annan princip.

### <a name="use-a-sign-in-policy"></a>Använda en princip för inloggning
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Använda en princip för registrering
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Använda en princip för Redigera-profil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Krävs |Det program-ID som har tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| response_type |Krävs |Svarstyp som måste innehålla `code` för auktoriseringskodflödet. |
| redirect_uri |Krävs |Omdirigerings-URI för din app, där autentisering svar skickas och tas emot av din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen, förutom att det måste vara URL-kodade. |
| Omfång |Krävs |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure Active Directory (AD Azure) båda de behörigheter som krävs. Med klient-ID som omfattningen visar att din app behöver ett åtkomsttoken som kan användas med tjänsten eller webb-API, som representeras av samma klient-ID.  Den `offline_access` scope visar att din app behöver en uppdateringstoken för långlivade åtkomst till resurser. Du kan också använda den `openid` scope för att begära en ID-token från Azure AD B2C. |
| response_mode |Rekommenderas |Den metod som används för att skicka den resulterande Auktoriseringskoden tillbaka till din app. Det kan vara `query`, `form_post`, eller `fragment`. |
| state |Rekommenderas |Ett värde som ingår i denna begäran som returneras i token svaret. Det kan vara en sträng med innehåll som du vill använda. Vanligtvis används ett slumpmässigt genererat unikt värde för att förhindra attacker med förfalskning av begäran. Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Till exempel sidan användaren utfördes eller den princip som kördes. |
| P |Krävs |Den princip som körs. Det är namnet på en princip som har skapats i Azure AD B2C-katalogen. Princip för namn-värde ska inledas med **b2c\_1\_**. Mer information om principer finns [Azure AD B2C inbyggda principer](active-directory-b2c-reference-policies.md). |
| kommandotolk |Valfri |Typ av användarinteraktion som krävs. Det enda giltiga värdet är för närvarande `login`, som tvingar användaren att ange sina autentiseringsuppgifter på begäran. Enkel inloggning börjar inte gälla. |

Nu uppmanas användaren att slutföra arbetsflödet för den principen. Detta kan handla om användaren att ange sina användarnamn och lösenord, logga in med en sociala identitet registrerar sig för katalogen, eller en annan siffra steg. Användaråtgärder beror på hur principen har definierats.

När användaren uppfyller principen, Azure AD tillbaka ett svar på din app på värdet som du använde för `redirect_uri`. Den använder metoden som anges i den `response_mode` parameter. Svaret är exakt samma för alla användare åtgärd scenarier, oberoende av den princip som har utförts.

Ett lyckat svar som använder `response_mode=query` ser ut så här:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beskrivning |
| --- | --- |
| Koden |Auktoriseringskoden som begärts av appen. Appen kan använda Auktoriseringskoden för att begära en åtkomst-token för en målresurs. Auktoriseringskoder är mycket tillfällig. Vanligtvis de går ut efter 10 minuter. |
| state |Se den fullständiga beskrivningen i tabellen i föregående avsnitt. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |

Felsvar kan också skickas till omdirigerings-URI så att appen kan hantera dem på rätt sätt:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som du kan använda för att klassificera typerna av fel som inträffar. Du kan också använda strängen att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |
| state |Se den fullständiga beskrivningen i föregående tabell. Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i förfrågan och svar är identiska. |

## <a name="2-get-a-token"></a>2. Hämta en token
Nu när du har skaffat ett auktoriseringskod, kan du lösa in den `code` för en token för den avsedda resursen genom att skicka en POST-begäran till den `/token` slutpunkt. I Azure AD B2C är den enda resursen som du kan begära en token för ditt Apps egen backend-webb-API. Konventionen som används för att begära en token till dig själv är att använda appens klient-ID som scope:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| P |Krävs |Den princip som användes för att hämta Auktoriseringskoden. Du kan inte använda en annan princip i den här förfrågan. Observera att du lägger till den här parametern till den *frågesträng*, inte i själva POST. |
| client_id |Krävs |Det program-ID som har tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| grant_type |Krävs |Typ av bevilja. För auktoriseringskodflödet, grant-typen måste vara `authorization_code`. |
| Omfång |Rekommenderas |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD båda de behörigheter som krävs. Med klient-ID som omfattningen visar att din app behöver ett åtkomsttoken som kan användas med tjänsten eller webb-API, som representeras av samma klient-ID.  Den `offline_access` scope visar att din app behöver en uppdateringstoken för långlivade åtkomst till resurser.  Du kan också använda den `openid` scope för att begära en ID-token från Azure AD B2C. |
| Koden |Krävs |Auktoriseringskoden som du har införskaffade i den första del av flödet. |
| redirect_uri |Krävs |Omdirigerings-URI för det program som du fick Auktoriseringskoden. |

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
| not_before |Den tid då token betraktas som giltigt epok tidpunkt. |
| token_type |Tokentypen-värde. Den enda typen som har stöd för Azure AD är ägar. |
| access_token |Den signerade JSON-Webbtoken (JWT) som du har begärt. |
| Omfång |Scope som gäller för token. Du kan också använda omfång cache-tokens för senare användning. |
| expires_in |Hur lång tid som denna token är giltig (i sekunder). |
| refresh_token |En token för uppdatering av OAuth 2.0. Appen kan använda denna token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdateringstoken är långlivade. Du kan använda dem för att få åtkomst till resurser för längre tid. Mer information finns i [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). |

Felsvar se ut så här:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som du kan använda för att klassificera typerna av fel som inträffar. Du kan också använda strängen att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

## <a name="3-use-the-token"></a>3. Använda token
Nu när du har har skaffat ett åtkomsttoken, kan du använda token i begäranden till din backend-webb-API: er genom att inkludera den i den `Authorization` huvud:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Uppdatera token
Åtkomst-token och ID-token är tillfällig. När de upphör att gälla måste du uppdatera dem. Om du vill fortsätta att komma åt resurser. Det gör du genom att skicka en annan POST-begäran till den `/token` slutpunkt. Den här gången den `refresh_token` i stället för den `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| P |Krävs |Den princip som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda en annan princip i den här förfrågan. Observera att du lägger till den här parametern till den *frågesträng*, inte i själva POST. |
| client_id |Rekommenderas |Det program-ID som har tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| grant_type |Krävs |Typ av bevilja. För denna del av auktoriseringskodflödet grant-typen måste vara `refresh_token`. |
| Omfång |Rekommenderas |En blankstegsavgränsad lista över scope. Ett enda scope-värde som anger till Azure AD båda de behörigheter som krävs. Med klient-ID som omfattningen visar att din app behöver ett åtkomsttoken som kan användas med tjänsten eller webb-API, som representeras av samma klient-ID.  Den `offline_access` omfång anger att din app måste en uppdateringstoken för långlivade åtkomst till resurser.  Du kan också använda den `openid` scope för att begära en ID-token från Azure AD B2C. |
| redirect_uri |Valfri |Omdirigerings-URI för det program som du fick Auktoriseringskoden. |
| refresh_token |Krävs |Den ursprungliga uppdateringstoken som du har införskaffade i andra del av flödet. |

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
| not_before |Den tid då token betraktas som giltigt epok tidpunkt. |
| token_type |Tokentypen-värde. Den enda typen som har stöd för Azure AD är ägar. |
| access_token |Den signerade JWT som du har begärt. |
| Omfång |Scope som gäller för token. Du kan också använda omfång cache-tokens för senare användning. |
| expires_in |Hur lång tid som denna token är giltig (i sekunder). |
| refresh_token |En token för uppdatering av OAuth 2.0. Appen kan använda denna token för att hämta ytterligare token när den aktuella token upphör att gälla. Uppdatera token är långlivade och kan användas för att få åtkomst till resurser för längre tid. Mer information finns i [Azure AD B2C tokenreferens](active-directory-b2c-reference-tokens.md). |

Felsvar se ut så här:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som du kan använda för att klassificera typer av fel som inträffar. Du kan också använda strängen att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Använda din egen Azure AD B2C-katalog
Utför följande steg om du vill testa dessa begäranden själv. Ersätt exempelvärden som vi använde i den här artikeln med egna värden.

1. [Skapa en Azure AD B2C-katalog](active-directory-b2c-get-started.md). Använd namnet på din katalog i begäranden.
2. [Skapa ett program](active-directory-b2c-app-registration.md) att få ett program-ID och omdirigerings-URI. Inkludera en intern klient i din app.
3. [Skapa dina principer](active-directory-b2c-reference-policies.md) att hämta principens namn.

