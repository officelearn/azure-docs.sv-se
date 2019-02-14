---
title: 'Snabbstart: Azure AD v2.0 ASP.NET Core-webbapp | Microsoft Docs'
description: Lär dig hur du implementerar Microsoft-inloggning på en ASP.NET Core-webbapp med OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2018
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35cf4fbe17df05b6d55f3ce309ff8cecbb47d455
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204720"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Snabbstart: Lägga till inloggning med Microsoft i en ASP.NET Core-webbapp

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

I den här snabbstarten lär du dig hur en ASP.NET Core-webbapp kan logga in personliga konton (hotmail.com, outlook.com osv.) och arbets- och skolkonton från alla instanser av Active Directory (Azure AD).

![Så fungerar den exempelapp som genereras med den här snabbstarten](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen – Appregistreringar (förhandsversion)](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)** > **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>    - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-Quickstart`.
>    - I **Svars-URL** lägger du till `https://localhost:44321/` och väljer **Registrera**.
> 1. Välj menyn **Autentisering** och lägg sedan till följande information:
>    - I **Svars-URL** lägger du till `https://localhost:44321/signin-oidc` och markerar **Registrera**.
>    - I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:44321/signout-oidc`.
>    - Under **Implicit beviljande** kontrollerar du **ID-token**.
>    - Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera ditt program i Azure Portal
> För kodexemplen för att den här snabbstarten ska fungera måste du lägga till svars-URL som `https://localhost:44321/` och `https://localhost:44321/signin-oidc`, lägga till Utloggnings-URL som `https://localhost:44321/signout-oidc` och begära att ID-token ska utfärdas av auktoriseringsslutpunkten.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-aspnet-core-project"></a>Steg 2: Ladda ned ditt ASP.NET Core-projekt

- [Ladda ned Visual Studio 2017-lösningen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp i rotkatalogen, till exempel **C:\Azure-Samples**
1. Om du använder Visual Studio 2017 öppnar du lösningen i Visual Studio (valfritt).
1. Redigera filen **appsettings.json**. Leta reda på `ClientId` och ersätt `Enter_the_Application_Id_here` med värdet i **Program-ID (klient)** från appen du precis har registrerat. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Där:
> - `Enter_the_Application_Id_here` – är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du kan hitta **Program-ID (klient)** på appens **översiktssida**.
> - `Enter_the_Tenant_Info_Here` – är något av följande alternativ:
>   - Om ditt program stöder **Endast konton i den här organisationskatalogen** ska du ersätta värdet med **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **Alla Microsoft-kontoanvändare** ersätter du värdet med `common`
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

## <a name="more-information"></a>Mer information

I det här avsnittet får du en översikt över koden som krävs för att logga in användare. Det kan vara användbart för att förstå hur koden fungerar, huvudargumenten och också om du vill lägga till inloggning för en befintlig ASP.NET Core-app.

### <a name="startup-class"></a>Startklass

*Microsoft.AspNetCore.Authentication*-mellanprogram använder en startklass som körs när värdprocessen startar:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoden `AddAuthentication` konfigurerar tjänsten för att lägga till cookie-baserad autentisering, vilket används i webbläsarscenarier, samt ange frågan för OpenID Connect. 

Den rad som innehåller `.AddAzureAd` lägger till Azure AD-autentisering för ditt program. Den konfigureras sedan för att logga in med Azure AD v2.0-slutpunkten.

> |Var  |  |
> |---------|---------|
> | ClientId  | Program-ID (klient) från appen som registrerats i Azure-portalen. |
> | Myndighet | STS-slutpunkten för autentisering av användaren. Vanligtvis https://login.microsoftonline.com/{tenant}/v2.0 för offentligt moln, där {tenant} är namnet på din klientorganisation eller ditt klientorganisations-ID eller *gemensam* för en referens till den gemensamma slutpunkten (används för appar för en innehavare) |
> | TokenValidationParameters | En lista över parametrar för tokenvalidering. I det här fallet ställs `ValidateIssuer` in på `false` för att ange att den kan acceptera inloggningar från personliga konton eller arbets- eller skolkonton. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Skydda en kontrollant eller en kontrollants metod

Du kan skydda en kontrollant eller kontrollantmetoder med attributet `[Authorize]`. Det här attributet begränsar åtkomsten till kontrollanten eller metoderna genom att bara tillåta autentiserade användare. Det innebär att autentiseringsfrågan kan startas för att få åtkomst till kontrollanten om användaren inte har autentiserats.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Kolla in GitHub-lagringsplatsen för den här ASP.NET Core-snabbstarten för mer information, inklusive anvisningar om hur du lägger till autentisering i ett helt nytt ASP.NET Core-webbprogram:

> [!div class="nextstepaction"]
> [Kodexempel för ASP.NET Core-webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

