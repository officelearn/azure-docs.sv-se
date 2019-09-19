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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086462"
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

### <a name="application-registration"></a>Programregistrering

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Under program registreringen har du registrerat en **URI för post-utloggning**. I den här självstudien `https://localhost:44321/signout-oidc` har du registrerat i fältet **Logga in URL** i avsnittet **Avancerade inställningar** på sidan **autentisering** . Mer information finns i [Registrera webApp-appen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Under program registreringen har du registrerat en **URI för post-utloggning**. I den här självstudien `https://localhost:44308/Account/EndSession` har du registrerat i fältet **Logga in URL** i avsnittet **Avancerade inställningar** på sidan **autentisering** . Mer information finns i [Registrera webApp-appen](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Under program registreringen registrerar du en **URI för post-utloggning**. I den här självstudien `http://localhost:8080/msal4jsample/` har du registrerat i fältet **Logga in URL** i avsnittet **Avancerade inställningar** på sidan **autentisering** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Under program registreringen behöver du inte registrera en utloggnings-URL. Exemplet implementerar inte global utloggning

---

### <a name="sign-out-button"></a>Knappen Logga ut

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.net Core visas knappen Logga ut i `Views\Shared\_LoginPartial.cshtml` och visas endast när det finns ett autentiserat konto (det vill säga när användaren tidigare har loggat in).

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET MVC visas knappen Logga ut i `Views\Shared\_LoginPartial.cshtml` och visas endast när det finns ett autentiserat konto (det vill säga när användaren tidigare har loggat in).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

I vår Java snabb start finns knappen Logga ut i filen main/resources/templates/auth_page.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-snabb starten finns knappen Logga ut i filen [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`åtgärd för kontrollanten

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Genom att trycka på knappen **Logga ut** i ASP.net i-webbappen utlöses `SignOut` åtgärden på `AccountController` kontrollanten. I tidigare versioner av ASP.net Core-mallarna `Account` har kontrollanten bäddats in med webbappen, men det är inte längre fallet eftersom det nu är en del av själva ASP.net Core ramverket.

Koden för `AccountController` är tillgänglig från ASP.net Core-lagringsplatsen på från [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Konto kontrollen:

- Ställer in en OpenID omdirigerings-URI till `/Account/SignedOut` så att styrenheten rings tillbaka när Azure AD har utfört utloggning
- Anrop `Signout()`, som låter OpenIdConnect mellanprogram kontakta den Microsoft Identity Platform `logout` -slutpunkt som:

  - Tar bort sessions-cookien från webbläsaren och
  - anropar slutligen återställnings- **URL: en**som standard visar sidan signerad vy som är signerad [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) som en del av ASP.net Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.net utlöses utloggning från `SignOut()` -metoden på en kontrollant (till exempel AccountController). Den här metoden är inte en del av ASP.NET-ramverket (som strider mot vad som händer i ASP.NET Core). Den använder `async`inte, och det är därför det:

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

I Java hanteras utloggningen genom att anropa slut punkten för Microsoft Identity Platform-utloggning direkt och tillhandahålla post_logout_redirect_uri.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Spärr av anrop till `logout` slut punkten

URI: n efter utloggning gör det möjligt för program att delta i den globala utloggningen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.net Core OpenIdConnect mellan program vara gör att din app kan fånga upp anropet till Microsoft Identity `logout` Platform-slutpunkten genom att `OnRedirectToIdentityProviderForSignOut`tillhandahålla en OpenIdConnect-händelse med namnet. Se [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) för ett exempel på hur du prenumererar på den här händelsen (rensa token cache)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET kan du delegera till mellanprogram för att köra utloggningen och rensa sessionens cookie:

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

I vår Java snabb start visar URL: en för att logga ut omdirigering bara index. html-Sidan 

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-snabb starten visar den utgående omdirigerings-URI: n bara index. HTML-sidan.

---

## <a name="protocol"></a>Protocol

Om du vill veta mer om utloggning läser du protokoll dokumentationen som är tillgänglig från [Öppna-ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-sign-user-production.md)
