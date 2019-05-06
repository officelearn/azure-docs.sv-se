---
title: 'Daemon för appen anropa webb-API: er (hämta token för appen) – Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app att anrop webb-API: er (hämta token)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075377"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app som anropar webb-API: er – hämta en token

När konfidentiellt klientprogrammet har skapats kan du hämta en token för appen genom att anropa ``AcquireTokenForClient``, skicka omfattning, och framtvingar eller inte en uppdatering av token.

## <a name="scopes-to-request"></a>Sökomfång för att begära

Omfånget till begäran för en autentiseringsuppgift klientflödet är namnet på resursen följt av `/.default`. Den här notationen talar om för Azure AD för att använda den **program serverbehörigheter** deklarerats statiskt vid registrering för programmet. Dessutom som visas tidigare måste dessa API-behörigheter beviljas av Innehavaradministratör

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

I MSAL. Python, konfigurationsfilen ser ut som följande kodavsnitt:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Alla

Med omfattningen som används för klientens autentiseringsuppgifter ska alltid vara resourceId + ”/ .default”

### <a name="case-of-v10-resources"></a>Om v1.0 resurser

> [!IMPORTANT]
> För MSAL (v2.0-slutpunkt) frågar en åtkomsttoken för en resurs som tar emot en token för åtkomst av v1.0, tolkar Azure AD målgrupp från det begärda omfånget genom att ta allt innan det senaste snedstrecket och använda det som resursidentifieraren.
> Därför if, som Azure SQL (**https://database.windows.net**) resursen förväntar sig en målgrupp som slutar med ett snedstreck (för Azure SQL: `https://database.windows.net/`), måste du begära en omfattning `https://database.windows.net//.default` (Observera dubbla snedstreck). Se även MSAL.NET problemet [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resurs-url avslutande snedstreck utelämnas som orsakade fel för sql-autentisering.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Programmet token-cache

I MSAL.NET, `AcquireTokenForClient` använder den **token cacheminne** (alla andra AcquireTokenXX-metoder använder token användarcachen) inte anropa `AcquireTokenSilent` innan du anropar `AcquireTokenForClient` som `AcquireTokenSilent` använder **användaren** token cache. `AcquireTokenForClient` kontrollerar den **program** token cachelagra själva och uppdaterar den.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protokoll

Om du inte har ännu ett bibliotek för på valfritt språk du vill använda protokollet direkt:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fall: Begäran om åtkomsttoken med en delad hemlighet

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: Begäran om åtkomsttoken med ett certifikat

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>Mer information om protokollet

Mer information finns i dokumentationen för protokollet: [Azure Active Directory v2.0- och OAuth 2.0-klient autentiseringsuppgifter flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="did-you-use-the-resourcedefault-scope"></a>Använde du resursen/.default omfånget?

Om du får ett felmeddelande om att du har använt en ogiltig omfattning, du förmodligen inte använda den `resource/.default` omfång.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Har du glömt att ange administratörens godkännande? Daemon-appar måste det!

Om du får ett felmeddelande när du anropar API: et **inte tillräcklig behörighet för att slutföra åtgärden**, klientadministratören måste tilldela behörigheter till programmet. Se steg 6 i registrera klientappen ovan.
Vanligtvis visas och fel som beskrivning av följande fel:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon för app - anropa ett webb-API](scenario-daemon-call-api.md)