---
title: Skyddad Web API – kod konfiguration för app | Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488842"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Skyddat webb-API: Lägger till auktorisering till ditt API

I den här artikeln beskrivs hur du kan lägga till auktorisering i ditt webb-API. Det här skyddet säkerställer att API: et endast anropas av:

- Program åt användare som har rätt omfång.
- Daemon-appar som har rätt program roller.

> [!NOTE]
> Kodfragmenten från den här artikeln extraheras från följande exempel, som är fullt funktionella
>
> - [ASP.net Core stegvis självstudie för webb-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) på GitHub
> - [ASP.NET-webb-API-exempel](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

För att skydda ett ASP.net/ASP.net Core webb-API måste du lägga till `[Authorize]` attributet på något av följande:

- Själva kontrollanten, om du vill att alla åtgärder för kontrollanten ska skyddas
- Åtgärden individuell kontrollant för ditt API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Men det här skyddet räcker inte. Det garanterar bara att ASP.NET/ASP.NET-kärnan validerar token. Ditt API måste verifiera att token som används för att anropa ditt webb-API begärdes med de anspråk som det förväntar sig, särskilt:

- *Omfattningarna*, om API: t anropas för en användares räkning.
- *App*-rollerna, om API: et kan anropas från en daemon-app.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verifierar omfång i API: er som anropas för användarnas räkning

Om ditt API anropas av en klient app för en användares räkning måste den begära en Bearer-token som har vissa omfång för API: et. (Se [kod konfiguration | Bearer](scenario-protected-web-api-app-configuration.md#bearer-token)-token.)

```CSharp
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

`VerifyUserHasAnyAcceptedScope` Metoden skulle göra något som liknar följande:

- Kontrol lera att det finns ett anspråk `http://schemas.microsoft.com/identity/claims/scope` med `scp`namnet eller.
- Kontrol lera att anspråket har ett värde som innehåller omfånget som förväntas av API: et.

```CSharp
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

Den här [exempel koden](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) är för ASP.net Core. För ASP.net ersätter `HttpContext.User` du bara med `ClaimsPrincipal.Current`och ersätter anspråks typen `"http://schemas.microsoft.com/identity/claims/scope"` med `"scp"`. (Se även kodfragmentet senare i den här artikeln.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verifiera app-roller i API: er som anropas av daemon-appar

Om ditt webb-API anropas av en [daemon-app](scenario-daemon-overview.md), bör appen kräva en program behörighet för ditt webb-API. Vi har sett i att visa [program behörigheter (app-roller)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) som ditt API visar sådana behörigheter (till exempel `access_as_application` app-rollen).
Du måste nu ha dina API: er verifiera att den token som tas `roles` emot innehåller anspråk och att detta anspråk har det värde som förväntas. Den här verifieringens kod liknar den kod som verifierar delegerade behörigheter, förutom att, i stället för att testa `scopes`, så kommer din kontroll enhet att testa för: `roles`

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` Metoden kan vara något som liknar detta:

```CSharp
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

Den här gången är kodfragmentet för ASP.NET. För ASP.net Core ersätter `ClaimsPrincipal.Current` du bara med `HttpContext.User`och ersätter `"roles"` anspråks namnet med `"http://schemas.microsoft.com/identity/claims/roles"`. (Se även kodfragmentet tidigare i den här artikeln.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accepterar endast app-token om webb-API: t endast ska anropas av daemon-appar

`roles` Anspråket används också för användare i användar tilldelnings mönster. (Mer [information finns i så här gör du: Lägg till app-roller i programmet och ta emot dem i](howto-add-app-roles-in-azure-ad-apps.md)token.) För att bara kontrol lera roller kan appar logga in som användare och det andra sättet, om rollerna kan tilldelas båda. Vi rekommenderar att du deklarerar olika roller för användare och appar för att förhindra denna förvirring.

Om du bara vill tillåta att daemon-appar anropar webb-API: et lägger du till ett villkor när du validerar app-rollen, att token är en app-only-token:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Genom att kontrol lera det inverterade villkoret kan endast appar som loggar in en användare anropa ditt API.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-protected-web-api-production.md)
