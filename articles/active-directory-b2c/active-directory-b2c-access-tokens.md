---
title: Begär en åtkomsttoken - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du begär en åtkomsttoken från Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5670d8b3c97cc1f9f6d149e8eadaa60d527e45f5
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683944"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Begär en åtkomsttoken i Azure Active Directory B2C

En *åtkomsttoken* innehåller anspråk som du kan använda i Azure Active Directory (Azure AD) B2C för att identifiera de beviljade behörigheterna för dina API: er. När du anropar resursservern måste en åtkomst-token finnas i HTTP-begäran. En åtkomsttoken är angiven som **access_token** i svar från Azure AD B2C. 

Den här artikeln visar hur du begär en åtkomsttoken för ett webbprogram och webb-API. Mer information om token i Azure AD B2C finns i den [översikt över token i Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Webb-API:ets kedjor (On-Behalf-Of) stöds inte av Azure AD B2C.** – Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, båda skyddade med Azure AD B2C. Det här scenariot är vanligt i klienter som har ett webb-API-serverdel, som i sin tur anropar en annan tjänst. Det här scenariot med länkade webb-API:er kan användas med hjälp av "OAuth 2.0 JWT Bearer Credential grant", även kallat On-Behalf-Of-flöde. Dock är On-Behalf-Of-flödet inte implementerat i Azure AD B2C.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera dig och logga in på ditt program.
- Om du inte redan gjort det, [lägga till ett webb-API-program till din Azure Active Directory B2C-klient](add-web-application.md).

## <a name="scopes"></a>Omfattningar

Scope är ett sätt att hantera behörigheter till skyddade resurser. När en åtkomsttoken begärs klientprogrammet måste ange de önskade behörigheterna i den **omfång** parameter för begäran. Till exempel vill ange den **Omfattningsvärde** av `read` för API som har den **Appidentitets-URI** av `https://contoso.onmicrosoft.com/api`, omfattningen skulle vara `https://contoso.onmicrosoft.com/api/read`.

Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. Du får tillgång till flera behörigheter i samma begäran, kan du lägga till flera poster i en enda **omfång** parameter för begäran, avgränsade med blanksteg.

I följande exempel visas scope avkodas i URL-adresser:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

I följande exempel visas scope i en URL-kodade:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Om du begär fler områden än vad som har beviljats för ditt klientprogram, lyckas anropet om minst en behörighet har beviljats. Den **scp** anspråk i den resulterande åtkomsttoken fylls i med de behörigheter som har beviljats. OpenID Connect-standarden specificerar flera särskilda omfångsvärden. Följande omfattningar representerar behörighet att komma åt användarens profil:

- **openid** -begär en ID-token.
- **offline_access** -begär en uppdatering token med hjälp av [Auth kod flödar](active-directory-b2c-reference-oauth-code.md).

Om den **response_type** parameter i en `/authorize` begäran innehåller `token`, **omfång** parameter måste innehålla minst en resurs omfång än `openid` och `offline_access`som kommer att beviljas. I annat fall den `/authorize` begärande misslyckas.

## <a name="request-a-token"></a>Begära en token

Om du vill begära en åtkomsttoken, behöver du en auktoriseringskod. Nedan visas ett exempel på en begäran om att den `/authorize` slutpunkt för en auktoriseringskod. Anpassade domäner stöds inte för användning med åtkomsttoken. Använd din klient name.onmicrosoft.com domän i fråge-URL.

Ersätt värdena i exemplet nedan:

- `<tenant-name>` – Namnet på din Azure AD B2C-klient.
- `<policy-name>` – Namnet på ditt anpassade principer eller användarnas flöde.
- `<application-ID>` -Program-ID för webbprogram som du har registrerat för att stödja användarflödet.
- `<redirect-uri>` – **Omdirigerings-URI** som du angav när du registrerade klientprogrammet.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

Svaret med Auktoriseringskoden bör likna det här exemplet:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

När du har fått har Auktoriseringskoden, kan du använda den för att begära en åtkomst-token:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Du bör se något som liknar följande svar:

```
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

När du använder https://jwt.ms om du vill kontrollera den åtkomst-token som returnerades, bör du se något som liknar följande exempel:

```
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

- Läs om hur du [konfigurera token i Azure AD B2C](configure-tokens.md)
