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
ms.openlocfilehash: a77bb59afa753fa9d1655e787d4f7a18715ed2ca
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701594"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Ta bort konton från cachen vid global utloggning

Du vet redan hur du ska lägga till inloggning till din webbapp. Du lär dig det i [en webbapp som loggar in användare – Lägg till inloggning](scenario-web-app-sign-user-sign-in.md).

Vad är det som är annorlunda, är att när användaren har loggat ut, från det här programmet eller från ett program som du vill ta bort från token cache, de token som är associerade med användaren.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Avlyssna återanrop efter utloggning – enkel utloggning

Ditt program kan fånga efter `logout` händelse, till exempel rensa posten för det token-cache som är associerat med det konto som loggade ut. Webbapp lagrar åtkomsttoken för användaren i ett cacheminne. Om du fångar upp efter `logout` motringning kan ditt webb program ta bort användaren från token-cachen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Den här mekanismen illustreras i `AddMsal()`-metoden för [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

I den **utloggnings-URL** som du har registrerat för ditt program kan du implementera enkel utloggning. Microsoft Identity Platform `logout`-slutpunkten anropar den **utloggnings-URL** som är registrerad i ditt program. Det här anropet sker om utloggningen initierades från din webbapp, eller från en annan webbapp eller webbläsaren. Mer information finns i [enkel utloggning](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Koden för RemoveAccountAsync är tillgänglig från [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET-exemplet tar inte bort konton från cachen vid global utloggning

# <a name="javatabjava"></a>[Java](#tab/java)

Java-exemplet tar inte bort konton från cachen vid global utloggning

# <a name="pythontabpython"></a>[Python](#tab/python)

Python-exemplet tar inte bort konton från cachen vid global utloggning

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
