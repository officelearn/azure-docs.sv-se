---
title: Azure AD v2.0 Snabbstart för ASP.NET Core-webbapp | Microsoft Docs
description: Lär dig hur du implementerar Microsoft-inloggning i en ASP.NET Core-Webbapp med hjälp av OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ab3d0b74e8305d67af862020197c69b15221086
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830233"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Lägga till logga in med Microsoft i en ASP.NET Core-webbapp

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur en ASP.NET Core-Webbapp kan logga in personal (hotmail.com, live.com eller andra) och fungerar och skolkonton från valfri Azure Active Directory-instans.

![Så här fungerar den exempelapp som skapas med den här snabbstarten](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


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
> 1. I **Redirect URLs** (Omdirigerings-URL:er) anger du `http://localhost:3110/`.
> 1. Rulla längst ned på sidan och klicka på **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kodexemplet för den här snabbstarten ska fungera måste vi lägga till en svars-URL som `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-core-webapp/green-check.png) appen konfigureras med det här attributet

#### <a name="step-2-download-your-aspnet-core-project"></a>Steg 2: Ladda ned ditt ASP.NET Core-projekt

- [Ladda ned ASP.NET Core-projektet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet

1. Extrahera zip-filen till en lokal mapp närmare till rotmappen – till exempel **C:\Azure-Samples**
1. Om du använder Visual Studio 2017 kan du öppna projektet i Visual Studio (valfritt)
1. Redigera **appsettings.json** och Ersätt värdet för `ClientId` med program-Id från programmet som du just registrerade:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Om programmet är en *enda klient programmet* (riktar in sig på konton i den aktuella katalogen) i din **appsettings.json** filen, hitta värdet för `TenantId` och Ersätt `common`med din **klient-Id** eller **klientnamn** (till exempel contoso.microsoft.com). Du kan hämta klientorganisationsnamnet på **översiktssidan**.

## <a name="more-information"></a>Mer information

I det här avsnittet får du en översikt över koden som krävs för att logga in användare. Detta kan vara användbar för att förstå hur koden fungerar, huvudsakliga argument, och även om du vill lägga till inloggning till ett befintligt ASP.NET Core-program.

### <a name="startup-class"></a>Startklass

*Microsoft.AspNetCore.Authentication* mellanprogram använder en startklass som körs när värdprocessen initierar:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

Metoden `AddAuthentication` konfigurerar tjänsten för att lägga till Cookie-baserad autentisering - använde på webbläsare scenarier, samt inställd OpenIdConnect utmaningen. 

Den rad som innehåller `.AddAzureAd` lägger till Azure Active Directory-autentisering för ditt program.

Förutom som filen **AzureAdAuthenticationBuilderExtensions.cs** lägger till tilläggsmetod i AzureAd-autentiseringsflödet. Den här tilläggsmetod Konfigurera attribut som krävs för Azure AD-autentisering. Den `Configure` -metod i `IConfigureNamedOptions` gränssnittet innehåller följande:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Var  |  |
> |---------|---------|
> |ClientId     |Program-Id från programmet som är registrerad i Azure portal|
> |Myndighet | STS-slutpunkten för användaren att autentisera. Vanligtvis https://login.microsoftonline.com/{tenant}/v2.0 för offentligt moln, där {tenant} är namnet på din klientorganisation, ditt klientorganisations-ID eller *gemensam* för en referens till den gemensamma slutpunkten (används för appar för en innehavare)|
> |UseTokenLifetime |Anger att autentiseringscookien bör matcha autentiseringstoken|
> |RequireHttpsMetadata     |Kräv HTTPS för metadata-adress eller utfärdare. Det rekommenderas att ändra det här värdet till `True`|
> |TokenValidationParameters     | En lista över parametrar för tokenvalidering. I det här fallet `ValidateIssuer` är inställd på `false` att indikera att den kan acceptera inloggningar från några personliga,- eller arbets- eller skolkonto konton|

### <a name="initiate-an-authentication-challenge"></a>Initiera en autentiseringsfråga

Du kan tvinga användaren att logga in genom att begära en autentiseringsfråga i din kontrollant precis som i **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Begär en autentiseringsfråga med hjälp av metoden ovan är valfritt och commonsly används när du vill att vyn ska vara tillgängliga för både autentiserade och icke-autentiserade användare. Du kan skydda domänkontrollanter med hjälp av den metod som beskrivs i nästa avsnitt.

### <a name="protect-a-controller-or-a-controllers-method"></a>Skydda en kontrollant eller en kontrollants metod

Du kan skydda en domänkontrollant eller domänkontrollern methodss med den `[Authorize]` attribut. Det här attributet begränsar åtkomsten till domänkontrollanten eller metoder genom att bara tillåta autentiserade användare – vilket innebär att autentiseringsfråga kan startas för att få åtkomst till kontrollanten om användaren inte autentiserats.

## <a name="next-steps"></a>Nästa steg

Kolla in GitHub-lagringsplatsen för den här ASP.NET Core-snabbstarten för mer information – inklusive anvisningar om hur du lägger till autentisering i en helt ny ASP.NET Core-webbprogram:

> [!div class="nextstepaction"]
> [ASP.NET Core Web App-kodexempel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]