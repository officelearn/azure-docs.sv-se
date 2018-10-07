---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 167fccd8e0546bc8f5ac1b24489cae68cc14191f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843517"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägga till en styrenhet för att hantera inloggning och utloggning begäranden

Det här steget visar hur du skapar en ny kontrollant som exponerar metoder för inloggning och utloggning.

1.  Högerklicka på den `Controllers` mappen och välj `Add` > `Controller`
2.  Välj `MVC (.NET version) Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `HomeController` och klicka på *Lägg till*
5.  Lägg till *OWIN* referenser till klassen:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Lägg till de två metoderna nedan för att hantera inloggning och utloggning i din kontrollant genom att initiera en autentiseringsfråga via kod:
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Skapa appens startsida för inloggning av användare via en knapp för inloggning

Skapa en ny vy i Visual Studio för att lägga till inloggningsknappen och visa användarinformation efter autentiseringen:

1.  Högerklicka på den `Views\Home` mappen och välj `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML-kod, som innehåller inloggningsknappen, i filen:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Mer information
> Den här sidan lägger till en knapp för inloggning i SVG-format med en svart bakgrund:<br/>![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> För mer inloggning knapparna går du till den [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "riktlinjer för anpassning").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Lägg till en domänkontrollant om du vill visa användarens anspråk
Den här kontrollanten demonstrerar hur `[Authorize]`-attributet kan skydda en kontrollant. Det här attributet begränsar åtkomsten till kontrollanten genom att bara tillåta autentiserade användare. Koden nedan använder attributet för att visa användaranspråk som hämtades som en del av inloggningen.

1.  Högerklicka på den `Controllers` mapp: `Add` > `Controller`
2.  Välj `MVC {version} Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `ClaimsController`
5.  Ersätt Koden i din kontrollantklassen med koden nedan – detta lägger till den `[Authorize]` attributet klassen:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Mer information
> Eftersom attributet `[Authorize]` används kan metoderna för den här kontrollanten endast köras om användaren har autentiserats. Om användaren inte har autentiserats och försöker få åtkomst till kontrollanten, initiera en autentiseringsfråga OWIN och tvinga användaren att autentisera. Koden ovan tittar på listan över anspråk för specifika användarattribut som ingår i användarens Id-token. Dessa attribut är användarens fullständiga namn och användarnamn, samt objektidentifieraren för den globala användaren. Den innehåller också *klientorganisations-ID:t*, som representerar ID:t för användarens organisation. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy i Visual Studio för att visa användarens anspråk på en webbsida:

1.  Högerklicka på den `Views\Claims` mappen och: `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML-kod i filen:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
