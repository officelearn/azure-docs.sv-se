---
title: Verifiera scope och approller skyddat webb-API | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API och konfigurerar programmets kod.
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
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768122"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Skyddat webb-API: Verifiera scope och approller

I den här artikeln beskrivs hur du kan lägga till auktorisering i webb-API:et. Det här skyddet säkerställer att API:et endast anropas av:

- Program för användare som har rätt omfattning.
- Daemon-appar som har rätt programroller.

> [!NOTE]
> Kodavsnitten från den här artikeln extraheras från följande exempel, som är fullt fungerande:
>
> - [ASP.NET inkrementell självstudiekurs för core web API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) på GitHub
> - [ASP.NET exempel på webb-API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Om du vill skydda ett ASP.NET eller ASP.NET Core-webb-API måste du lägga till `[Authorize]` attributet i något av följande objekt:

- Själva handkontrollen om du vill att alla styrenhetsåtgärder ska skyddas
- Åtgärden för enskild styrenhet för ditt API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Men det här skyddet räcker inte. Det garanterar bara att ASP.NET och ASP.NET Core validerar token. Ditt API måste verifiera att token som används för att anropa API:et begärs med förväntade anspråk. Dessa påståenden behöver särskilt kontrolleras:

- *Scopen* om API:et anropas för en användares räkning.
- *Appen roller* om API kan anropas från en demon app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verifiera scope i API:er som anropas för användarnas räkning

Om en klientapp anropar ditt API för en användares räkning måste API:et begära en innehavartoken som har specifika scope för API:et. Mer information finns i [Kodkonfiguration | Bärare token](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

Metoden `VerifyUserHasAnyAcceptedScope` gör något i stil med följande steg:

- Kontrollera att det finns `http://schemas.microsoft.com/identity/claims/scope` `scp`ett anspråk som heter eller .
- Verifiera att anspråket har ett värde som innehåller det scope som förväntas av API:et.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Den föregående [exempelkoden](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) är för ASP.NET Core. `HttpContext.User` För ASP.NET ersätter du `ClaimsPrincipal.Current`bara med och `"http://schemas.microsoft.com/identity/claims/scope"` ersätter anspråkstypen med `"scp"`. Se även kodavsnittet senare i den här artikeln.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifiera approller i API:er som anropas av daemonappar

Om ditt webb-API anropas av en [app för daemon](scenario-daemon-overview.md)bör den appen kräva en programbehörighet till webb-API:et. Som visas i [Exponera programbehörigheter (approller)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)exponerar ditt API sådana behörigheter. Ett exempel `access_as_application` är approllen.

Du måste nu ha ditt API verifiera att `roles` token den tar emot innehåller anspråket och att det här anspråket har det förväntade värdet. Verifieringskoden liknar koden som verifierar delegerade behörigheter, förutom att styrenhetens åtgärd testar för roller i stället för scope:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Metoden `ValidateAppRole` kan vara ungefär så här:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Den här gången är kodavsnittet för ASP.NET. För ASP.NET Core `ClaimsPrincipal.Current` ersätter du `HttpContext.User`bara med `"roles"` och `"http://schemas.microsoft.com/identity/claims/roles"`ersätter anspråksnamnet med . Se även kodavsnittet tidigare i den här artikeln.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Acceptera app-bara token om webb-API bör anropas endast av daemon apps

Användare kan också använda rollanspråk i användartilldelningsmönster, som visas i [Så här lägger du till approller i ditt program och tar emot dem i token](howto-add-app-roles-in-azure-ad-apps.md). Om rollerna kan tilldelas båda, kommer kontrollroller att låta appar logga in som användare och användare för att logga in som appar. Vi rekommenderar att du deklarerar olika roller för användare och appar för att förhindra denna förvirring.

Om du bara vill att demonappar ska anropa webb-API:et lägger du till villkoret att token är en apptoken när du validerar approllen.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Om du markerar det omvända villkoret kan endast appar som loggar in en användare anropa ditt API.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-protected-web-api-production.md)
