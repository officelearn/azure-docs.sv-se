---
title: Ändringar som gjorts i ett WebAPI-projekt när du ansluter till Azure AD
description: Beskriver vad som händer med ditt WebAPI-projekt när du ansluter till Azure AD med hjälp av Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324281"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt WebAPI-projekt (Visual Studio Azure Active Directory Connected service)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln beskriver de exakta ändringar som gjorts i ASP.NET WebAPI, ASP.NET och ASP.NET Azure API-projekt när du lägger till den [Azure Active Directory anslutna tjänsten med hjälp av Visual Studio](vs-active-directory-add-connected-service.md). Gäller även för ASP.NET Azure Mobile Service-projekt i Visual Studio 2015.

Information om hur du arbetar med den anslutna tjänsten finns i [komma igång](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projekt filen *. net-referenser) och `packages.config` (NuGet-referenser).

| type | Referens |
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

| type | Referens |
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

| type | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft.AspNet.Identity.Core |
| Nettotid NuGet | Microsoft.AspNet.Identity.EntityFramework |
| Nettotid NuGet | Microsoft. ASPNET. Identity. OWIN |

## <a name="project-file-changes"></a>Projekt fil ändringar

- Ange en distinkt `IISExpressSSLPort` siffra som egenskap.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0, eller 1 om du har valt alternativet **Läs katalog data** .
- Ange egenskapen `IISUrl` till `https://localhost:<port>/` där `<port>` matchar värdet.`IISExpressSSLPort`

## <a name="webconfig-or-appconfig-changes"></a>ändringar i Web. config eller app. config

- Följande konfigurations poster har lagts till:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Endast Visual Studio 2017: Även följande post har lagts till `<appSettings>`under "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Element `<dependentAssembly>` har lagts till `<runtime><assemblyBinding>` under noden `System.IdentityModel.Tokens.Jwt`för.

- Om du har valt alternativet **Läs katalog data** lade du till följande konfigurations post `<appSettings>`under:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kod ändringar och tillägg

- Attributet har lagts till `Controllers/ValueController.cs` och andra befintliga kontrollanter. `[Authorize]`

- Har lagt till en start klass `App_Start/Startup.Auth.cs`för autentisering, som innehåller start logik för Azure AD-autentisering eller ändrat den. Om du har valt alternativet **Läs katalog data** innehåller den här filen även kod för att ta emot en OAuth-kod och utbyta den för en åtkomsttoken.

- (Endast Visual Studio 2015 med ASP.NET 4 app) Har `App_Start/IdentityConfig.cs` tagits bort `Controllers/AccountController.cs`och `Models/IdentityModel.cs`lagts till `Providers/ApplicationAuthProvider.cs`, och.

- Lade `Connected Services/AzureAD/ConnectedService.json` till (Visual Studio 2017) `Service References/Azure AD/ConnectedService.json` eller (Visual Studio 2015) som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

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
- [Lägg till inloggning med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
