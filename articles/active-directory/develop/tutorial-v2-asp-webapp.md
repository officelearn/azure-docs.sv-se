---
title: Komma igång med Azure AD v 2.0 ASP.NET-webbserver | Microsoft Docs
description: Implementera Microsoft-inloggning på en ASP.NET-lösning med ett traditionellt webbläsarbaserat program med OpenID Connect standard
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511976"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning med Microsoft till en ASP.NET-webbapp

Den här guiden visar hur du implementerar inloggning med Microsoft med en ASP.NET MVC-lösning med ett traditionellt webbläsarbaserat program med OpenID Connect.

I slutet av den här hand boken kommer ditt program att kunna acceptera inloggningar med personliga konton, till exempel outlook.com, live.com och andra. Dessa konton innehåller även arbets-och skol konton från alla företag eller organisationer som har integrerat med Azure Active Directory.

> Den här guiden kräver Visual Studio 2019.  Saknas det?  [Ladda ned Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Det exempel program som du skapar baseras på scenariot där du använder webbläsaren för att få åtkomst till en ASP.NET-webbplats som begär att en användare autentiseras via en inloggnings knapp. I det här scenariot sker merparten av renderingen av webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

I den här guiden används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program kan använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som gör att ett program kan hantera en användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program kan köras i IIS med hjälp av pipelinen för ASP.NET-förfrågningar|

## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet visas stegen för att installera och konfigurera pipeline för autentisering via OWIN mellan program i ett ASP.NET-projekt med OpenID Connect.

> Vill du hämta det här exemplets Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) och hoppa till [konfigurations steget](#register-your-application) för att konfigurera kod exemplet innan du kör.

### <a name="create-your-aspnet-project"></a>Skapa ASP.NET-projektet

1. I Visual Studio:`File` > `New` > `Project`
2. Under *visuell C#\Web*väljer `ASP.NET Web Application (.NET Framework)`du.
3. Namnge ditt program och klicka på *OK*
4. Markera `Empty` och markera kryss rutan för att `MVC` lägga till referenser

## <a name="add-authentication-components"></a>Lägga till autentiseringskomponenter

1. I Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Lägg till *NuGet-paket för OWIN-mellanprogram* genom att skriva följande i Package Manager-konsolfönstret:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Om dessa bibliotek
> Biblioteken ovan möjliggör enkel inloggning (SSO) med hjälp av OpenID Connect via cookie-baserad autentisering. När autentiseringen har slutförts och den token som representerar användaren skickas till ditt program, skapar OWIN-mellanprogrammet en sessions-cookie. Webbläsaren använder sedan denna cookie vid efterföljande begär Anden så att användaren inte behöver ange lösen ordet igen och ingen ytterligare verifiering krävs.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentiseringspipelinen

Stegen nedan används för att skapa en OWIN-mellanprogram start klass för att konfigurera OpenID Connect-autentisering. Den här klassen körs automatiskt när IIS-processen startas.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs`-fil i rotmappen:
> 1. Högerklicka på projektets rotmapp: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Ge den namnet `Startup.cs`
>
>> Kontrollera att den klass som valts är en OWIN-startklass och inte en C#-standardklass. Bekräfta detta genom att kontrollera om `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` visas ovanför namnområdet.

1. Lägg till *OWIN* -och *Microsoft. IdentityModel* -referenser till `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Ersätt start klass med koden nedan:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Inställningen `ValidateIssuer = false` är en förenkling för den här snabb starten. I verkliga program måste du verifiera utfärdaren i exemplen för att förstå hur du gör det.

<!--start-collapse-->
> ### <a name="more-information"></a>Mer information
> De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater som programmet använder för att kommunicera med Azure AD. Eftersom OpenID Connect-programvara använder cookies i bakgrunden, måste du också ställa in cookie-autentisering som koden ovan. Värdet *ValidateIssuer* instruerar OpenIdConnect att inte begränsa åtkomsten till en specifik organisation.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägga till en styrenhet för att hantera inloggnings- och utloggningsbegäranden

Det här steget visar hur du skapar en ny kontrollant för att exponera inloggnings-och utloggnings metoder.

1.  Högerklicka på `Controllers` mappen och välj`Add` > `Controller`
2.  Välj `MVC (.NET version) Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den `HomeController` namnet och klicka på *Lägg till*
5.  Lägg till *OWIN* -referenser till klassen:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Lägg till de två metoderna nedan för att hantera inloggning och logga ut till din styrenhet genom att initiera en autentiseringsbegäran via kod:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Skapa appens startsida för att logga in användare via en inloggningsknapp

Skapa en ny vy i Visual Studio för att lägga till inloggningsknappen och visa användarinformation efter autentiseringen:

1.  Högerklicka på `Views\Home` mappen och välj`Add View`
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
> Den här sidan lägger till en inloggnings knapp i SVG-format med svart bakgrund:<br/>![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Om du vill ha fler inloggnings knappar går du till [](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "rikt linjerna")för sid anpassning.
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Lägg till en kontrollant för att Visa användarens anspråk
Den här kontrollanten demonstrerar hur `[Authorize]`-attributet kan skydda en kontrollant. Det här attributet begränsar åtkomsten till kontrollanten genom att bara tillåta autentiserade användare. I koden nedan används attributet för att Visa användar anspråk som hämtades som en del av inloggningen.

1.  Högerklicka på `Controllers` mappen:`Add` > `Controller`
2.  Välj `MVC {version} Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `ClaimsController`
5.  Ersätt koden för din kontroll enhets klass med koden nedan – detta lägger `[Authorize]` till attributet i klassen:

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
> Eftersom attributet `[Authorize]` används kan metoderna för den här kontrollanten endast köras om användaren har autentiserats. Om användaren inte är autentiserad och försöker komma åt kontrollanten initierar OWIN en autentiserings-utmaning och tvingar användaren att autentisera sig. Koden ovan söker i listan över anspråk för vissa användarattribut som ingår i användarens ID-token. Dessa attribut är användarens fullständiga namn och användarnamn, samt objektidentifieraren för den globala användaren. Den innehåller också *klientorganisations-ID:t*, som representerar ID:t för användarens organisation. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy i Visual Studio för att visa användarens anspråk på en webbsida:

1.  Högerklicka på `Views\Claims` mappen och:`Add View`
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

## <a name="register-your-application"></a>Registrera ditt program

Om du vill registrera ditt program och lägga till program registrerings informationen i din lösning har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Express läge

Du kan snabbt registrera ditt program genom att göra följande:

1. Gå till fönstret ny [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Ange ett namn för programmet och klicka på **Registrera**.
1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:

1. Gå till Visual Studio och:
   1. i Solution Explorer väljer du projektet och tittar på Fönstret Egenskaper (om du inte ser en Fönstret Egenskaper trycker du på F4).
   1. Ändra SSL till `True`.
   1. Högerklicka på projektet i Visual Studio och välj sedan **Egenskaper**och fliken **webb** . I avsnittet *servrar* ändrar du *projekt-URL* : en till SSL-URL: en.
   1. Kopiera SSL-URL: en. Du kommer att lägga till denna URL i listan över omdirigerings-URL: er i registrerings portalens lista över omdirigerings-URL: er i nästa steg:<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `ASPNET-Tutorial`.
   1. Lägg till SSL-URL: en som du kopierade från Visual Studio i steg `https://localhost:44368/`1 (till exempel) i svars- **URL**och klicka på **Registrera**.
1. Välj **autentiseringsmenyn** och ange **ID-token** under **Implicit beviljande** och välj sedan **Spara**.
1. Lägg till följande i `web.config` mappen root i avsnittet: `configuration\appSettings`

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersätt `ClientId` med det program-ID som du precis har registrerat.
1. Ersätt `redirectUri` med SSL-URL: en för projektet.

## <a name="test-your-code"></a>Testa koden

Om du vill testa programmet i Visual Studio trycker du på **F5** för att köra projektet. Webbläsaren öppnas till platsen http://<span></span>localhost: {port} och du ser knappen **Logga in med Microsoft** . Klicka på knappen för att starta inloggnings processen.

När du är redo att köra testet använder du ett Microsoft Azure Active Directory (Azure AD)-konto (arbets-eller skol konto) eller en personlig Microsoft-konto (<span>Live.</span> com eller <span>Outlook.</span> com) för att logga in.

![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på din Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visa program resultat

När du har loggat in omdirigeras användaren till start sidan för din webbplats. Start sidan är HTTPS-URL: en som anges i din program registrerings information i registrerings portalen för Microsoft-program. Start sidan innehåller ett välkomst meddelande *" \<Hello user >",* en länk för att logga ut och en länk för att Visa användarens anspråk. Länken för användarens anspråk bläddrar till den anspråks kontroll som du skapade tidigare.

### <a name="browse-to-see-the-users-claims"></a>Bläddra för att se användarens anspråk

Om du vill se användarens anspråk väljer du länken för att bläddra till vyn kontrollant som endast är tillgänglig för autentiserade användare.

#### <a name="view-the-claims-results"></a>Visa anspråks resultatet

När du bläddrar till vyn kontrollant bör du se en tabell som innehåller grundläggande egenskaper för användaren:

|Egenskap |Value |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användarens för-och efter namn.
|**Användarnamn** |användarvänlig<span>@domain.com</span> | Det användar namn som används för att identifiera användaren.
|**Subject** |Subject |En sträng som unikt identifierar användaren på webben.|
|**Klient-ID** |Guid | Ett **GUID** som unikt representerar användarens Azure AD-organisation.|

Dessutom bör du se en tabell över alla anspråk som finns i autentiseringsbegäran. Mer information finns i [listan över anspråk som finns i en Azure AD ID-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har ett auktoriserat attribut (valfritt)

Följ dessa steg om du vill testa åtkomst som en anonym användare till `Authorize` en kontrollant som skyddas med attributet:

1. Välj länken för att logga ut användaren och slutföra inloggnings processen.
2. I webbläsaren skriver du http://<span></span>localhost: {port}/anspråk för att komma åt din kontrollant som skyddas med `Authorize` -attributet.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntade resultat efter åtkomst till en skyddad styrenhet

Du uppmanas att autentisera för att använda vyn skyddad kontrollant.

## <a name="advanced-options"></a>Avancerade alternativ

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Skydda hela webbplatsen
Om du vill skydda hela webbplatsen går du till filen **Global. asax** och lägger `AuthorizeAttribute` till attributet i `GlobalFilters` filtret i `Application_Start` metoden:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Begränsa vem som kan logga in på ditt program

Som standard när du skapar programmet som skapats av den här guiden, accepterar programmet inloggnings uppgifter för personliga konton (inklusive outlook.com, live.com och andra) samt arbets-och skol konton från alla företag eller organisationer som har integrerat med Azure Active Directory. Detta är ett rekommenderat alternativ för SaaS-program.

För att begränsa användarens inloggnings åtkomst för ditt program är flera alternativ tillgängliga:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Alternativ 1: Begränsa inloggningen till ditt program till en enda organisations Active Directory-instans (en enda innehavare)

Det här alternativet är ett vanligt scenario för *LOB-program*: Gör så här om du vill att programmet endast ska godkänna inloggningar från konton som tillhör en specifik Azure Active Directory instans (inklusive *gäst konton* för den instansen):

1. I filen **Web. config** ändrar du värdet för `Tenant` parametern från `Common` till organisationens organisations `contoso.onmicrosoft.com`namn, till exempel.
2. I din [OWIN](#configure-the-authentication-pipeline)-startklass anger `ValidateIssuer` du argumentet till `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Alternativ 2: Begränsa åtkomsten till ditt program till användare i en specifik lista över organisationer

Du kan begränsa inloggnings åtkomst till användar konton som finns i en Azure AD-organisation som finns i listan över tillåtna organisationer:
1. I din [OWIN](#configure-the-authentication-pipeline)-startklass anger `ValidateIssuer` du argumentet till `true`.
2. Ange värdet för `ValidIssuers` parametern till listan över tillåtna organisationer.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Alternativ 3: Använd en anpassad metod för att validera utfärdare

Du kan implementera en anpassad metod för att verifiera utfärdare med hjälp av parametern **IssuerValidator** . Mer information om hur du använder den här parametern finns i om [klassen TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om webb program som anropar webb-API: er:.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig stegen för hur du skapar det program som används i den här snabbstarten

> [!div class="nextstepaction"]
> [Webb program som anropar webb-API: er]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)