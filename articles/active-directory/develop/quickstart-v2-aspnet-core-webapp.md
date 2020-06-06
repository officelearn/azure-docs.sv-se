---
title: Lägg till inloggning med Microsoft till ASP.NET Core Web Apps – Microsoft Identity Platform | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en ASP.NET Core-webbapp med OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 50b6244e5db6f812d1f9e3d23ea5fa88ea3bebea
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456818"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Snabb start: lägga till inloggning med Microsoft i ett ASP.NET Core-webbprogram
I den här snabb starten använder du ett kod exempel för att lära dig hur en ASP.NET Core webbapp kan logga in personliga konton (hotmail.com, outlook.com, andra) och arbets-och skol konton från valfri Azure Active Directory (Azure AD)-instans. (Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure Portal-Appregistreringar](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>    - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-Quickstart`.
>    - I **omdirigerings-URI**, Lägg till `https://localhost:44321/` och välj **Registrera**.
> 1. Välj menyn **Autentisering** och lägg sedan till följande information:
>    - I **omdirigerings-URI: er**lägger `https://localhost:44321/signin-oidc` du till och väljer **Spara**.
>    - I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:44321/signout-oidc`.
>    - Under **Implicit beviljande** kontrollerar du **ID-token**.
>    - Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För kodexemplen för att den här snabbstarten ska fungera måste du lägga till svars-URL som `https://localhost:44321/` och `https://localhost:44321/signin-oidc`, lägga till Utloggnings-URL som `https://localhost:44321/signout-oidc` och begära att ID-token ska utfärdas av auktoriseringsslutpunkten.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-aspnet-core-project"></a>Steg 2: Ladda ned ditt ASP.NET Core-projekt

> [!div renderon="docs"]
> [Ladda ned Visual Studio 2019-lösningen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Steg 3: köra Visual Studio-projektet
> 1. Extrahera zip-filen i en lokal mapp i rotkatalogen, till exempel **C:\Azure-Samples**
> 1. Öppna lösningen i Visual Studio
> 1. Redigera filen **appsettings.json**. Hitta `ClientId` och uppdatera värdet för `ClientId` med **programmets (klient) ID-** värdet för det program som du har registrerat.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



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

Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, huvudsakliga argument och även om du vill lägga till inloggning till ett befintligt ASP.NET Core-program.

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

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
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

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

Metoden `AddAuthentication` konfigurerar tjänsten för att lägga till cookie-baserad autentisering, som används i webb läsar scenarier och för att ställa in utmaningen till OpenID Connect.

Raden som innehåller `.AddAzureAd` lägger till Microsoft Identity Platform-autentisering till ditt program. Den konfigureras sedan att logga in med hjälp av Microsoft Identity Platform-slutpunkten.

> |Var  |  |
> |---------|---------|
> | ClientId  | Program-ID (klient) från appen som registrerats i Azure-portalen. |
> | Myndighet | STS-slutpunkten för autentisering av användaren. Vanligtvis <https://login.microsoftonline.com/{tenant}/v2.0> för offentligt moln, där {tenant} är namnet på din klientorganisation eller ditt klientorganisations-ID eller *gemensam* för en referens till den gemensamma slutpunkten (används för appar för en innehavare) |
> | TokenValidationParameters | En lista över parametrar för tokenvalidering. I det här fallet ställs `ValidateIssuer` in på `false` för att ange att den kan acceptera inloggningar från personliga konton eller arbets- eller skolkonton. |


> [!NOTE]
> Inställningen `ValidateIssuer = false` är en förenkling för den här snabb starten. I verkliga program måste du verifiera utfärdaren.
> Se exemplen för att förstå hur du gör det.
>
> Observera också `Configure` metoden som innehåller två viktiga metoder: `app.UseCookiePolicy()` och`app.UseAuthentication()`

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more core
    app.UseCookiePolicy();
    app.UseAuthentication();
    // more core
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Skydda en kontrollant eller en kontrollants metod

Du kan skydda en kontrollant eller kontrollantmetoder med attributet `[Authorize]`. Det här attributet begränsar åtkomsten till styrenheten eller metoderna genom att bara tillåta autentiserade användare, vilket innebär att autentiseringen kan startas för att få åtkomst till kontrollanten om användaren inte är autentiserad.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Kolla in GitHub-lagrings platsen för den här ASP.NET Core själv studie kursen om du vill ha mer information, inklusive instruktioner om hur du lägger till autentisering till en helt ny ASP.NET Core webbapp, hur du anropar Microsoft Graph och andra Microsoft-API: er, hur du anropar dina egna API: er, hur du lägger till auktorisering, hur du loggar in användare i nationella moln eller med sociala identiteter med mera :

> [!div class="nextstepaction"]
> [Själv studie kurs om ASP.NET Core Web Apps](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
