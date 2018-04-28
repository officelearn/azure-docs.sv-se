---
title: Azure AD v2.0 .NET web app inloggning komma igång | Microsoft Docs
description: Hur du skapar en .NET MVC-Webbapp som loggar användarna in med både personliga Account och arbets-eller skolkonton.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a23b3b1084cf6776cee8583891ae3d879183d072
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2018
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Lägga till inloggning till en .NET MVC-webbapp
Du kan snabbt lägga till autentisering till dina webbappar med stöd för både personliga Microsoft-konton och arbets-eller skolkonton med v2.0-slutpunkten.  I ASP.NET-webbprogram, kan du göra detta med hjälp av Microsofts OWIN mellanprogram som ingår i .NET Framework 4.5.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

 Här ska vi skapa en webbapp som använder OWIN för att logga in användaren, visa viss information om användaren och logga ut från appen användaren.

## <a name="download"></a>Ladda ned
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Den färdiga appen finns i slutet av den här kursen samt.

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa dessa [detaljerade steg](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** tilldelats din app måste den snart.
* Lägg till den **Web** plattform för din app.
* Ange rätt **omdirigerings-URI**. Omdirigerings-uri anger till Azure AD där autentisering svar som ska dirigeras - standarden för den här kursen är `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installera och konfigurera OWIN-autentisering
Här kan konfigurerar vi OWIN-mellanprogrammet att använda autentiseringsprotokollet OpenID Connect.  OWIN används för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarens session och få information om användare, bland annat.

1. Öppna först den `web.config` filen i roten av projektet och ange din Apps konfigurationsvärden i den `<appSettings>` avsnitt.

  * Den `ida:ClientId` är den **program-Id** tilldelats din app i portalen för registrering.
  * Den `ida:RedirectUri` är den **omdirigerings-Uri** du angav på portalen.

2. Lägg sedan till OWIN mellanprogram NuGet-paket i projektet med hjälp av Package Manager-konsolen.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Lägg till en ”OWIN-startklass” projektet kallas `Startup.cs` höger klickar du på projektet--> **Lägg till** --> **nytt objekt** --> Sök efter ”OWIN”.  OWIN-mellanprogrammet anropar `Configuration(...)`-metoden när appen startas.
4. Ändra klassdeklarationen till `public partial class Startup` -vi har implementerat en del av den här klassen som du redan i en annan fil.  I den `Configuration(...)` metod, gör ett anrop till ConfigureAuth(...) du konfigurerar autentisering för ditt webbprogram  

        ```csharp
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Öppna filen `App_Start\Startup.Auth.cs` och genomföra den `ConfigureAuth(...)` metoden.  De parametrar som du anger i `OpenIdConnectAuthenticationOptions` fungerar som koordinater för din app för att kommunicera med Azure AD.  Du måste också konfigurera Cookie-autentisering - mellanprogram OpenID Connect använder cookies djupare.

        ```csharp
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden
Appen har nu konfigurerats korrekt för att kommunicera med v2.0-slutpunkten med hjälp av autentiseringsprotokollet OpenID Connect.  OWIN har tagit hand om alla ugly detaljer utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessioner.  Allt som återstår är att ge användarna ett sätt att logga in och logga ut.

- Du kan använda auktorisera taggar i dina domänkontrollanter kräver att användaren loggar in innan du använder en viss sida.  Öppna `Controllers\HomeController.cs`, och Lägg till den `[Authorize]` tagg om domänkontrollanten.
        
        ```csharp
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- Du kan också använda OWIN för att skicka autentiseringsbegäranden från direkt i din kod.  Öppna `Controllers\AccountController.cs`.  I SignIn() och SignOut() åtgärder, utfärda du OpenID Connect challenge respektive utloggning begäranden.

        ```csharp
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- Öppna nu `Views\Shared\_LoginPartial.cshtml`.  Detta är där du visar användaren appens inloggning och utloggning länkar och skriva ut användarens namn i en vy.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## <a name="display-user-information"></a>Visa användarinformation
När du autentiserar användare med OpenID Connect returnerar ett id_token till appen som innehåller anspråk eller intyg om användaren v2.0-slutpunkten.  Du kan använda dessa anspråk för att anpassa din app:

- Öppna filen `Controllers\HomeController.cs`.  Du kan komma åt användarens anspråk i din domänkontrollanter via den `ClaimsPrincipal.Current` säkerhetsobjekt.

        ```csharp
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Kör
Slutligen skapar och kör appen!   Logga in med ett personligt Microsoft-Account eller ett arbets- eller skolkonto konto och hur användarens identitet visas i det övre navigeringsfältet.  Nu har du en webbapp som kan skyddas med standardprotokollen som kan autentisera användare med både sina personliga och arbete/skola konton.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) [har angetts som en .zip här](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), eller kan du klona den från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till mer avancerade avsnitt.  Du kanske vill prova:

[Skydda ett webb-API med den v2.0-slutpunkten >>](active-directory-devquickstarts-webapi-dotnet.md)

För ytterligare resurser, kolla:

* [Utvecklarhandbok v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow ”azure-active-directory” taggen >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.
