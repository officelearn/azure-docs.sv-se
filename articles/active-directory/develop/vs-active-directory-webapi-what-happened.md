---
title: "Ändringar i ett projekt för WebApi när du ansluter till Azure AD | Microsoft Docs"
description: "Beskriver vad som händer i projektet WebApi när du ansluter till Azure AD med hjälp av Visual Studio"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med min WebApi-projektet (Visual Studio Azure Active Directory ansluten service)
> [!div class="op_single_selector"]
> * [Komma igång](vs-active-directory-webapi-getting-started.md)
> * [Vad hände](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Referenser har lagts till
### <a name="nuget-package-references"></a>NuGet-paketet refererar till
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>.NET-referenser
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Kodändringar
### <a name="code-files-were-added-to-your-project"></a>Kodfiler har lagts till i ditt projekt
En autentisering startklass **App_Start/Startup.Auth.cs** har lagts till i ditt projekt som innehåller Startlogik för Azure AD-autentisering.

### <a name="startup-code-was-added-to-your-project"></a>Startkoden har lagts till i ditt projekt
Om du redan har en startklass i projektet, den **Configuration** metod har uppdaterats med ett anrop till `ConfigureAuth(app)`. Annars en startklass har lagts till i projektet.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Din app.config eller web.config-fil har nya konfigurationsvärden.
Följande konfigurationsposter har lagts till.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>En Azure AD App har skapats
En Azure AD-program har skapats i den katalog som du valde i guiden.

[Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Om jag markerad *inaktivera enskilda användarkonton autentisering*, vilka ytterligare ändringar har gjorts i projektet?
NuGet-paketet refererar till har tagits bort och filer har tagits bort och säkerhetskopieras. Beroende på ditt projekt status kan du behöva manuellt ta bort filer eller ytterligare referenser eller ändra koden efter behov.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet-paketreferenser tas bort (för de finns)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Kodfiler säkerhetskopieras och ta bort (de finns)
Var och en av följande filer har säkerhetskopierats och tas bort från projektet. Säkerhetskopiera filer finns i en ”Backup”-mapp i roten på den projektkatalogen.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Kodfiler säkerhetskopieras (för de finns)
Var och en av följande filer har säkerhetskopierats innan som ersätts. Säkerhetskopiera filer finns i en ”Backup”-mapp i roten på den projektkatalogen.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Om jag markerad *läsa katalogdata*, vilka ytterligare ändringar har gjorts i projektet?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Ytterligare ändringar har gjorts till web.config eller app.config
Följande ytterligare konfigurationsposter har lagts till.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Din Azure Active Directory App har uppdaterats
Din Azure Active Directory App har uppdaterats med den *läsa katalogdata* behörighet och nyckeln skapades användes sedan som den *ida: lösenord* i den `web.config` filen.

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

