---
title: 'Webbapp som anropar webb-API: er (logga in) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (logga in)'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720205"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webbapp som anropar webb-API: er – logga in

Du vet redan hur du ska lägga till inloggning till din webbapp. Du lär dig det i [en webbapp som loggar in användare – Lägg till inloggning](scenario-web-app-sign-user-sign-in.md).

Vad är det som är annorlunda, är att när användaren har loggat ut, från det här programmet eller från ett program som du vill ta bort från token cache, de token som är associerade med användaren.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Avlyssna återanrop efter utloggning – enkel utloggning

Ditt program kan fånga efter `logout`-händelse, till exempel ta bort posten för det token-cache som är associerat med kontot som loggade ut. Webbapp lagrar åtkomsttoken för användaren i ett cacheminne. När återanropet efter `logout` fångas upp kan ditt webb program ta bort användaren från token-cachen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Den här mekanismen illustreras i metoden `AddMsal()` i [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

I den **utloggnings-URL** som du har registrerat för ditt program kan du implementera enkel utloggning. Microsoft Identity Platform `logout`-slutpunkten anropar den **utloggnings-URL** som är registrerad i ditt program. Det här anropet sker om utloggningen initierades från din webbapp, eller från en annan webbapp eller webbläsaren. Mer information finns i [enkel utloggning](v2-protocols-oidc.md#single-sign-out).

```CSharp
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

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](scenario-web-app-call-api-acquire-token.md)
