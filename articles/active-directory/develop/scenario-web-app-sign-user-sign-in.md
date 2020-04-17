---
title: Skriv en webbapp som loggar in/ut användare - Microsoft identity platform | Azure
description: Läs om hur du skapar en webbapp som loggar in/ut användare
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2ab5697ceff612e65174fdb7f9ef6137e2c8b9a5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537074"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Webbapp som loggar in användare: Logga in och logga ut

Läs om hur du lägger till inloggning i koden för din webbapp som loggar in användare. Lär dig sedan hur du låter dem logga ut.

## <a name="sign-in"></a>Logga in

Inloggning består av två delar:

- Inloggningsknappen på HTML-sidan
- Inloggningsåtgärden i den bakomkoda i handkontrollen

### <a name="sign-in-button"></a>Knappen Logga in

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core visas inloggningsknappen i `Views\Shared\_LoginPartial.cshtml`. Den visas bara när det inte finns något autentiserat konto. Det vill än, det visas när användaren ännu inte har loggat in eller har loggat ut.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET MVC visas ut logga ut-knappen `Views\Shared\_LoginPartial.cshtml`i . Den visas bara när det finns ett autentiserat konto. Det vill än, det visas när användaren tidigare har loggat in.

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

# <a name="java"></a>[Java](#tab/java)

I vår Java-snabbstart finns inloggningsknappen i [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) filen.

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

# <a name="python"></a>[Python](#tab/python)

I snabbstarten Python finns ingen inloggningsknapp. Bakom koden uppmanas automatiskt användaren att logga in när den når webbappens rot. Se [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`styrenhetens agerande

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Om du ASP.NET utlöser du åtgärden på handkontrollen om `SignIn` du `AccountController` väljer knappen Logga **in i** webbappen. I tidigare versioner av de ASP.NET kärnmallarna har `Account` handkontrollen bäddats in med webbappen. Så är inte längre fallet eftersom den registeransvarige nu är en del av ASP.NET Core-ramverket.

Koden för `AccountController` är tillgänglig från ASP.NET Core-databasen i [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontokontrollen utmanar användaren genom att omdirigera till slutpunkten för Microsoft-identitetsplattformen. Mer information finns i [SignIn-metoden](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) som tillhandahålls som en del av ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET utlöses utskrivning från `SignOut()` metoden på en styrenhet (till exempel [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Denna metod är inte en del av ASP.NET ram (i motsats till vad som händer i ASP.NET Core). Den skickar en OpenID-inloggningsutmaning efter att ha föreslagit en omdirigera URI.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

I Java hanteras ut logga ut genom `logout` att anropa slutpunkten för Microsoft-identitetsplattformen `post_logout_redirect_uri` direkt och ge värdet. Mer information finns i [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

# <a name="python"></a>[Python](#tab/python)

Till skillnad från andra plattformar tar MSAL Python hand om att låta användaren logga in från inloggningssidan. Se [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Metoden `_build_msal_app()` definieras i [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) enligt följande:

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
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

När användaren har loggat in på din app vill du att de ska logga ut.

## <a name="sign-out"></a>Logga ut

Att logga ut från en webbapp innebär mer än att ta bort informationen om det inloggade kontot från webbappens tillstånd.
Webbappen måste också omdirigera användaren till `logout` slutpunkten för Microsoft-identitetsplattformen för att kunna logga ut.

När webbappen omdirigerar användaren `logout` till slutpunkten rensar den här slutpunkten användarens session från webbläsaren. Om appen inte gick till `logout` slutpunkten kommer användaren att oma till din app utan att ange sina autentiseringsuppgifter igen. Anledningen är att de har en giltig enstaka inloggningssession med slutpunkten för Microsoft-identitetsplattformen.

Mer information finns i avsnittet [Skicka en uttiseringsbegäran](v2-protocols-oidc.md#send-a-sign-out-request) i [Microsofts identitetsplattform och Dokumentationen till OpenID Connect-protokollet.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Programregistrering

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Under ansökningsregistreringen registrerar du en post-logout URI. I vår handledning `https://localhost:44321/signout-oidc` registrerade du dig i fältet **Utloggningsadress** i avsnittet **Avancerade inställningar** på **sidan Autentisering.** Mer information finns i [Registrera webApp-appen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Under ansökningsregistreringen registrerar du en post-logout URI. I vår handledning `https://localhost:44308/Account/EndSession` registrerade du dig i fältet **Utloggningsadress** i avsnittet **Avancerade inställningar** på **sidan Autentisering.** Mer information finns i [Registrera webApp-appen](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Under ansökningsregistreringen registrerar du en post-logout URI. I vår handledning `http://localhost:8080/msal4jsample/sign_out` registrerade du dig i fältet **Utloggningsadress** i avsnittet **Avancerade inställningar** på **sidan Autentisering.**

# <a name="python"></a>[Python](#tab/python)

Under ansökningsregistreringen behöver du inte registrera en extra utloggningsadress. Appen kommer att anropas tillbaka på sin huvudadress.

---

### <a name="sign-out-button"></a>Knappen Ut signering

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core visas ut logga utknappen `Views\Shared\_LoginPartial.cshtml`i . Den visas bara när det finns ett autentiserat konto. Det vill än, det visas när användaren tidigare har loggat in.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET MVC visas ut logga ut-knappen `Views\Shared\_LoginPartial.cshtml`i . Den visas bara när det finns ett autentiserat konto. Det vill än, det visas när användaren tidigare har loggat in.

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

# <a name="java"></a>[Java](#tab/java)

I vår Java-snabbstart finns ut logga ut-knappen i main/resources/templates/auth_page.html-filen.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

I snabbstarten Python finns ut logga ut-knappen i [filen templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`styrenhetens agerande

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Om du ASP.NET utlöser om du väljer **knappen Logga** ut `SignOut` i `AccountController` webbappen åtgärden på handkontrollen. I tidigare versioner av mallarna ASP.NET Core `Account` har handkontrollen bäddats in med webbappen. Så är inte längre fallet eftersom den registeransvarige nu är en del av ASP.NET Core-ramverket.

Koden för `AccountController` är tillgänglig från ASP.NET kärndatabasen i [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontokontrollen:

- Ställer in en OpenID-omdirigerings-URI så `/Account/SignedOut` att styrenheten anropas tillbaka när Azure AD har slutfört ut logga utet.
- Anrop `Signout()`, som gör att OpenID Connect `logout` mellanprogram kontaktar slutpunkten för Microsoft identity platform. Slutpunkten då:

  - Rensar sessionscookien från webbläsaren.
  - Anropar webbadressen för utloggning. Som standard visar utloggnings-URL:en den utloggade visningssidan [Signerad.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Den här sidan finns också som en del av ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET utlöses utskrivning från `SignOut()` metoden på en styrenhet (till exempel [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Denna metod är inte en del av ASP.NET ram, i motsats till vad som händer i ASP.NET Core. Det:

- Skickar en OpenID-ut signeringsutmaning.
- Rensar cacheminnet.
- Omdirigerar till den sida som den vill ha.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

I Java hanteras ut logga ut genom `logout` att anropa slutpunkten för Microsoft-identitetsplattformen `post_logout_redirect_uri` direkt och ge värdet. Mer information finns i [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

# <a name="python"></a>[Python](#tab/python)

Koden som signerar användaren finns i [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Avlyssna samtalet till `logout` slutpunkten

URI:n efter utloggning gör det möjligt för program att delta i den globala utloggningen.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Med ASP.NET Core OpenID Connect-mellanprogram kan appen avlyssna anropet `logout` till slutpunkten för Microsoft-identitetsplattformen genom att tillhandahålla en OpenID Connect-händelse med namnet `OnRedirectToIdentityProviderForSignOut`. Ett exempel på hur du prenumererar på den här händelsen (för att rensa tokencachen) finns i [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET delegerar du till mellanprogram för att köra ut logga ut, rensa sessionscookien:

```csharp
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

# <a name="java"></a>[Java](#tab/java)

I Snabbstarten för Java visar URI-omdirigeran efter utloggning bara index.html-sidan.

# <a name="python"></a>[Python](#tab/python)

I snabbstarten för Python visar URI-snabbinriktningen efter utloggning bara index.html-sidan.

---

## <a name="protocol"></a>Protokoll

Om du vill veta mer om ut logga ut läser du protokolldokumentationen som är tillgänglig från [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-sign-user-production.md)
