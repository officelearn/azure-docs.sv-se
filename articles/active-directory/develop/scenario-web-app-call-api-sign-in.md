---
title: Ta bort konton från tokencachen vid ut logga ut – Microsoft identity platform | Azure
description: Lär dig hur du tar bort ett konto från tokencachen vid ut logga ut
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881620"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>En webbapp som anropar webb-API:er: Ta bort konton från tokencachen vid global utskrivning

Du har lärt dig hur du lägger till inloggning i webbappen i [webbappen som loggar in användare: Logga in och logga ut](scenario-web-app-sign-user-sign-in.md).

Ut logga ut är annorlunda för en webbapp som anropar webb-api:er. När användaren loggar ut från ditt program, eller från ett program, måste du ta bort token som är associerade med den användaren från tokencachen.

## <a name="intercept-the-callback-after-single-sign-out"></a>Avlyssna motringningen efter enkel utskrivning

Om du vill rensa den tokencachepost som är associerad `logout` med kontot som loggat ut kan ditt program avlyssna efterhändelsen. Webbappar lagrar åtkomsttoken för varje användare i en tokencache. Genom att avlyssna `logout` efterringningen kan webbprogrammet ta bort användaren från cacheminnet.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

För ASP.NET Core illustreras avlyssningsmekanismen i `AddMsal()` metoden för [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Med webbadressen för utloggning som du tidigare har registrerat för ditt program kan du implementera en enda utloggning. Slutpunkten för `logout` Microsoft-identitetsplattformen anropar din utloggnings-URL. Det här samtalet sker om ut logga utet har startats från din webbapp eller från en annan webbapp eller webbläsaren. Mer information finns i [Enkel ut logga ut](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Koden för `RemoveAccountAsync` är tillgänglig från [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Det ASP.NET exemplet tar inte bort konton från cacheminnet vid global utskrivning.

# <a name="java"></a>[Java](#tab/java)

Java-exemplet tar inte bort konton från cacheminnet vid global utskrivning.

# <a name="python"></a>[Python](#tab/python)

Python-exemplet tar inte bort konton från cacheminnet vid global utskrivning.

---

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Skaffa en token för webbappen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Skaffa en token för webbappen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Skaffa en token för webbappen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Skaffa en token för webbappen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
