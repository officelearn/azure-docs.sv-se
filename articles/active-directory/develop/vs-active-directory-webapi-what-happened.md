---
title: Ändringar i ett projekt för WebAPI när du ansluter till Azure AD
description: Beskriver vad som händer i projektet WebAPI när du ansluter till Azure AD med hjälp av Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: b8303ccf6fc96e8d0708c9e414fdf511e0cf2fdf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31785170"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med min WebAPI-projektet (Visual Studio Azure Active Directory ansluten service)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln identifierar exakt ändringarna i ASP.NET-WebAPI ASP.NET Single-Page Application och Azure-API för ASP.NET-projekt när du lägger till den [Azure Active Directory anslutna service med Visual Studio](vs-active-directory-add-connected-service.md). Gäller även för ASP.NET Azure Mobile Service-projekt i Visual Studio 2015.

Information om hur du arbetar med anslutna tjänsten finns [komma igång](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Tillagd referenserna

Påverkar projektreferenser filen *.NET) och `packages.config` (NuGet referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 endast) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 endast: också har lagt till följande post under `<appSettings>`”

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Lägga till `<dependentAssembly>` element under den `<runtime><assemblyBinding>` nod för `System.IdentityModel.Tokens.Jwt`.

- Om du har valt den **läsa katalogdata** alternativ, läggs följande konfigurationspost under `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kodändringar och tillägg

- Lägga till den `[Authorize]` attributet `Controllers/ValueController.cs` och andra befintliga domänkontrollanter.

- Lägga till en autentisering startklass `App_Start/Startup.Auth.cs`, som innehåller Startlogik för Azure AD-autentisering eller ändrades i enlighet därmed. Om du har valt den **läsa katalogdata** alternativet filen innehåller även kod för att ta emot en OAuth-kod och exchange för en åtkomst-token.

- (Visual Studio 2015 med ASP.NET 4 app bara) Ta bort `App_Start/IdentityConfig.cs` och läggs `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, och `Providers/ApplicationAuthProvider.cs`.

- Lägga till `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) eller `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) som innehåller information som används i Visual Studio för att spåra tillägg av tjänsten anslutna.

### <a name="file-backup-visual-studio-2015"></a>Säkerhetskopiering (Visual Studio 2015)

När du lägger till tjänsten anslutna säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD`. Visual Studio-2017 kan inte skapa säkerhetskopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapa en Azure AD-program i den domän som du valde när du lägger till tjänsten anslutna.
- Uppdaterade appen att inkludera den **läsa katalogdata** behörighet om det alternativet har valts.

[Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](active-directory-authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram](guidedsetups/active-directory-aspnetwebapp-v1.md)