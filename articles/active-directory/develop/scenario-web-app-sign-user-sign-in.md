---
title: Webbapp som loggar in användare (logga in) – Microsoft Identity Platform
description: Lär dig hur du skapar en webbapp som loggar in användare (logga in)
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
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812391"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webbapp som loggar in användare – logga in

Lär dig hur du lägger till inloggning till koden för din webbapp som loggar in användare.

## <a name="sign-in"></a>Logga in

Koden som vi har granskat i föregående artikel [Apps kod konfiguration](scenario-web-app-sign-user-app-configuration.md) är allt du behöver för att implementera inloggning.
När användaren har loggat in till din app vill du förmodligen göra det möjligt att logga ut. ASP.NET Core hanterar utloggning åt dig.

## <a name="what-sign-out-involves"></a>Vilken utloggning omfattar

Att logga ut från en webbapp är mer än att ta bort informationen om det inloggade kontot från webbappens tillstånd.
Webbappen måste också omdirigera användaren till Microsoft Identity Platform `logout` -slutpunkten för att logga ut. När din webbapp omdirigerar användaren till `logout` slut punkten rensar den här slut punkten användarens session från webbläsaren. Om din app inte gick till `logout` slut punkten, skulle användaren autentisera till appen igen utan att ange sina autentiseringsuppgifter igen, eftersom de skulle ha en giltig enkel inloggnings-session med slut punkten för Microsoft Identity Platform.

Mer information finns i avsnittet [skicka en utloggnings förfrågan](v2-protocols-oidc.md#send-a-sign-out-request) i dokumentationen för [Microsoft Identity Platform och OpenID Connect Protocol](v2-protocols-oidc.md) konceptuell.

## <a name="application-registration"></a>Programregistrering

Under program registreringen har du registrerat en **URI för post-utloggning**. I den här självstudien `https://localhost:44321/signout-oidc` har du registrerat i fältet **Logga in URL** i avsnittet **Avancerade inställningar** på sidan **autentisering** . Mer information finns i [Registrera webApp-appen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core kod

### <a name="signout-button"></a>Knappen Logga in

Knappen Logga ut visas `Views\Shared\_LoginPartial.cshtml` och visas endast när det finns ett autentiserat konto (det vill säga när användaren tidigare har loggat in).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`åtgärd för`AccountController`

Genom att trycka på knappen **Logga ut** i webbappen utlöses `SignOut` åtgärden på `Account` kontrollanten. I tidigare versioner av ASP.net Core-mallarna `Account` har kontrollanten bäddats in med webbappen, men det är inte längre fallet eftersom det nu är en del av själva ASP.net Core ramverket. 

Koden för `AccountController` är tillgänglig från ASP.net Core-lagringsplatsen på från [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Konto kontrollen:

- Ställer in en OpenID omdirigerings-URI till `/Account/SignedOut` så att styrenheten rings tillbaka när Azure AD har utfört utloggning
- Anrop `Signout()`, som låter OpenIdConnect mellanprogram kontakta den Microsoft Identity Platform `logout` -slutpunkt som:

  - Tar bort sessions-cookien från webbläsaren och
  - Anrop skickas slutligen tillbaka till **utloggnings-URL: en**, som standard, som standard visar sidan signerad vy som är [signerad. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ingår också som en del av ASP.net Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Spärr av anrop till `logout` slut punkten

ASP.net Core OpenIdConnect mellan program vara gör att din app kan fånga upp anropet till Microsoft Identity `logout` Platform-slutpunkten genom att `OnRedirectToIdentityProviderForSignOut`tillhandahålla en OpenIdConnect-händelse med namnet. Webbappen använder den för att försöka undvika att dialog rutan Välj konto visas för användaren vid utloggning. Den här avlyssningen görs i `AddAzureAdV2Authentication` `Microsoft.Identity.Web` det återanvändbara biblioteket. Se [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

## <a name="aspnet-code"></a>ASP.NET-kod

I ASP.NET utlöses utloggning från metoden Signe () på en kontrollant (till exempel AccountController). Den här metoden är inte en del av ASP.NET-ramverket (som strider mot vad som händer i ASP.NET Core) och som inte använder async, och det är därför:

- skickar en OpenId-utloggnings utmaning
- rensar cachen
- omdirigerar till sidan den vill ha

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

## <a name="protocol"></a>Protocol

Om du inte vill använda ASP.NET Core eller ASP.NET kan du titta i protokoll dokumentationen, som är tillgänglig från [Öppna ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-sign-user-production.md)
