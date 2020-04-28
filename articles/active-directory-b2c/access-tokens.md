---
title: Begär en åtkomsttoken – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du begär en åtkomsttoken från Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259778"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Begär en åtkomsttoken i Azure Active Directory B2C

En *åtkomsttoken* innehåller anspråk som du kan använda i Azure Active Directory B2C (Azure AD B2C) för att identifiera de beviljade behörigheterna för dina API: er. När du anropar en resurs Server måste en åtkomsttoken finnas i HTTP-begäran. En åtkomsttoken betecknas som **access_token** i svaren från Azure AD B2C.

Den här artikeln visar hur du begär en åtkomsttoken för ett webb program och webb-API. Mer information om tokens i Azure AD B2C finns i [Översikt över tokens i Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Webb-API-kedjor (på-plats-av) stöds inte av Azure AD B2C.** – Många arkitekturer innehåller ett webb-API som måste anropa ett annat underordnat webb-API, som båda skyddas av Azure AD B2C. Det här scenariot är vanligt i klienter som har en server del för webb-API, som i sin tur anropar en annan tjänst. Det här länkade webb-API-scenariot kan stödjas med hjälp av OAuth 2,0 JWT Bearer Credential Grant, på annat sätt kallas för flöde på uppdrag. Det finns dock för närvarande inte implementerat flöde på uppdrag i Azure AD B2C.

## <a name="prerequisites"></a>Krav

- [Skapa ett användar flöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in i programmet.
- Om du inte redan har gjort det [lägger du till ett webb-API-program till din Azure Active Directory B2C-klient](add-web-application.md).

## <a name="scopes"></a>Omfattningar

Med hjälp av omfång kan du hantera behörigheter för skyddade resurser. När en åtkomsttoken begärs måste klient programmet ange önskade behörigheter i **scope** -parametern för begäran. Om du till exempel vill ange **omfattning svärdet** för `read` för API: t som har **app-ID-URI: n** `https://contoso.onmicrosoft.com/api`, blir `https://contoso.onmicrosoft.com/api/read`omfattningen.

Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. Om du vill förvärva flera behörigheter i samma förfrågan kan du lägga till flera poster i den enda **omfattnings** parametern för begäran, avgränsade med blank steg.

I följande exempel visas omfattningarna som avkodats i en URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

I följande exempel visas omfång som är kodade i en URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Om du begär fler omfattningar än vad som beviljas för ditt klient program, lyckas anropet om minst en behörighet beviljas. **SCP** -anspråket i den resulterande åtkomsttoken fylls med endast de behörigheter som har beviljats. OpenID Connect standard anger flera särskilda omfångs värden. Följande omfång representerar behörigheten att komma åt användarens profil:

- **OpenID** -begär en ID-token.
- **offline_access** -begär en uppdateringstoken med hjälp av [auth Code](authorization-code-flow.md)flows.

Om parametern **response_type** i en `/authorize` begäran inkluderar `token`måste **omfattnings** parametern innehålla minst en annan resurs omfattning än `openid` och `offline_access` som ska beviljas. Annars Miss lyckas `/authorize` begäran.

## <a name="request-a-token"></a>Begär en token

Om du vill begära en åtkomsttoken behöver du en auktoriseringskod. Nedan visas ett exempel på en begäran till `/authorize` slut punkten för en auktoriseringskod. Anpassade domäner stöds inte för användning med åtkomsttoken. Använd din tenant-name.onmicrosoft.com-domän i fråge-URL: en.

I följande exempel ersätter du följande värden:

- `<tenant-name>`– Namnet på din Azure AD B2C klient.
- `<policy-name>`– Namnet på din anpassade princip eller ditt användar flöde.
- `<application-ID>`– Program identifieraren för det webb program som du har registrerat för att stödja användar flödet.
- `<redirect-uri>`– Den **omdirigerings-URI** som du angav när du registrerade klient programmet.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Svaret med auktoriseringskod bör likna följande exempel:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

När du har tagit emot auktoriseringskod kan du använda den för att begära en åtkomsttoken:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Du bör se något som liknar följande svar:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

När du https://jwt.ms använder för att kontrol lera den åtkomsttoken som returnerades, bör du se något som liknar följande exempel:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar tokens i Azure AD B2C](configure-tokens.md)
