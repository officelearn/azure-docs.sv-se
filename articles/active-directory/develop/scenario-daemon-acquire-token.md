---
title: 'Daemon-appen anropar webb-API: er (hämtar token för appen) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er (hämtar token)'
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
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562350"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app som anropar webb-API: er – hämta en token

När det konfidentiella klient programmet har skapats kan du hämta en token för appen genom att ``AcquireTokenForClient``anropa, skicka omfattningen och framtvinga eller inte uppdatera token.

## <a name="scopes-to-request"></a>Omfattningar som ska begäras

Omfånget för att begära ett flöde för klientautentiseringsuppgifter är namnet på resursen följt av `/.default`. Den här texten gör att Azure AD kan använda **behörigheter på program nivå** som har deklarerats statiskt under program registreringen. Som tidigare sett måste dessa API-behörigheter beviljas av en klient administratör

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

I MSAL. Python, konfigurations filen ser ut som följande kodfragment:

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

Det omfång som används för klientens autentiseringsuppgifter ska alltid vara resourceId + "/.default"

### <a name="case-of-azure-ad-v10-resources"></a>Exempel på Azure AD-resurser (v 1.0)

> [!IMPORTANT]
> För MSAL (Microsoft Identity Platform-slutpunkt) som efterfrågar en åtkomsttoken för en resurs som accepterar en v 1.0-åtkomsttoken, parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID.
> Om till exempel Azure SQL ( **https://database.windows.net** ) förväntar sig en mål grupp som slutar med ett snedstreck (för Azure SQL: `https://database.windows.net/` ), måste du begära ett omfånget `https://database.windows.net//.default` (Observera det dubbla snedstrecket). Se även MSAL.NET problem [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-auth-haveriet.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient-API

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

#### <a name="application-token-cache"></a>Cache för program-token

`AcquireTokenForClient` I MSAL.net använder **Application token cache** (alla andra AcquireTokenXX-metoder använder token-cachen) anropa `AcquireTokenSilent` inte innan anrop `AcquireTokenForClient` som `AcquireTokenSilent` använder **användartoken** . `AcquireTokenForClient`kontrollerar cacheminnet för **program** -token och uppdaterar det.

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

### <a name="protocol"></a>Protocol

Om du ännu inte har ett bibliotek för ditt språk väljer du att använda protokollet direkt:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Åtkomstbegäran med en delad hemlighet

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Begäran om åtkomsttoken med ett certifikat

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

### <a name="learn-more-about-the-protocol"></a>Läs mer om protokollet

Mer information finns i protokoll dokumentationen: [Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter flöde](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="did-you-use-the-resourcedefault-scope"></a>Har du använt Resource/. default-omfånget?

Om du får ett fel meddelande om att du har använt ett ogiltigt omfång använder `resource/.default` du antagligen inte omfattningen.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Glömde du att ge administratörs tillåtelse? Daemon-appar behöver!

Om du får ett fel meddelande när du anropar API: t **för otillräckliga behörigheter för att slutföra åtgärden**, måste klient organisationens administratör bevilja behörighet till programmet. Se steg 6 i registrera klient programmet ovan.
Du ser vanligt vis fel som följande fel Beskrivning:

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
> [Daemon-app – anropar ett webb-API](scenario-daemon-call-api.md)