---
title: Ändringar i ett projekt med MVC när du ansluter till Azure AD
description: Beskriver vad som händer i projektet MVC när du ansluter till Azure AD med hjälp av Visual Studio anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: bd4a32575067b3de94935322ead9a7f0966d2969
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med min MVC-projektet (Visual Studio Azure Active Directory ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln identifierar exakt ändringarna i am ASP.NET MVC-projektet när du lägger till den [Azure Active Directory anslutna service med Visual Studio](vs-active-directory-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns [komma igång](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Tillagd referenserna

Påverkar projektreferenser filen *.NET) och `packages.config` (NuGet referenser).

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
| .NET        | Avsnittsgruppen |

Ytterligare referenser om du har valt den **läsa katalogdata** alternativ:

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

I följande referenser tas bort (ASP.NET 4 projekt, som i Visual Studio 2015):

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projektet filändringar

- Ange egenskapen `IISExpressSSLPort` till ett distinkta tal.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0 eller 1 om du har valt den **läsa katalogdata** alternativet.
- Ange egenskapen `IISUrl` till `https://localhost:<port>/` där `<port>` matchar den `IISExpressSSLPort` värde.

## <a name="webconfig-or-appconfig-changes"></a>ändringar i Web.config eller app.config

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

- Lägga till `<dependentAssembly>` element under den `<runtime><assemblyBinding>` nod för `System.IdentityModel.Tokens.Jwt` och `Microsoft.IdentityModel.Protocol.Extensions`.

Ytterligare ändringar om du har valt den **läsa katalogdata** alternativ:

- Lägga till följande konfigurationspost under `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Lägga till följande element under `<configuration>`; värdena för varierar mdf projektfilen och projekt-katalog-id:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
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

- Lägga till `<dependentAssembly>` element under den `<runtime><assemblyBinding>` nod för `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, och `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Kodändringar och tillägg

- Lägga till den `[Authorize]` attributet `Controllers/HomeController.cs` och andra befintliga domänkontrollanter.

- Lägga till en autentisering startklass `App_Start/Startup.Auth.cs`, som innehåller Startlogik för Azure AD-autentisering. Om du har valt den **läsa katalogdata** alternativet filen innehåller även kod för att ta emot en OAuth-kod och exchange för en åtkomst-token.

- Lägga till en klass som domänkontrollant `Controllers/AccountController.cs`, som innehåller `SignIn` och `SignOut` metoder.

- Lägga till en del av en vy, `Views/Shared/_LoginPartial.cshtml`, som innehåller en åtgärdslänken för `SignIn` och `SignOut`.

- Lägga till en del av en vy, `Views/Account/SignoutCallback.cshtml`, som innehåller HTML för utloggning Användargränssnittet.

- Uppdatera den `Startup.Configuration` metod för att inkludera ett anrop till `ConfigureAuth(app)` om klassen redan finns, annars läggs en `Startup` klass som innehåller anropar metoden.

- Lägga till `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) som innehåller information som används i Visual Studio för att spåra tillägg av tjänsten anslutna.

- Om du har valt den **läsa katalogdata** alternativet läggs `Models/ADALTokenCache.cs` och `Models/ApplicationDbContext.cs` att stödja tokencachelagring. Har även lagt till en ytterligare domänkontrollant och visa att illustrera åtkomst till användarens profilinformation som använder Azure graph API: er: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, och `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Säkerhetskopiering (Visual Studio 2015)

När du lägger till tjänsten anslutna säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD`. Visual Studio-2017 kan inte skapa säkerhetskopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapa en Azure AD-program i den domän som du valde när du lägger till tjänsten anslutna.
- Uppdaterade appen att inkludera den **läsa katalogdata** behörighet om det alternativet har valts.

[Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](active-directory-authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram](guidedsetups/active-directory-aspnetwebapp-v1.md)
