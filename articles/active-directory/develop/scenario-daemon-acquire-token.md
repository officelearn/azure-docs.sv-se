---
title: Skaffa token för att anropa ett webb-API (daemonapp) – Microsoft identity platform | Azure
description: Lär dig hur du skapar en daemon app som anropar webb-API:er (hämta token)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f1010949a72f95ef2836c43666e6cea9281e04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262651"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon app som anropar webb-API: er - skaffa en token

När du har skapat ett konfidentiellt klientprogram kan du hämta `AcquireTokenForClient`en token för appen genom att anropa, skicka scopet och eventuellt tvinga fram en uppdatering av token.

## <a name="scopes-to-request"></a>Scope att begära

Scopet för att begära ett klientautentiseringsflöde är namnet `/.default`på resursen följt av . Den här notationen talar om för Azure Active Directory (Azure AD) att använda *behörigheterna på programnivå som deklarerats* statiskt under programregistreringen. Dessa API-behörigheter måste också beviljas av en klientadministratör.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

I MSAL Python ser konfigurationsfilen ut så här kodavsnittet:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD-resurser (v1.0)

Det scope som används för klientautentiseringsuppgifter ska `/.default`alltid vara resurs-ID följt av .

> [!IMPORTANT]
> När MSAL begär en åtkomsttoken för en resurs som accepterar en version 1.0-åtkomsttoken tolkar Azure AD önskad målgrupp från det begärda omfånget genom att ta allt före det sista snedstrecket och använda den som resursidentifierare.
> Så om, som Azure SQL Database (**https:\//database.windows.net**), resursen förväntar sig en `https://database.windows.net/`publik som slutar med ett `https://database.windows.net//.default`snedstreck (för Azure SQL Database, ), måste du begära ett omfång av . (Notera det dubbla snedstrecket.) Se även MSAL.NET problem [#747: Resurs url s avslutande snedstreck utelämnas, vilket orsakade SQL Auth misslyckande](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>Skaffa Api för att förvärvaTokenForClient

Om du vill hämta en token `AcquireTokenForClient` för appen använder du eller motsvarande, beroende på plattform.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Den här koden extraheras från [MSAL Java dev-exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protokoll

Om du ännu inte har ett bibliotek för det språk du valt kan du använda protokollet direkt:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Första fallet: Få tillgång till tokenbegäran med hjälp av en delad hemlighet

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Andra fallet: Få tillgång till tokenbegäran med hjälp av ett certifikat

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Mer information finns i protokolldokumentationen: [Microsoft identity platform och OAuth 2.0-klientautentiseringsflödet](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache för programtoken

I MSAL.NET `AcquireTokenForClient` använder programtokens cacheminnet. (Alla andra AcquireToken*XX-metoder* använder användartokencachen.) Ring `AcquireTokenSilent` inte innan du `AcquireTokenForClient`ringer `AcquireTokenSilent` , eftersom använder *användaren* token cache. `AcquireTokenForClient`kontrollerar *application* själva programtoken-cachen och uppdaterar den.

## <a name="troubleshooting"></a>Felsökning

### <a name="did-you-use-the-resourcedefault-scope"></a>Använde du resurs/.standardomfattning?

Om du får ett felmeddelande om att du har använt ett `resource/.default` ogiltigt scope har du förmodligen inte använt scopet.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Har du glömt att ge admin samtycke? Daemon apps behöver det!

Om du får **otillräckliga privilegier för att slutföra åtgärdsfelet** när du anropar API:et måste klientadministratören bevilja behörigheter till programmet. Se steg 6 i Registrera klientappen ovan.
Du ser vanligtvis ett fel som ser ut så här:

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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - ringa en webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - ringa en webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - ringa en webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
