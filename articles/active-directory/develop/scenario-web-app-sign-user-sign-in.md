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
ms.openlocfilehash: d5c45005d6a54765458b463acb12c21a1f3b6d0c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336770"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Webbapp som loggar in användare – logga in och logga ut

Lär dig hur du lägger till inloggning i koden för din webbapp som loggar in användare och sedan hur du kan logga ut

## <a name="sign-in"></a>Logga in

Inloggning har gjorts av två delar:

- inloggnings knappen på HTML-sidan
- inloggnings åtgärden i koden bakom i kontrollanten

### <a name="sign-in-button"></a>Inloggnings knapp

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.net Core visas inloggnings knappen i `Views\Shared\_LoginPartial.cshtml` och visas bara när det inte finns något autentiserat konto (det vill säga när användaren inte har loggat in eller har loggat in).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
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
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

I vår Java snabb start finns knappen Logga ut i filen [main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-snabb starten finns det ingen inloggnings knapp. Användaren uppmanas automatiskt att logga in med koden bakom när den når webbappens rot. Se [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`Login`åtgärd för kontrollanten

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.net utlöser `SignIn` åtgärden på `AccountController` kontroll enheten genom att trycka på **inloggnings** knappen i webbappen. I tidigare versioner av ASP.net Core-mallarna `Account` har kontrollanten bäddats in med webbappen, men det är inte längre fallet eftersom det nu är en del av själva ASP.net Core ramverket.

Koden för `AccountController` är tillgänglig från ASP.net Core-lagringsplatsen från [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Konto kontrollen utmanar användaren genom att omdirigera till Microsoft Identity Platform-slutpunkten. Mer information finns i [signin](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) -metoden som tillhandahålls som en del av ASP.net Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.net utlöses utloggning från `SignOut()` -metoden på en kontrollant (till exempel [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Den här metoden är inte en del av ASP.NET-ramverket (som strider mot vad som händer i ASP.NET Core). Företaget

- skickar en OpenId inloggnings utmaning när en omdirigerings-URI har föreslås

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

I Java hanteras utloggningen genom att anropa slut punkten för Microsoft Identity Platform-utloggning direkt och tillhandahålla post_logout_redirect_uri. Mer information finns i [AuthPageController. java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Till skillnad från andra plattformar, MSAL. Python tar hand om att låta användaren logga in från inloggnings sidan. Se [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

med metoden _build_msal_app () definierad i [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) enligt följande:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

När användaren har loggat in till din app vill du förmodligen göra det möjligt att logga ut.

## <a name="sign-out"></a>Logga ut

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

Under program registreringen behöver du inte registrera en extra utloggnings-URL. Appen kommer att anropas igen på dess huvud-URL

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

I python-snabb starten finns knappen Logga ut i filen [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout`åtgärd för kontrollanten

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Genom att trycka på knappen **Logga ut** i ASP.net i-webbappen utlöses `SignOut` åtgärden på `AccountController` kontrollanten. I tidigare versioner av ASP.net Core-mallarna `Account` har kontrollanten bäddats in med webbappen, men det är inte längre fallet eftersom det nu är en del av själva ASP.net Core ramverket.

Koden för `AccountController` är tillgänglig från ASP.net Core-lagringsplatsen på från [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Konto kontrollen:

- Ställer in en OpenID omdirigerings-URI till `/Account/SignedOut` så att styrenheten rings tillbaka när Azure AD har slutfört utloggningen
- Anrop `Signout()`, som låter OpenIdConnect mellanprogram kontakta den Microsoft Identity Platform `logout` -slutpunkt som:

  - Tar bort sessions-cookien från webbläsaren och
  - anropar slutligen återställnings- **URL: en**som standard visar sidan signerad vy som är signerad [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) som en del av ASP.net Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.net utlöses utloggning från `SignOut()` -metoden på en kontrollant (till exempel [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Den här metoden är inte en del av ASP.NET-ramverket (som strider mot vad som händer i ASP.NET Core). Företaget

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

I Java hanteras utloggningen genom att anropa slut punkten för Microsoft Identity Platform-utloggning direkt och tillhandahålla post_logout_redirect_uri. Mer information finns i [AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

Koden som loggar ut användaren finns i [appen. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
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
