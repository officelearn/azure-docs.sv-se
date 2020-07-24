---
title: 'Hämta en token för ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er som behöver förvärva en token för appen.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026466"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Ett webb-API som anropar webb-API: er: Hämta en token för appen

När du har skapat ett klient program objekt kan du använda det för att hämta en token som du kan använda för att anropa ett webb-API.

## <a name="code-in-the-controller"></a>Kod i kontrollanten

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Här är ett exempel på kod som använder Microsoft. identitet. Web som anropas i API-styrenhetens åtgärder. Den anropar ett underordnat API med namnet *ToDoList*. Om du vill hämta en token för att anropa det underordnade API: t ska du injicera `ITokenAcquisition` tjänsten med beroende inmatning i Controller-konstruktorn (eller din sidlayout om du använder blixt), och du använder den i styrenhets åtgärderna, hämtar en token för användaren ( `GetAccessTokenForUserAsync` ) eller för själva programmet ( `GetAccessTokenForAppAsync` ) i händelse av ett bakgrunds scenario.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Mer information om- `callTodoListService` metoden finns i [ett webb-API som anropar webb-API: er: anropa ett API](scenario-web-api-call-api-call-api.md).

# <a name="java"></a>[Java](#tab/java)
Här är ett exempel på kod som anropas i API-styrenhetens åtgärder. Den anropar underordnad API-Microsoft Graph.

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

Ett python-webb-API måste använda vissa mellanprogram för att verifiera Bearer-token som tas emot från klienten. Webb-API: et kan sedan hämta åtkomsttoken för underordnad API med hjälp av MSAL python Library genom att anropa [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metoden. Ett exempel som demonstrerar det här flödet med MSAL python är ännu inte tillgängligt.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er: anropa ett API](scenario-web-api-call-api-call-api.md)
