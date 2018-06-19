---
title: ta med fil
description: ta med fil
services: active-directory
author: andretms
ms.service: active-directory
ms.topic: include
ms.date: 05/08/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 9c7daf7bc947b08835148f6d09c58b47c9e0186b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "36204928"
---
## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet visar hur du installerar och konfigurerar autentisering pipeline via OWIN mellanprogram på en ASP.NET-projekt med OpenID Connect. 

> Om du vill hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) och gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan du kör.

### <a name="create-your-aspnet-project"></a>Skapa din ASP.NET-projekt

1. I Visual Studio: `File` > `New` > `Project`
2. Under *Visual C# \Web*väljer `ASP.NET Web Application (.NET Framework)`.
3. Namnge ditt program och klicka på *OK*
4. Välj `Empty` och markera kryssrutan för att lägga till `MVC` referenser

## <a name="add-authentication-components"></a>Lägga till autentiseringskomponenter

1. I Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Lägg till *OWIN mellanprogram NuGet-paket* genom att skriva följande i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Om dessa bibliotek
>Bibliotek ovan aktivera enkel inloggning (SSO) med OpenID Connect via cookie-baserad autentisering. När autentiseringen är slutförd och token som representerar användaren skickas till ditt program, skapar OWIN mellanprogram sessions-cookie. Webbläsaren använder sedan cookien för efterföljande förfrågningar så att användaren inte behöver ange lösenordet igen och ingen ytterligare verifiering krävs.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentisering pipeline
Stegen nedan för att skapa ett mellanprogram för OWIN-startklass konfigurera OpenID Connect autentisering. Den här klassen kommer att köras automatiskt när IIS-processen startar.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs` filen i rotmappen:
> 1. Högerklicka på projektets rotmapp: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Ge den namnet `Startup.cs`
>
>> Kontrollera att den klass som valts är en OWIN-startklass och inte en standard C#-klass. Kontrollera detta genom att kontrollera om du ser `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` ovanför namnområdet.

1. Lägg till *OWIN* och *Microsoft.IdentityModel* referenser till `Startup.cs`:

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

2. Ersätt startklass med koden nedan:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                        ValidateIssuer = false
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

<!--start-collapse-->
> ### <a name="more-information"></a>Mer information
> De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för programmet som ska kommunicera med Azure AD. Eftersom OpenID Connect mellanprogram använder cookies i bakgrunden, måste du också konfigurera cookie-autentisering som koden ovan visas. Den *ValidateIssuer* värde anger OpenIdConnect inte begränsa åtkomsten till en specifik organisation.
<!--end-collapse-->

