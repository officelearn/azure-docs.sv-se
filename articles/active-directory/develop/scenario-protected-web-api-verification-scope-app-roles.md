---
title: Skyddade webb-API - konfiguration av kod | Azure Active Directory
description: Lär dig hur du skapar ett skyddat webb-API och konfigurera din programkod.
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
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551546"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Skyddade webb-API: Att lägga till auktorisering i ditt API

Den här artikeln beskrivs hur du kan lägga till auktorisering till dina webb-API. Det här skyddet säkerställer att API: et anropas endast av:

- Program för användare som har rätt scope.
- Daemon-appar som har rätt programroller.

Om du vill skydda en ASP.NET/ASP.NET Core webb-API, måste du lägga till den `[Authorize]` attributet på något av följande:

- Kontrollanten själva, om du vill att alla åtgärder för kontrollenheten som ska skyddas
- Enskilda controller-åtgärden för ditt API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Men det räcker inte med det här skyddet. Det garanterar endast att ASP.NET/ASP.NET Core verifierar token. Din API behöver att verifiera att den token som används för att anropa ditt webb-API som begärdes med anspråk det förväntar sig, särskilt:

- Den *scope*, om API: et anropas för en användares räkning.
- Den *approller*om API: et kan anropas från en daemon-app.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verifiera scope i API: er som kallas för användare

Om ditt API anropas av en klientapp för en användares räkning, måste det att begära en ägartoken som har specifika omfattningar för API: et. (Se [kod configuration | Ägartoken](scenario-protected-web-api-app-configuration.md#bearer-token).)

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

Den `VerifyUserHasAnyAcceptedScope` metoden skulle göra något som liknar följande:

- Kontrollera att det finns ett anspråk med namnet `http://schemas.microsoft.com/identity/claims/scope` eller `scp`.
- Kontrollera att anspråket har ett värde som innehåller den omfattning som förväntas av API: et.

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

Det här exemplet är för ASP.NET Core. Ersätt bara för ASP.NET, `HttpContext.User` med `ClaimsPrincipal.Current`, och Ersätt Anspråkstypen `"http://schemas.microsoft.com/identity/claims/scope"` med `"scp"`. (Se även kodfragmentet nedan.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verifiera roller i API: er som anropas av daemon-appar

Om ditt webb-API anropas av en [daemon app](scenario-daemon-overview.md), appen kräver ett program behörighet att ditt webb-API. Vi har sett i [exponera programbehörigheter (roller)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) att ditt API exponerar sådan behörighet (till exempel den `access_as_application` app rollen).
Nu måste du ha dina API: er som du kontrollera att den token som togs emot innehåller den `roles` anspråk och att det här anspråket har värdet för det förväntar sig. Den kod som gör den här kontrollen som liknar den kod som verifierar delegerade behörigheter, förutom den, i stället för att testa för `scopes`, domänkontrollant-åtgärden kommer att testa för `roles`:

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

Den `ValidateAppRole()` metoden kan vara ungefär så här:

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

Det här exemplet är för ASP.NET. Ersätt bara för ASP.NET Core `ClaimsPrincipal.Current` med `HttpContext.User`, och Ersätt den `"roles"` anspråk namn med `"http://schemas.microsoft.com/identity/claims/roles"`. (Se även kodfragmentet tidigare i den här artikeln.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accepterar endast app-token om webb-API ska anropas bara av daemon-appar

Den `roles` anspråk används också för användare i användarmönster för tilldelning. (Se [så här: Lägg till roller i ditt program och tar emot dem i token](howto-add-app-roles-in-azure-ad-apps.md).) Så här kommer bara kontrollerar roller tillåter program att logga in som användare och tvärtom, om rollerna som tilldelas till båda. Vi rekommenderar att du deklarera olika roller för användare och appar att förhindra att den här förvirring.

Om du vill att endast tillåta daemon appar att anropa ditt webb-API, Lägg till ett villkor, när du validerar rollen app att token är en appspecifika-token:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Kontrollerar inverterade villkoret kan endast appar som loggar in en användare att anropa ditt API.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-protected-web-api-production.md)
