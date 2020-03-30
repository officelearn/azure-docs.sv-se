---
title: Ändringar som gjorts i WebAPI-projekt vid anslutning till Azure AD
description: Beskriver vad som händer med ditt WebAPI-projekt när du ansluter till Azure AD med Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159394"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt WebAPI-projekt (Visual Studio Azure Active Directory-ansluten tjänst)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln identifierar de exakta ändringarna i ASP.NET WebAPI, ASP.NET ensidiga program och ASP.NET Azure API-projekt när du lägger till [Azure Active Directory-anslutna tjänsten med Visual Studio](vs-active-directory-add-connected-service.md). Gäller även för ASP.NET Azure Mobile Service-projekt i Visual Studio 2015.

Information om hur du arbetar med den anslutna tjänsten finns i [Komma igång](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projektfilen *.NET-referenser) `packages.config` och (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet (nuget) | Microsoft.Owin |
| .NET; NuGet (nuget) | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet (nuget) | Microsoft.Owin.Security |
| .NET; NuGet (nuget) | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet (nuget) | Microsoft.Owin.Security.Jwt |
| .NET; NuGet (nuget) | Microsoft.owin.Security.oauth |
| .NET; NuGet (nuget) | Owin (owin) |
| .NET; NuGet (nuget) | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Endast Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Endast Visual Studio 2017: Lade `<appSettings>`också till följande bidrag under "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Lade `<dependentAssembly>` till `<runtime><assemblyBinding>` element under `System.IdentityModel.Tokens.Jwt`noden för .

- Om du har valt alternativet **Läs katalogdata** `<appSettings>`lade du till följande konfigurationspost under:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kodändringar och tillägg

- Lade `[Authorize]` till `Controllers/ValueController.cs` attributet till och andra befintliga styrenheter.

- Lade till en `App_Start/Startup.Auth.cs`autentiseringsstartklass, som innehåller startlogik för Azure AD-autentisering eller ändrade den därefter. Om du har valt alternativet **Läs katalogdata** innehåller den här filen också kod för att ta emot en OAuth-kod och byta ut den mot en åtkomsttoken.

- (Visual Studio 2015 med endast ASP.NET 4-appen) Tog `App_Start/IdentityConfig.cs` bort `Controllers/AccountController.cs` `Models/IdentityModel.cs`och `Providers/ApplicationAuthProvider.cs`lade till , och .

- Lade `Connected Services/AzureAD/ConnectedService.json` till (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

### <a name="file-backup-visual-studio-2015"></a>Säkerhetskopiering av filer (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och tog bort filer. Alla berörda filer sparas `Backup/AzureAD`i mappen . Visual Studio 2017 skapar inte säkerhetskopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Ändringar på Azure

- Skapade ett Azure AD-program i den domän som du valde när du lade till den anslutna tjänsten.
- Uppdaterade appen så att den innehåller behörigheten **Läskatalogdata** om det alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
