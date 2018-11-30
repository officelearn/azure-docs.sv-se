---
title: Ändringar som gjorts till ett MVC-projekt när du ansluter till Azure AD
description: Beskriver vad som händer MVC-projekt när du ansluter till Azure AD med hjälp av Visual Studio-anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: cc14f170a748f91289bbc644d5a9bef10697ce34
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427377"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt MVC-projekt (Visual Studio Azure Active Directory ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln identifierar exakt ändringar som görs i am ASP.NET MVC-projekt när du lägger till den [Azure Active Directory-anslutna service med Visual Studio](vs-active-directory-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns i [komma igång](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Har lagts till referenser

Påverkar projektreferenserna filen *.NET) och `packages.config` (NuGet referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Ytterligare referenser om du har valt den **läsa katalogdata** alternativet:

| Typ | Referens |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 endast) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 endast) |
| .NET; NuGet | System.Spatial |

I följande referenser tas bort (ASP.NET 4-projekt, som i Visual Studio 2015):

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projektet filändringar

- Ange egenskapen `IISExpressSSLPort` till ett distinkt antal.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0 eller 1 om du har valt den **läsa katalogdata** alternativet.
- Ange egenskapen `IISUrl` till `https://localhost:<port>/` där `<port>` matchar den `IISExpressSSLPort` värde.

## <a name="webconfig-or-appconfig-changes"></a>Web.config eller app.config ändringar

- Lägga till följande konfiguration:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Lagt till `<dependentAssembly>` element under den `<runtime><assemblyBinding>` nod för `System.IdentityModel.Tokens.Jwt` och `Microsoft.IdentityModel.Protocol.Extensions`.

Ytterligare ändringar om du har valt den **läsa katalogdata** alternativet:

- Lagt till följande konfigurationsposten under `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Lagt till följande element under `<configuration>`; värdena för varierar projekt-mdf-filen och katalog-projekt-id:

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

- Lagt till `<dependentAssembly>` element under den `<runtime><assemblyBinding>` nod för `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, och `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Ändringar i koden och tillägg

- Har lagts till i `[Authorize]` attributet `Controllers/HomeController.cs` och andra befintliga domänkontrollanter.

- Lagt till en authentication-startklass `App_Start/Startup.Auth.cs`, som innehåller Startlogik för Azure AD-autentisering. Om du har valt den **läsa katalogdata** alternativ, den här filen innehåller också kod för att få en OAuth-kod och exchange för en åtkomsttoken.

- Lagt till en kontrollantklassen `Controllers/AccountController.cs`, som innehåller `SignIn` och `SignOut` metoder.

- Lagt till en del av en vy, `Views/Shared/_LoginPartial.cshtml`, som innehåller en åtgärdslänken för `SignIn` och `SignOut`.

- Lagt till en del av en vy, `Views/Account/SignoutCallback.cshtml`, som innehåller HTML för utloggning Användargränssnittet.

- Uppdatera den `Startup.Configuration` metoden och ta ett anrop till `ConfigureAuth(app)` om klassen redan hade funnits; Annars läggs en `Startup` klass som innehåller anropar metoden.

- Lagt till `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) som innehåller information som Visual Studio använder för att spåra tillägget av anslutna tjänsten.

- Om du har valt den **läsa katalogdata** alternativet, har lagts till `Models/ADALTokenCache.cs` och `Models/ApplicationDbContext.cs` för tokencachelagring. Även lagt till en ytterligare domänkontrollant och visa att illustrera åtkomst till information om användarprofiler med hjälp av Azure graph API: er: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, och `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Säkerhetskopiering av filer (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD`. Visual Studio 2017 kan inte skapa säkerhetskopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapa ett Azure AD-program i den domän som du valde när du lägger till den anslutna tjänsten.
- Uppdaterade appen att inkludera den **läsa katalogdata** behörighet om det alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till logga in med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
