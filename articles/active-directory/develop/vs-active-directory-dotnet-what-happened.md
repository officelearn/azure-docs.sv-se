---
title: Ändringar som gjorts i ett MVC-projekt när du ansluter till Azure AD
description: Beskriver vad som händer med ditt MVC-projekt när du ansluter till Azure AD med hjälp av Visual Studio-anslutna tjänster
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: d42d905bf35c015213e76bc50c4bc339a5c4a062
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886117"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Vad har hänt med mitt MVC-projekt (Visual Studio Azure Active Directory-ansluten tjänst)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln identifierar de exakta ändringarna i ett ASP.NET MVC-projekt när den [anslutna Azure Active Directory-tjänsten läggs till med Visual Studio](vs-active-directory-add-connected-service.md).

Information om hur du arbetar med den anslutna tjänsten finns i [Komma igång](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projektfilen (*.NET-referenser) `packages.config` och (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet (nuget) | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet (nuget) | Microsoft.Owin |
| .NET; NuGet (nuget) | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet (nuget) | Microsoft.Owin.Security |
| .NET; NuGet (nuget) | Microsoft.Owin.Security.Cookies |
| .NET; NuGet (nuget) | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet (nuget) | Owin (owin) |
| .NET        | System.IdentityModel |
| .NET; NuGet (nuget) | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Ytterligare referenser om du har valt alternativet **Läs katalogdata:**

| Typ | Referens |
| --- | --- |
| .NET; NuGet (nuget) | EntityFramework |
| .NET        | EntityFramework.SqlServer (endast Visual Studio 2015) |
| .NET; NuGet (nuget) | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet (nuget) | Microsoft.Data.Edm |
| .NET; NuGet (nuget) | Microsoft.Data.OData |
| .NET; NuGet (nuget) | Microsoft.Data.Services.Client |
| .NET; NuGet (nuget) | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (endast Visual Studio 2015) |
| .NET; NuGet (nuget) | System.Rumsligt |

Följande referenser tas bort (endast ASP.NET 4 projekt, som i Visual Studio 2015):

| Typ | Referens |
| --- | --- |
| .NET; NuGet (nuget) | Microsoft.AspNet.Identity.Core |
| .NET; NuGet (nuget) | Microsoft.AspNet.Identity.entityFramework |
| .NET; NuGet (nuget) | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Ändringar av projektfiler

- Ange egenskapen `IISExpressSSLPort` till ett avgränsat tal.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0 eller 1 om du har markerat alternativet **Läs katalogdata.**
- Ange `IISUrl` egenskapen `https://localhost:<port>/` `<port>` till `IISExpressSSLPort` platsen där värdet matchars.

## <a name="webconfig-or-appconfig-changes"></a>web.config eller app.config ändringar

- Lade till följande konfigurationsposter:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Lade `<dependentAssembly>` till `<runtime><assemblyBinding>` element under `System.IdentityModel.Tokens.Jwt` `Microsoft.IdentityModel.Protocol.Extensions`noden för och .

Ytterligare ändringar om du har valt alternativet **Läs katalogdata:**

- Lade till följande `<appSettings>`konfigurationspost under:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Lade till följande `<configuration>`element under ; värdena för project-mdf-filen och projektkatalog-id varierar:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Lade `<dependentAssembly>` till `<runtime><assemblyBinding>` element under `Microsoft.Data.Services.Client` `Microsoft.Data.Edm`noden `Microsoft.Data.OData`för , och .

## <a name="code-changes-and-additions"></a>Kodändringar och tillägg

- Lade `[Authorize]` till `Controllers/HomeController.cs` attributet till och andra befintliga styrenheter.

- Lade till en `App_Start/Startup.Auth.cs`startklass för autentisering, som innehåller startlogik för Azure AD-autentisering. Om du har valt alternativet **Läs katalogdata** innehåller den här filen också kod för att ta emot en OAuth-kod och byta ut den mot en åtkomsttoken.

- Lade till en `Controllers/AccountController.cs`controller-klass, som innehåller `SignIn` och `SignOut` metoder.

- Lade till en `Views/Shared/_LoginPartial.cshtml`partiell vy, `SignIn` som `SignOut`innehåller en åtgärdslänk för och .

- Lade till en `Views/Account/SignoutCallback.cshtml`partiell vy som innehåller HTML för ut signeringsgränssnittet.

- Uppdaterade `Startup.Configuration` metoden för att `ConfigureAuth(app)` inkludera ett anrop till om klassen redan fanns. I annat `Startup` fall har du lagt till en klass som innehåller anrop av metoden.

- Lade `Connected Services/AzureAD/ConnectedService.json` till (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

- Om du har valt alternativet `Models/ADALTokenCache.cs` Läs `Models/ApplicationDbContext.cs` **katalogdata,** tillagt och stöd för tokencachelagring. Lade också till ytterligare en styrenhet och vy för att `Controllers/UserProfileController.cs` `Views/UserProfile/Index.cshtml`illustrera åtkomst till användarprofilinformation med Azure graph API:er: , och`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Säkerhetskopiering av filer (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och tog bort filer. Alla berörda filer sparas `Backup/AzureAD`i mappen . Visual Studio 2017 och senare skapar inte säkerhetskopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Ändringar på Azure

- Skapade ett Azure AD-program i den domän som du valde när du lade till den anslutna tjänsten.
- Uppdaterade appen så att den innehåller behörigheten **Läskatalogdata** om det alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
