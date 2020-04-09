---
title: 'Hämta en token för ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett webb-API som anropar webb-API:er som kräver att du hämtar en token för appen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885148"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Ett webb-API som anropar webb-API:er: Skaffa en token för appen

När du har skapat ett klientprogramobjekt använder du det för att hämta en token som du kan använda för att anropa ett webb-API.

## <a name="code-in-the-controller"></a>Kod i styrenheten

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Här är ett exempel på kod som anropas i åtgärderna för API-styrenheter. Den anropar ett nedströms-API med namnet *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`liknar scenariot i [Ett webb-API som anropar webb-API:er: Appkonfiguration](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`instansierar `IConfidentialClientApplication` med en cache som innehåller information för endast ett konto. Kontot tillhandahålls med `GetAccountIdentifier` metoden.

Metoden `GetAccountIdentifier` använder anspråk som är associerade med identiteten på den användare som webb-API:et tog emot JSON Web Token (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Här är ett exempel på kod som anropas i åtgärderna för API-styrenheter. Det anropar nedströms API - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Ett Python-webb-API måste använda vissa mellanprogram för att validera innehavartoken som tagits emot från klienten. Webb-API:et kan sedan hämta åtkomsttoken för nedströms-API med MSAL Python-biblioteket genom att anropa [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metoden. Ett exempel som visar det här flödet med MSAL Python är ännu inte tillgängligt.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API:er: Anropa ett API](scenario-web-api-call-api-call-api.md)
