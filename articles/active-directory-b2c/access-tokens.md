---
title: Begär en åtkomsttoken – Azure Active Directory B2C | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259778"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Begär en åtkomsttoken i Azure Active Directory B2C

En *åtkomsttoken* innehåller anspråk som du kan använda i Azure Active Directory B2C (Azure AD B2C) för att identifiera de beviljade behörigheterna till dina API:er. När du anropar en resursserver måste en åtkomsttoken finnas i HTTP-begäran. En åtkomsttoken betecknas som **access_token** i svaren från Azure AD B2C.

Den här artikeln visar hur du begär en åtkomsttoken för ett webbprogram och webb-API. Mer information om token i Azure AD B2C finns [i översikten över token i Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Webb-API-kedjor (on-behalf-Of) stöds inte av Azure AD B2C.** - Många arkitekturer innehåller ett webb-API som måste anropa ett annat nedströms webb-API, båda skyddade av Azure AD B2C. Det här scenariot är vanligt i klienter som har en webb-API-serverdel, vilket i sin tur anropar en annan tjänst. Det här kedjade webb-API-scenariot kan stödjas med hjälp av OAuth 2.0 JWT Bearer-autentiseringsbidraget, annars känt som flödet On-Behalf-Of. Flödet on-behalf-of implementeras dock inte i Azure AD B2C.

## <a name="prerequisites"></a>Krav

- [Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in på ditt program.
- Om du inte redan har gjort det [lägger du till ett webb-API-program i din Azure Active Directory B2C-klient](add-web-application.md).

## <a name="scopes"></a>Omfattningar

Scope är ett sätt att hantera behörigheter till skyddade resurser. När en åtkomsttoken begärs måste klientprogrammet ange önskade behörigheter i **scopeparametern** för begäran. Om du till exempel vill `read` ange **scopevärdet** för för API:et som `https://contoso.onmicrosoft.com/api/read`har **App-ID URI** för `https://contoso.onmicrosoft.com/api`, skulle omfånget vara .

Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. Om du vill hämta flera behörigheter i samma begäran kan du lägga till flera poster i parametern för det enda **scopet** för begäran, avgränsade med blanksteg.

I följande exempel visas scope som avkodats i en URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

I följande exempel visas scope som kodats i en URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Om du begär fler scope än vad som beviljas för klientprogrammet lyckas anropet om minst en behörighet beviljas. **Scp-anspråket** i den resulterande åtkomsttoken fylls i med endast de behörigheter som har beviljats. OpenID Connect-standarden anger flera särskilda scopevärden. Följande scope representerar behörigheten att komma åt användarens profil:

- **openid** - Begär en ID-token.
- **offline_access** - Begär en uppdateringstoken med hjälp av [Auth-kodflöden](authorization-code-flow.md).

Om **parametern response_type** i `/authorize` en `token`begäran innehåller måste **scopeparametern** innehålla `openid` minst `offline_access` ett annat resursomfattning än och som kommer att beviljas. Annars misslyckas `/authorize` begäran.

## <a name="request-a-token"></a>Begär en token

Om du vill begära en åtkomsttoken behöver du en auktoriseringskod. Nedan följer ett exempel på `/authorize` en begäran till slutpunkten för en auktoriseringskod. Anpassade domäner stöds inte för användning med åtkomsttoken. Använd din tenant-name.onmicrosoft.com domän i url:en för begäran.

I följande exempel ersätter du dessa värden:

- `<tenant-name>`- Namnet på din Azure AD B2C-klient.
- `<policy-name>`- Namnet på din anpassade princip eller användarflöde.
- `<application-ID>`- Programidentifieraren för webbprogrammet som du registrerade för att stödja användarflödet.
- `<redirect-uri>`- **Omdirigera URI** som du angav när du registrerade klientprogrammet.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Svaret med auktoriseringskoden bör likna det här exemplet:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

När du har tagit emot auktoriseringskoden kan du använda den för att begära en åtkomsttoken:

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

När https://jwt.ms du använder för att undersöka åtkomsttoken som returnerades bör du se något som liknar följande exempel:

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

- Lär dig mer om hur du [konfigurerar token i Azure AD B2C](configure-tokens.md)
