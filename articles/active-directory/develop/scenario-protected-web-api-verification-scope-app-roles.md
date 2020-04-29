---
title: Verifiera omfattningar och app-roller skyddad webb-API | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API och konfigurerar programmets kod.
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
ms.openlocfilehash: 018e7f9bc389e3d148ff6860dae9fef88991e5c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537176"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Skyddat webb-API: kontrol lera omfattningar och app-roller

I den här artikeln beskrivs hur du kan lägga till auktorisering i ditt webb-API. Det här skyddet säkerställer att API: et endast anropas av:

- Program åt användare som har rätt omfång.
- Daemon-appar som har rätt program roller.

> [!NOTE]
> Kodfragmenten från den här artikeln extraheras från följande exempel, som är fullt funktionella:
>
> - [ASP.net Core stegvis självstudie för webb-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) på GitHub
> - [ASP.NET-webb-API-exempel](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Om du vill skydda ett ASP.NET-eller ASP.NET Core-webb-API `[Authorize]` måste du lägga till attributet till något av följande objekt:

- Själva styrenheten om du vill att alla åtgärder för styrenheter ska skyddas
- Åtgärden individuell kontrollant för ditt API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Men det här skyddet räcker inte. Det garanterar endast att ASP.NET och ASP.NET Core validera token. Ditt API måste verifiera att token som används för att anropa API: et begärs med förväntade anspråk. Dessa anspråk måste särskilt verifieras:

- *Omfattningarna* om API: t anropas för en användares räkning.
- *App-rollerna* om API: et kan anropas från en daemon-app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verifiera omfattningarna i API: er som anropas för användarnas räkning

Om en klient app anropar ditt API för en användares räkning måste API: et begära en Bearer-token som har vissa omfång för API: et. Mer information finns i [kod konfiguration | Bearer-token](scenario-protected-web-api-app-configuration.md#bearer-token).

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

`VerifyUserHasAnyAcceptedScope` Metoden gör något så här:

- Kontrol lera att det finns ett `http://schemas.microsoft.com/identity/claims/scope` anspråk `scp`med namnet eller.
- Kontrol lera att anspråket har ett värde som innehåller omfånget som förväntas av API: et.

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

Föregående [exempel kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) är för ASP.net Core. För ASP.NET ersätter `HttpContext.User` du bara med `ClaimsPrincipal.Current`och ersätter anspråks typen `"http://schemas.microsoft.com/identity/claims/scope"` med `"scp"`. Se även kodfragmentet senare i den här artikeln.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifiera app-roller i API: er som anropas av daemon-appar

Om ditt webb-API anropas av en [daemon-app](scenario-daemon-overview.md), bör appen kräva en program behörighet för ditt webb-API. Som det visas i [exponera program behörigheter (app-roller)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)exponerar ditt API sådana behörigheter. Ett exempel är `access_as_application` app-rollen.

Du måste nu ha ditt API för att verifiera att token som den tar `roles` emot innehåller anspråk och att detta anspråk har det förväntade värdet. Verifierings koden liknar den kod som verifierar delegerade behörigheter, förutom att styrenhets åtgärden testar för roller i stället för omfattningar:

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

`ValidateAppRole` Metoden kan vara något som liknar detta:

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

Den här gången är kodfragmentet för ASP.NET. För ASP.NET Core ersätter `ClaimsPrincipal.Current` du bara med `HttpContext.User`och ersätter `"roles"` anspråks namnet med `"http://schemas.microsoft.com/identity/claims/roles"`. Se även kodfragmentet tidigare i den här artikeln.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accepterar endast app-token om webb-API: t endast ska anropas av daemon-appar

Användare kan också använda roll anspråk i användar tilldelnings mönster, som du ser i gör så [här: Lägg till app-roller i programmet och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md). Om rollerna är tilldelnings bara till båda kan du kontrol lera att appar loggar in som användare och användare loggar in som appar när du kontrollerar roller. Vi rekommenderar att du deklarerar olika roller för användare och appar för att förhindra denna förvirring.

Om du bara vill att daemon-appar ska anropa ditt webb-API lägger du till villkoret att token är en app-only-token när du validerar app-rollen.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Genom att kontrol lera det inverterade villkoret kan endast appar som loggar in en användare anropa ditt API.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-protected-web-api-production.md)
