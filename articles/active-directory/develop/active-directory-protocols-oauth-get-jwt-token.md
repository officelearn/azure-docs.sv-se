---
title: Autentisera med Azure AD och få en JWT-Token med hjälp av OAuth 2.0
description: 'Exempelkod som visar hur du autentiserar med Azure Active Directory med OAuth 2.0 för att få åtkomst till skyddade webbprogram och webb-API: er i din organisation.'
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: cbc86eb6d13034fb3154ed8e9d021064f1d15ece
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266048"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Begär en åtkomsttoken med hjälp av OAuth 2.0 till åtkomst till webb-API: er och program som skyddas av Azure Active Directory

Den här artikeln visar hur du hämtar en JSON Web Token (JWT) att komma åt resurser som skyddas av Azure AD. Det förutsätter att du har en [auktoriseringstoken](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) antingen från en användare har beviljats behörighet eller via en [tjänstens huvudnamn](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Skapa begäran

Använd följande `POST` HTTP-begäran att hämta en JWT för att komma åt ett visst program eller en API som skyddas av Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Begärandehuvud

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
| *Värden:* | https://login.microsoftonline.com |
| *Content-Type:*| Application/x--www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI-parametrar

| Parameter     |                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| klient        | obligatorisk              | Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | obligatorisk              | Programmet med ID som portalen för registrering av ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tilldelats din app.                                                                                                                                                                                                                             |
| _typ av beviljande    | obligatorisk              | Måste vara `authorization_code` för auktoriseringskodsflödet.                                                                                                                                                                                                                                                                                                                                                                            |
| omfång         | obligatorisk              | En blankstegsavgränsad lista med omfattningar. Omfattningar som efterfrågas i det här ben måste vara motsvarar eller en delmängd av omfång i anropet till `/authorize`. Om omfattningar som angetts i den här begäran sträcker sig över flera resursservern, returnerar en token för den resurs som angetts i det första omfånget v2.0-slutpunkten. En mer detaljerad förklaring av omfång finns [behörigheter och samtycke scope](active-directory-v2-scopes.md). |
| Kod          | obligatorisk              | Authorization_code som du har köpt i anropet till `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | obligatorisk              | Samma redirect_uri värde som används för att hämta authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | krävs för web apps | Programhemlighet som du skapade i portalen för registrering av app för din app. Använd inte i en inbyggd app eftersom client_secrets inte lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er som har möjlighet att lagra client_secret på ett säkert sätt på serversidan.  Klienten hemligheter måste vara URL-kodat innan de skickas.                                                                                 |
| code_verifier | valfri              | Den samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE har använts i auktoriseringsbegäran kod bevilja. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>Hantera svaret

Ett lyckat svar för token innehåller en JWT-token och ser ut:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Den begärda åtkomst-token. Appen kan använda den här token för att autentisera till den säkra resursen, till exempel ett webb-API.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är ägar                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Hur länge den åtkomst-token är giltig (i sekunder).                                                                                                                                                                                                                                                                                                                                                                                                       |
| omfång         | Scope som gäller för access_token.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0-uppdateringstoken. Appen kan använda den här token skaffa ytterligare åtkomsttoken när den aktuella åtkomst-token upphör att gälla. Refresh_tokens är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. Mer information finns i den [v2.0 tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `offline_access` omfång begärdes.                                               |
| id_token      | En osignerad JSON Web Token (JWT). Appen kan avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. Mer information om id_tokens finns i den [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md). <br> **Obs:** endast angivna om `openid` omfång begärdes. |



