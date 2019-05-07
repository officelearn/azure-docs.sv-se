---
title: Webbapp som loggar in användare (logga in) - Microsoft identity-plattformen
description: Lär dig att skapa en webbapp som loggar in användare (logga in)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074627"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webbapp som loggar in användare – logga in

Lär dig mer om att lägga till inloggning till koden för din webbapp som loggar in användare.

## <a name="sign-in"></a>logga in

Koden som vi har i föregående artikel [appkonfiguration kod](scenario-web-app-sign-user-app-configuration.md) är allt du behöver implementera utloggning. När användaren har loggat in till din app, vill du förmodligen att de kan logga ut. ASP.NET core hanterar utloggning åt dig.

## <a name="what-sign-out-involves"></a>Innebär att vad logga ut

Logga ut från en webbapp handlar om mer än ta bort information om det inloggade kontot från webbappens tillstånd.
Webbappen måste också omdirigera användaren till Microsoft identity-plattformen v2.0 `logout` slutpunkt för att logga ut. När din webbapp omdirigeras användaren till den `logout` slutpunkten, den här slutpunkten tar bort användarens session i webbläsaren. Om din app inte gå till den `logout` slutpunkt kan användaren skulle autentiseras på nytt till din app utan att behöva ange sina autentiseringsuppgifter igen, eftersom de skulle ha en giltig inloggning session med Microsoft Identity-plattformen v2.0-slutpunkten.

Mer information finns i den [skicka en utloggning begäran](v2-protocols-oidc.md#send-a-sign-out-request) i avsnittet den [Microsoft Identity-plattformen version 2.0 och OpenID Connect-protokollet](v2-protocols-oidc.md) konceptuella dokumentation.

## <a name="application-registration"></a>Programregistrering

Under registreringen programmet du kommer har registrerat en **efter utloggning URI**. I våra självstudier ska du registrerade `https://localhost:44321/signout-oidc` i den **URL för utloggning** i den **avancerade inställningar** i avsnittet den **autentisering** sidan. Mer information finns i [ registrera webApp-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core-koden

### <a name="signout-button"></a>Knappen för utloggning

Logga ut knappen exponeras i `Views\Shared\_LoginPartial.cshtml` och endast visas när det finns ett autentiserat konto (det vill säga när användaren har tidigare loggat in).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` åtgärden på den `AccountController`

Att trycka på den **logga ut** utlösare för web Apps-knappen i `SignOut` åtgärden på den `Account` controller. I tidigare versioner av ASP.NET core-mallar i `Account` controller har inbyggda med webbappen, men detta gäller inte längre eftersom den är nu en del av ramverket ASP.NET Core. 

Koden för den `AccountController` är tillgänglig från ASP.NET core lagringsplatsen på från [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontroll av:

- Anger ett OpenID omdirigerings-URI att `/Account/SignedOut` betyder att kontrollanten när Azure AD har utfört logga ut
- Anrop `Signout()`, vilket gör att kontakta Microsofts identitetsplattform OpenIdConnect mellanprogrammet `logout` slutpunkt som:

  - Tar bort sessions-cookie från webbläsaren, och
  - Anrop slutligen anropar tillbaka den **URL för utloggning**, vilket) visar som standard den signerade ut visningssida [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) får också information som en del av ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Spärra anropet till den `logout` slutpunkt

ASP.NET Core OpenIdConnect mellanprogram gör det möjligt för din app att fånga upp anropet till Microsoft identity-plattformen `logout` slutpunkt genom att tillhandahålla en OpenIdConnect-händelse med namnet `OnRedirectToIdentityProviderForSignOut`. Webbappen använder den att undvika dialogrutan Välj konto behöver visas för användaren när du loggar. Den här avlyssning görs den `AddAzureAdV2Authentication` av den `Microsoft.Identity.Web` återanvändbara biblioteket. Se [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET Code

I ASP.NET, logga ut utlöses från metoden SignOut() på en domänkontrollant (till exempel AccountController). Den här metoden är inte en del av ASP.NET-ramverket (sätt som strider mot vad som händer i ASP.NET Core), och inte använder async och det är därför det:

- skickar en utloggning OpenId-utmaning
- Rensar cachen
- omdirigerar till sidan för företaget

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protokoll

Om du inte vill använda ASP.NET Core och ASP.NET, kan du titta på i dokumentationen för protokoll, som är tillgänglig från [öppna ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-sign-user-production.md)
