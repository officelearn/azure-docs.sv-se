---
title: Ta bort konton från token cache vid utloggning – Microsoft Identity Platform | Azure
description: Lär dig hur du tar bort ett konto från token-cache vid utloggning
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758878"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>En webbapp som anropar webb-API: ta bort konton från token-cachen vid global utloggning

Du har lärt dig hur du lägger till inloggning till din webbapp i [en webbapp som loggar in användare: inloggning och utloggning](scenario-web-app-sign-user-sign-in.md).

Utloggning är annorlunda för en webbapp som anropar webb-API: er. När användaren loggar ut från ditt program, eller från ett program, måste du ta bort de token som är associerade med användaren från token-cachen.

## <a name="intercept-the-callback-after-single-sign-out"></a>Fånga upp återanropet efter enkel utloggning

För att ta bort den token-cachepost som är kopplad till det konto som loggade ut kan ditt program fånga efter `logout` händelse. Web Apps lagrar åtkomsttoken för varje användare i en token-cache. Genom att fånga efter `logout` motringning, kan ditt webb program ta bort användaren från cachen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

För ASP.NET Core illustreras avlyssnings mekanismen i `AddMsal()`-metoden för [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

I den utloggnings-URL som du tidigare registrerade för ditt program kan du implementera enkel utloggning. Microsoft Identity Platform `logout`-slutpunkten anropar din utloggnings-URL. Det här anropet sker om utloggningen startar från din webbapp eller från en annan webbapp eller webbläsaren. Mer information finns i [enkel utloggning](v2-protocols-oidc.md#single-sign-out).

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

Koden för `RemoveAccountAsync` är tillgänglig från [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET-exemplet tar inte bort konton från cachen vid global utloggning.

# <a name="javatabjava"></a>[Java](#tab/java)

Java-exemplet tar inte bort konton från cachen vid global utloggning.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python-exemplet tar inte bort konton från cachen vid global utloggning.

---

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
