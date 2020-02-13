---
title: Ändringar som görs i WebAPI-projekt vid anslutning till Azure AD
description: Beskriver vad som händer med ditt WebAPI-projekt när du ansluter till Azure AD med hjälp av Visual Studio
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159394"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt WebAPI-projekt (Visual Studio Azure Active Directory Connected service)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln beskriver de exakta ändringar som gjorts i ASP.NET WebAPI, ASP.NET och ASP.NET Azure API-projekt när du lägger till den [Azure Active Directory anslutna tjänsten med hjälp av Visual Studio](vs-active-directory-add-connected-service.md). Gäller även för ASP.NET Azure Mobile Service-projekt i Visual Studio 2015.

Information om hur du arbetar med den anslutna tjänsten finns i [komma igång](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projekt filen *. NET-referenser) och `packages.config` (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft. OWIN |
| Nettotid NuGet | Microsoft.Owin.Host.SystemWeb |
| Nettotid NuGet | Microsoft.Owin.Security |
| Nettotid NuGet | Microsoft.Owin.Security.ActiveDirectory |
| Nettotid NuGet | Microsoft.Owin.Security.Jwt |
| Nettotid NuGet | Microsoft.Owin.Security.OAuth |
| Nettotid NuGet | Owin |
| Nettotid NuGet | System.IdentityModel.Tokens.Jwt |

Ytterligare referenser om du har valt alternativet **Läs katalog data** :

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (endast Visual Studio 2015) |
| Nettotid NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| Nettotid NuGet | Microsoft.Data.Edm |
| Nettotid NuGet | Microsoft.Data.OData |
| Nettotid NuGet | Microsoft.Data.Services.Client |
| Nettotid NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Endast Visual Studio 2015) |
| Nettotid NuGet | System.Spatial |

Följande referenser tas bort (endast ASP.NET 4-projekt, som i Visual Studio 2015):

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft.AspNet.Identity.Core |
| Nettotid NuGet | Microsoft.AspNet.Identity.EntityFramework |
| Nettotid NuGet | Microsoft. ASPNET. Identity. OWIN |

## <a name="project-file-changes"></a>Projekt fil ändringar

- Ange egenskapen `IISExpressSSLPort` till ett distinkt tal.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0, eller 1 om du har valt alternativet **Läs katalog data** .
- Ange egenskapen `IISUrl` till `https://localhost:<port>/` där `<port>` matchar `IISExpressSSLPort` svärdet.

## <a name="webconfig-or-appconfig-changes"></a>ändringar i Web. config eller app. config

- Följande konfigurations poster har lagts till:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Endast Visual Studio 2017: även följande post har lagts till under `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- `<dependentAssembly>` element har lagts till under noden `<runtime><assemblyBinding>` för `System.IdentityModel.Tokens.Jwt`.

- Om du har valt alternativet **Läs katalog data** lade du till följande konfigurations post under `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kod ändringar och tillägg

- `[Authorize]`-attributet har lagts till i `Controllers/ValueController.cs` och andra befintliga kontrollanter.

- Har lagt till en start klass för autentisering, `App_Start/Startup.Auth.cs`, som innehåller start logik för Azure AD-autentisering eller ändrat den. Om du har valt alternativet **Läs katalog data** innehåller den här filen även kod för att ta emot en OAuth-kod och utbyta den för en åtkomsttoken.

- (Endast Visual Studio 2015 med ASP.NET 4 app) Tog bort `App_Start/IdentityConfig.cs` och lagt till `Controllers/AccountController.cs`, `Models/IdentityModel.cs`och `Providers/ApplicationAuthProvider.cs`.

- Lade till `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

### <a name="file-backup-visual-studio-2015"></a>Fil säkerhets kopiering (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD`. I Visual Studio 2017 skapas inga säkerhets kopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapade ett Azure AD-program i den domän som du valde när du lade till den anslutna tjänsten.
- Uppdaterade appen för att inkludera behörigheten **läsa katalog data** om alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägg till inloggning med Microsoft i en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
