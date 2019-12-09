---
title: 'Hämta token i daemon-appar som anropar webb-API: er – Microsoft Identity Platform | Azure'
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0588e20467701512da6542da0d87fca786dcc793
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920301"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app som anropar webb-API: er – hämta en token

När det konfidentiella klient programmet har skapats kan du hämta en token för appen genom att anropa ``AcquireTokenForClient``, skicka omfånget och framtvinga eller inte uppdatera token.

## <a name="scopes-to-request"></a>Omfattningar som ska begäras

Omfånget för att begära ett flöde för klientautentiseringsuppgifter är namnet på resursen följt av `/.default`. Den här texten gör att Azure AD kan använda **behörigheter på program nivå** som har deklarerats statiskt under program registreringen. Som tidigare sett måste dessa API-behörigheter beviljas av en klient administratör

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I MSAL python skulle konfigurations filen se ut som följande kodfragment:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Exempel på Azure AD-resurser (v 1.0)

Det omfång som används för klientens autentiseringsuppgifter ska alltid vara resourceId + "/.default"

> [!IMPORTANT]
> För MSAL som frågar en åtkomsttoken för en resurs som accepterar en v 1.0-åtkomsttoken parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID.
> Om t. ex. Azure SQL ( **https://database.windows.net** )-resursen förväntar sig en mål grupp som slutar med ett snedstreck (för Azure SQL: `https://database.windows.net/` ) måste du begära ett omfång för `https://database.windows.net//.default` (Observera det dubbla snedstrecket). Se även MSAL.NET problem [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-auth-fel.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient-API

Om du vill hämta en token för appen använder du `AcquireTokenForClient` eller motsvarande beroende på plattformarna.

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

Detta är ett utdrag från [MSAL java-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protokoll

Om du ännu inte har ett bibliotek för ditt språk väljer du att använda protokollet direkt:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: begäran om åtkomsttoken med en delad hemlighet

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: åtkomsttoken för begäran med ett certifikat

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

Mer information finns i protokoll dokumentationen: [Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter Flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache för program-token

I MSAL.NET använder `AcquireTokenForClient` token **cache för program** (alla andra AcquireTokenXX-metoder använder user token cache) anropar inte `AcquireTokenSilent` innan du anropar `AcquireTokenForClient` som `AcquireTokenSilent` **använder token** cache. `AcquireTokenForClient` kontrollerar cacheminnet för **program** -token och uppdaterar det.

## <a name="troubleshooting"></a>Felsöka

### <a name="did-you-use-the-resourcedefault-scope"></a>Har du använt Resource/. default-omfånget?

Om du får ett fel meddelande om att du har använt ett ogiltigt omfång, använder du förmodligen inte `resource/.default` omfattningen.

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

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app – anropar ett webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app – anropar ett webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app – anropar ett webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
