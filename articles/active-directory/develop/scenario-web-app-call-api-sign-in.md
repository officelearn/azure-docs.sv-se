---
title: 'Webbapp som anropar webb-API: er (logga in) - Microsoft identity-plattformen'
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074567"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webbapp som anropar webb-API: er – logga in

Vet du redan hur du lägger till inloggning till din webbapp. Du lär dig att [webbapp som loggar in användare – Lägg till inloggning](scenario-web-app-sign-user-sign-in.md).

Vad är annorlunda här, är när användaren har loggat ut, från det här programmet eller från alla program, vill du ta bort från token cachen token som är associerade med användaren.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Spärra motringningen efter utloggning - enda logga ut

Ditt program kan komma åt den när `logout` händelse, till exempel att rensa posten tokens cacheminne som är associerade med kontot som loggat ut. Vi ser i den andra delen av den här självstudiekursen (om det webbprogram som anropar ett webb-API), att webbappen lagrar åtkomsttoken för användaren i ett cacheminne. Fånga upp den när `logout` återanrop gör det möjligt för ditt webbprogram att ta bort användaren från token-cache. Den här mekanismen illustreras i den `AddMsal()` -metoden för [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

Den **Url för utloggning** att du har registrerat för ditt program kan du implementera enkel utloggning. Microsofts identitetsplattform `logout` slutpunkten anropar den **URL för utloggning** registrerats med ditt program. Det här anropet händer om utloggningen initierades från din webbapp eller från en annan webbapp eller webbläsaren. Mer information finns i [enkel utloggning](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) i konceptuell-dokumentationen.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token för webbappen](scenario-web-app-call-api-acquire-token.md)
