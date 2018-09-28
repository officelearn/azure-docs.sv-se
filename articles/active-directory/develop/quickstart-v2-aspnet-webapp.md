---
title: 'Snabbstart: Azure AD v2.0 ASP.NET-webbserver | Microsoft Docs'
description: Lär dig hur du implementerar Microsoft-inloggning på en ASP.NET-webbapp med OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7196cd6c18680583494a485c136081d90960464d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991291"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Snabbstart: Lägga till inloggning med Microsoft till en ASP.NET-webbapp

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

I den här snabbstarten lär du dig hur en ASP.NET-webbapp kan logga in personliga konton (hotmail.com, outlook.com osv.) och arbets- och skolkonton från alla instanser av Active Directory (Azure AD).

![Så här fungerar exempelappen som skapats med snabbstarten](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Registrera appen och ladda ned snabbstartsappen
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrera och konfigurera appen och kodexemplet
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera din app
> 
> 1. Gå till [Microsoft-portalen för appregistrering](https://apps.dev.microsoft.com/portal/register-app).
> 1. Ange namnet på din app, kontrollera att alternativet **Guided Setup** (Interaktiv installation) är avmarkerat och klicka på **Create** (Skapa).
> 1. Klicka på `Add Platform` och välj sedan `Web`.
> 1. Kontrollera att **Allow Implicit Flow** (Tillåt implicit flöde) är *markerat*.
> 1. I **Redirect URLs** (Omdirigerings-URL:er) anger du `https://localhost:44368/`.
> 1. Rulla längst ned på sidan och klicka på **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kodexemplet för den här snabbstarten ska fungera måste vi lägga till en svars-URL som `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Appen har konfigurerats med det här attributet

#### <a name="step-2-download-your-project"></a>Steg 2: Ladda ned ditt projekt

[Ladda ned Visual Studio 2017-lösningen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp (t.ex. **C:\Azure-Samples**)
1. Öppna lösningen i Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. Redigera **Web.config** och ersätt `Enter_the_Application_Id_here` med program-ID från appen du precis har registrerat:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    ```
    
> [!div class="sxs-lookup" renderon="portal"]
> [!IMPORTANT]
> Om appen är en *app för en innehavare* (och bara riktar in sig på konton i den här katalogen) i **Web.config**-filen letar du reda på värdet för `Tenant` och ersätter `common` med ditt **klientorganisations-ID** eller **klientorganisationsnamn** (t.ex. contoso.microsoft.com). Du kan hämta klientorganisationsnamnet på **översiktssidan**.

## <a name="more-information"></a>Mer information

I det här avsnittet får du en översikt över koden som krävs för att logga in användare. Det kan vara användbart för att förstå hur koden fungerar, huvudargumenten och också om du vill lägga till inloggning för en befintlig ASP.NET-app.

### <a name="owin-middleware-nuget-packages"></a>OWIN-mellanprogrammets NuGet-paket

Du kan konfigurera autentiserings-pipeline med cookiebaserad autentisering med hjälp av OpenID Connect i ASP.NET med paket för OWIN-mellanprogrammet. Du kan installera dessa paket genom att köra följande kommandon i **Package Manager-konsolen** i Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN-startklass

OWIN-mellanprogrammet använder en *startklass* som körs när värdprocessen initieras (i snabbstartens fall filen *startup.cs* som finns i rotmappen). Följande kod visar parametern som används av den här snabbstarten:

```csharp
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
```

> |Var  |  |
> |---------|---------|
> | `ClientId`     | Program-ID från appen som registrerats i Azure-portalen |
> | `Authority`    | STS-slutpunkten för autentisering av användaren. Vanligtvis https://login.microsoftonline.com/{tenant}/v2.0 för offentligt moln, där {tenant} är namnet på din klientorganisation, ditt klientorganisations-ID eller *gemensam* för en referens till den gemensamma slutpunkten (används för appar för en innehavare) |
> | `RedirectUri`  | URL där användare skickas efter autentisering mot Azure AD v2.0-slutpunkt |
> | `PostLogoutRedirectUri`     | URL där användare skickas efter utloggning |
> | `Scope`     | Listan över omfång som begärs, avgränsade med blanksteg |
> | `ResponseType`     | Begär att svaret från autentiseringen innehåller en ID-token |
> | `TokenValidationParameters`     | En lista över parametrar för tokenvalidering. I det här fallet ställs `ValidateIssuer` in på `false` för att ange att den kan acceptera inloggningar från personliga konton eller arbets- eller skolkonton |
> | `Notifications`     | En lista över ombud kan köra på olika *OpenIdConnect*-meddelanden |

### <a name="initiate-an-authentication-challenge"></a>Initiera en autentiseringsfråga

Du kan tvinga en användare att logga in genom att begära en autentiseringsfråga i kontrollanten:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Att begära en autentiseringsfråga med hjälp av metoden ovan är valfritt och används normalt när du vill att en vy ska nås från både autentiserade och icke-autentiserade användare. Du kan också skydda kontrollanter med hjälp av metoden som beskrivs i nästa avsnitt.

### <a name="protect-a-controller-or-a-controllers-method"></a>Skydda en kontrollant eller en kontrollants metod

Du kan skydda en kontrollant eller kontrollantåtgärder med attributet `[Authorize]`. Det här attributet begränsar åtkomsten till kontrollanten eller åtgärder genom att bara tillåta åtkomst för autentiserade användare i kontrollanten, vilket betyder att autentiseringsfråga tillämpas automatiskt när en *icke-autentiserad* användare försöker få åtkomst till någon av åtgärderna eller till kontrollanten som anpassats av attributet `[Authorize]`.

## <a name="next-steps"></a>Nästa steg

Prova att använda ASP.NET-självstudien för en komplett steg för steg-guide till att skapa appar och nya funktioner, bland annat en fullständig förklaring av den här snabbstarten.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig hur du skapar appen som används i den här snabbstarten

> [!div class="nextstepaction"]
> [Självstudie om inloggning](.\tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]