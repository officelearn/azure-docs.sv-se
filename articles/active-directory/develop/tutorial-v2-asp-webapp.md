---
title: Lägga till inloggning på Microsofts identitetsplattform ASP.NET webbapp
titleSuffix: Microsoft identity platform
description: Implementera Microsoft-inloggning på en ASP.NET lösning med hjälp av ett traditionellt webbläsarbaserat program och OpenID Connect-standard
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 94d3993c6a0c62a68ea77a888d3351c8fea1d935
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990998"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning i Microsoft i en ASP.NET webbapp

Den här guiden visar hur du implementerar inloggning till Microsoft via en ASP.NET MVC-lösning med hjälp av ett traditionellt webbläsarbaserat program och OpenID Connect.

När du har slutfört den här guiden kan din ansökan acceptera inloggningar av personliga konton från outlook.com och live.com. Dessutom kan arbets- och skolkonton från alla företag eller organisationer som är integrerade med Microsofts identitetsplattform logga in på din app.

> Den här guiden kräver Microsoft Visual Studio 2019.  Saknas det?  [Ladda ner Visual Studio 2019 gratis.](https://www.visualstudio.com/downloads/)

>[!NOTE]
> Om du inte har en ny till Microsofts identitetsplattform rekommenderar vi att du börjar med [inloggningen till En ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)för Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelappen som genereras av den här guiden

![Visar hur exempelappen som genereras av den här självstudien fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Exempelprogrammet som du skapar baseras på ett scenario där du använder webbläsaren för att komma åt en ASP.NET webbplats som uppmanar en användare att autentisera via en inloggningsknapp. I det här scenariot sker merparten av renderingen av webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

I den här guiden används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program kan använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware som gör det möjligt för ett program att underhålla en användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware som gör det möjligt för OWIN-baserade program att köras på Internet Information Services (IIS) med hjälp av ASP.NET begäran pipeline|

## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet beskrivs hur du installerar och konfigurerar autentiseringspipelinen via OWIN-mellanprogram i ett ASP.NET projekt med hjälp av OpenID Connect.

> Föredrar du att ladda ned det här exemplets Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) och hoppa till [registrera programmet](#register-your-application) för att konfigurera kodexemplet innan du kör.

### <a name="create-your-aspnet-project"></a>Skapa ASP.NET-projektet

1. I Visual Studio: Gå till **fil** > **nytt** > **projekt**.
2. Under **Visual C#\Web** väljer du **ASP.NET-webbprogram (.NET Framework)**.
3. Namnge ditt program och välj **OK**.
4. Markera **Tom**och markera kryssrutan om du vill lägga till **MVC-referenser.**

## <a name="add-authentication-components"></a>Lägga till autentiseringskomponenter

1. I Visual Studio: Gå till **Tools** > **Nuget Package Manager** > **Package Manager Console**.
2. Lägg till *NuGet-paket för OWIN-mellanprogram* genom att skriva följande i Package Manager-konsolfönstret:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Om dessa bibliotek
> Dessa bibliotek möjliggör enkel inloggning (SSO) med hjälp av OpenID Connect via cookie-baserad autentisering. När autentiseringen har slutförts och den token som representerar användaren skickas till ditt program, skapar OWIN-mellanprogrammet en sessions-cookie. Webbläsaren använder sedan den här cookien på efterföljande begäranden så att användaren inte behöver skriva lösenordet igen och ingen ytterligare verifiering behövs.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentiseringspipelinen

Följande steg används för att skapa en OWIN-middleware-startklass för att konfigurera OpenID Connect-autentisering. Den här klassen körs automatiskt när din IIS-process startar.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs`-fil i rotmappen:
> 1. Högerklicka på projektets rotmapp och välj sedan **Lägg till** > **OWIN-startklass****för nytt objekt** > .<br/>
> 2. Nämn **den Startup.cs**.
>
>> Kontrollera att den valda klassen är en OWIN-startklass och inte en standardklass C#. Bekräfta detta genom att kontrollera att du ser [sammansättning: OwinStartup(typeof({NameSpace}. Start))] ovanför namnområdet.

1. Lägg till *OWIN-* och *Microsoft.IdentityModel-referenser* till Startup.cs:

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

2. Ersätt startklassen med följande kod:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
> Inställningen `ValidateIssuer = false` är en förenkling för den här snabbstarten. I riktiga program måste du validera utfärdaren.
> Se proverna för att lära dig hur man gör det.

<!--start-collapse-->
> ### <a name="more-information"></a>Mer information
> De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för att programmet ska kommunicera med Microsofts identitetsplattform. Eftersom OpenID Connect middleware använder cookies i bakgrunden måste du också ställa in cookie-autentisering som den föregående koden visar. Värdet *ValidateIssuer* talar om för OpenIdConnect att inte begränsa åtkomsten till en viss organisation.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägga till en styrenhet för att hantera inloggnings- och utloggningsbegäranden

Så här skapar du en ny styrenhet för att exponera inloggnings- och ut logga utmetoder:

1.  Högerklicka på mappen **Controllers** och välj **Lägg till** > **styrenhet**.
2.  Välj **MVC-kontrollant (.NET-version) – tom**.
3.  Välj **Lägg till**.
4.  Ge den namnet **HomeController** och välj sedan **Lägg till**.
5.  Lägg till OWIN-referenser till klassen:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Lägg till följande två metoder för att hantera inloggning och ut logga ut till handkontrollen genom att initiera en autentiseringsutmaning:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Skapa appens startsida för användarinloggning

I Visual Studio skapar du en ny vy för att lägga till inloggningsknappen och visa användarinformation efter autentisering:

1.  Högerklicka på mappen **Views\Home** och välj **Lägg till vy**.
2.  Ge den nya vyn **Index namn**.
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
>  Den här sidan lägger till en inloggningsknapp i SVG-format med en svart bakgrund:<br/>![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Fler inloggningsknappar finns i [riktlinjerna för branding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Riktlinjer för anpassning").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Lägga till en handkontroll för att visa användarens anspråk
Den här kontrollanten demonstrerar hur `[Authorize]`-attributet kan skydda en kontrollant. Det här attributet begränsar åtkomsten till styrenheten genom att endast tillåta autentiserade användare. Följande kod använder attributet för att visa användaranspråk som har hämtats som en del av inloggningen:

1.  Högerklicka på mappen **Controllers** och välj sedan **Lägg till** > **styrenhet**.
2.  Välj **MVC-kontrollant {version} – tom**.
3.  Välj **Lägg till**.
4.  Ge den namnet **ClaimsController**.
5.  Ersätt koden för styrenheten med följande kod. Detta lägger till `[Authorize]` attributet i klassen:

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
> På grund av `[Authorize]` attributets användning kan alla metoder för den här styrenheten endast köras om användaren autentiseras. Om användaren inte autentiseras och försöker komma åt styrenheten initierar OWIN en autentiseringsutmaning och tvingar användaren att autentisera. Den föregående koden tittar på listan över anspråk för specifika användarattribut som ingår i användarens ID-token. Dessa attribut är användarens fullständiga namn och användarnamn, samt objektidentifieraren för den globala användaren. Den innehåller också *klientorganisations-ID:t*, som representerar ID:t för användarens organisation. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy i Visual Studio för att visa användarens anspråk på en webbsida:

1.  Högerklicka på mappen **Vyer\Anspråk** och välj sedan **Lägg till vy**.
2.  Ge den nya vyn **Index namn**.
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

Om du vill registrera din ansökan och lägga till din ansökan registreringsinformation till din lösning, har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Expressläge

Så här registrerar du programmet snabbt:

1. Gå till den nya [Azure-portalen - fönstret Appregistreringar.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)
1. Ange ett namn för programmet och välj **Registrera**.
1. Följ instruktionerna för att ladda ner och konfigurera ditt nya program automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:

1. Öppna Visual Studio och sedan:
   1. I Solution Explorer markerar du projektet och visar fönstret Egenskaper (om du inte ser ett egenskapsfönster trycker du på F4).
   1. Ändra SSL Aktiverat `True`till .
   1. Högerklicka på projektet i Visual Studio, välj **Egenskaper**och välj sedan **fliken Webb.** Ändra inställningen **Project Url** till **SSL-URL**i avsnittet **Servrar** .
   1. Kopiera SSL-URL:en. Du lägger till den här URL:en i listan över omdirigeringsadresser i registreringsportalens lista över omdirigeringsadresser i nästa steg.<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av ett arbets- eller skolkonto eller med hjälp av ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till mer än en klient väljer du ditt konto i det övre högra hörnet och anger din portalsession till den Azure AD-klientorganisation som du vill ha.
1. Gå till sidan Microsoft identity platform for developers [App registrations.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Välj **Ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen, till exempel **ASPNET-Tutorial**.
   1. Lägg till SSL-URL:en som du kopierade från `https://localhost:44368/`Visual Studio i steg 1 (till exempel) i **Svars-URL**och välj **Registrera**.
1. Välj **autentiseringsmenyn,** välj **ID-token** under **Implicit beviljande**och välj sedan **Spara**.
1. Lägg till följande i filen web.config, som `configuration\appSettings` finns i rotmappen i avsnittet:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersätt `ClientId` med det program-ID som du just registrerade.
1. Ersätt `redirectUri` med SSL-URL:en för projektet.

## <a name="test-your-code"></a>Testa koden

Om du vill testa programmet i Visual Studio trycker du på F5 för att köra projektet. Webbläsaren öppnas till http://<span></span>lokalhost:{port} plats och du ser knappen Logga in med **Microsoft.** Välj knappen för att starta inloggningsprocessen.

När du är redo att köra testet använder du ett Azure AD-konto (arbets- eller skolkonto) eller ett personligt Microsoft-konto (<span>live.</span> com eller <span>outlook.</span> com) för att logga in.

![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på ditt Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Behörigheter och medgivande i slutpunkten för Microsoft-identitetsplattform
>  Program som integreras med Microsofts identitetsplattform följer en auktoriseringsmodell som ger användare och administratörer kontroll över hur data kan nås. När en användare autentiserar med Microsofts identitetsplattform för att komma åt det här programmet uppmanas de att godkänna de behörigheter som begärs av programmet ("Visa din grundläggande profil" och "Underhåll åtkomst till data som du har gett den åtkomst till"). När du har accepterat dessa behörigheter fortsätter användaren till programresultaten. Användaren kan dock i stället uppmanas med en **sida för godkännande av behovsadministratör** om något av följande inträffar:
>  > - Programutvecklaren lägger till ytterligare behörigheter som kräver **administratörsgodkännande**.
>  > - Eller klienten är konfigurerad (i **Enterprise Applications -> Användarinställningar)** där användare inte kan samtycka till appar som använder företagsdata för deras räkning.
>
> Mer information finns [i Behörigheter och medgivande i slutpunkten för Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Visa programresultat

När du har loggat in omdirigeras användaren till webbplatsens startsida. Startsidan är den HTTPS-URL som anges i din programregistreringsinformation i Microsoft Application Registration Portal. Startsidan innehåller ett *välkomstmeddelande "Hej \<användare>",* en länk för att logga ut och en länk för att visa användarens anspråk. Länken för användarens anspråk ansluter till den anspråkskontrollant som du skapade tidigare.

### <a name="view-the-users-claims"></a>Visa användarens anspråk

Om du vill visa användarens anspråk väljer du länken för att bläddra till den controller-vy som bara är tillgänglig för autentiserade användare.

#### <a name="view-the-claims-results"></a>Visa anspråksresultaten

När du har bläddrat till kontrollantvyn bör du se en tabell som innehåller de grundläggande egenskaperna för användaren:

|Egenskap |Värde |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användarens förnamn och efternamn
|**Användarnamn** |Användaren<span>@domain.com</span> | Användarnamnet som används för att identifiera användaren|
|**Subjekt** |Subjekt |En sträng som unikt identifierar användaren på webben|
|**Klient-ID:t** |GUID | Ett **guid** som unikt representerar användarens Azure AD-organisation|

Dessutom bör du se en tabell över alla anspråk som finns i autentiseringsbegäran. Mer information finns [i listan över anspråk som finns i en ID-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har ett auktoriserat attribut (valfritt)

Så här testar du åtkomst som anonym användare till `Authorize` en styrenhet som skyddas av attributet:

1. Välj länken för att logga ut användaren och slutför utloggningsprocessen.
2. Skriv http://<span></span>localhost:{port}/claims i webbläsaren för att komma åt den `Authorize` styrenhet som skyddas av attributet.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntade resultat efter åtkomst till en skyddad styrenhet

Du uppmanas att autentisera för att använda den skyddade styrenhetsvyn.

## <a name="advanced-options"></a>Avancerade alternativ

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Skydda hela din webbplats
Om du vill skydda hela webbplatsen lägger du `AuthorizeAttribute` till `GlobalFilters` attributet `Application_Start` i filtret i metoden i filen **Global.asax:**

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Begränsa vem som kan logga in på ditt program

Som standard när du skapar programmet som skapats av den här guiden accepterar programmet inloggningar av personliga konton (inklusive outlook.com, live.com och andra) samt arbets- och skolkonton från alla företag eller organisationer som är integrerade med Microsofts identitetsplattform. Detta är ett rekommenderat alternativ för SaaS-program.

Om du vill begränsa användaråtkomsten för ditt program är flera alternativ tillgängliga.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Alternativ 1: Begränsa användare från endast en organisations Active Directory-instans att logga in på ditt program (en enda klient)

Det här alternativet används ofta för *LOB-program:* Om du vill att ditt program endast ska acceptera inloggningar från konton som tillhör en viss Azure AD-instans (inklusive *gästkonton* för den instansen) gör du så här:

1. I filen web.config ändrar du `Tenant` värdet `Common` för parametern från till `contoso.onmicrosoft.com`organisationens klientnamn, till exempel .
2. I [klassen OWIN Startup](#configure-the-authentication-pipeline) `ValidateIssuer` anger `true`du argumentet till .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Alternativ 2: Begränsa åtkomsten till användare i en viss lista över organisationer

Du kan begränsa inloggningsåtkomsten till endast de användarkonton som finns i en Azure AD-organisation som finns med i listan över tillåtna organisationer:
1. I [klassen OWIN Startup](#configure-the-authentication-pipeline) `ValidateIssuer` anger `true`du argumentet till .
2. Ange värdet för `ValidIssuers` parametern till listan över tillåtna organisationer.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Alternativ 3: Använd en anpassad metod för att validera emittenter

Du kan implementera en anpassad metod för att validera utfärdare med parametern **IssuerValidator.** Mer information om hur du använder den här parametern finns i [klassen TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur webbappar kan anropa webb-API:er.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Lär dig hur du skapar programmet som används i den här snabbstartsguiden

Läs mer om webbappar som anropar webb-API:er med Microsofts identitetsplattform:

> [!div class="nextstepaction"]
> [Webbappar som anropar webb-API:er](scenario-web-app-sign-user-overview.md)

Lär dig hur du skapar webbappar som anropar Microsoft Graph:

> [!div class="nextstepaction"]
> [Självstudiekurs för Microsoft Graph ASP.NET](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
