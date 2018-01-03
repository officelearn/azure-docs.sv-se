---
title: "Ändringar i ett projekt med MVC när du ansluter till Azure AD | Microsoft Docs"
description: "Beskriver vad som händer i projektet MVC när du ansluter till Azure AD med hjälp av Visual Studio anslutna tjänster"
services: active-directory
documentationcenter: na
author: kraigb
manager: mtillman
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: eccff00847968b4293b6e7142af0cceff0476c46
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med min MVC-projektet (Visual Studio Azure Active Directory ansluten service)?
> [!div class="op_single_selector"]
> * [Komma igång](vs-active-directory-dotnet-getting-started.md)
> * [Vad hände](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Referenser har lagts till
### <a name="nuget-package-references"></a>NuGet-paketet refererar till
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET-referenser
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **Avsnittsgruppen**

## <a name="code-has-been-added"></a>Koden har lagts till
### <a name="code-files-were-added-to-your-project"></a>Kodfiler har lagts till i ditt projekt
En autentisering startklass **App_Start/Startup.Auth.cs** har lagts till i ditt projekt som innehåller Startlogik för Azure AD-autentisering. Dessutom lades en domänkontrollant klass, Controllers/AccountController.cs som innehåller **SignIn()** och **SignOut()** metoder. Slutligen en del av en vy, **Views/Shared/_LoginPartial.cshtml** har lagts till som innehåller en länk för åtgärden för inloggning/utloggning.

### <a name="startup-code-was-added-to-your-project"></a>Startkoden har lagts till i ditt projekt
Om du redan har en startklass i projektet, den **Configuration** metod har uppdaterats med ett anrop till **ConfigureAuth(app)**. Annars en startklass har lagts till i projektet.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Din app.config eller web.config har nya konfigurationsvärden
Följande konfigurationsposter har lagts till.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>En App i Azure Active Directory (AD) skapades
En Azure AD-program har skapats i den katalog som du valde i guiden.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Om jag markerad *inaktivera enskilda användarkonton autentisering*, vilka ytterligare ändringar har gjorts i projektet?
NuGet-paketet refererar till har tagits bort och filer har tagits bort och säkerhetskopieras. Beroende på ditt projekt status kan du behöva manuellt ta bort filer eller ytterligare referenser eller ändra koden efter behov.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet-paketreferenser tas bort (för de finns)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Kodfiler säkerhetskopieras och ta bort (de finns)
Var och en av följande filer har säkerhetskopierats och tas bort från projektet. Säkerhetskopiera filer finns i en ”Backup”-mapp i roten på den projektkatalogen.

* **App_Start\IdentityConfig.CS**
* **Controllers\ManageController.CS**
* **Models\IdentityModels.CS**
* **Models\ManageViewModels.CS**

### <a name="code-files-backed-up-for-those-present"></a>Kodfiler säkerhetskopieras (för de finns)
Var och en av följande filer har säkerhetskopierats innan som ersätts. Säkerhetskopiera filer finns i en ”Backup”-mapp i roten på den projektkatalogen.

* **Startup.CS**
* **App_Start\Startup.auth.CS**
* **Controllers\AccountController.CS**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Om jag markerad *läsa katalogdata*, vilka ytterligare ändringar har gjorts i projektet?
Ytterligare referenser har lagts till.

### <a name="additional-nuget-package-references"></a>Ytterligare referenser för NuGet-paket
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Ytterligare referenser för .NET
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>Ytterligare kodfiler har lagts till i ditt projekt
Två filer har lagts till stöd för tokencachelagring: **Models\ADALTokenCache.cs** och **Models\ApplicationDbContext.cs**.  En ytterligare domänkontrollant och visa har lagts till för att illustrera åtkomst till användarens profilinformation som använder Azure graph API: er.  De här filerna är **Controllers\UserProfileController.cs** och **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>Ytterligare startkoden har lagts till i ditt projekt
I den **startup.auth.cs** -fil, en ny **OpenIdConnectAuthenticationNotifications** objekt lades till i **meddelanden** medlem i den  **OpenIdConnectAuthenticationOptions**.  Detta är att ta emot OAuth-koden och byta ut den för en åtkomst-token.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Ytterligare ändringar har gjorts till web.config eller app.config
Följande ytterligare konfigurationsposter har lagts till.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Följande avsnitt och anslutningssträngen har lagts till.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
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


### <a name="your-azure-active-directory-app-was-updated"></a>Din Azure Active Directory App har uppdaterats
Din Azure Active Directory App har uppdaterats med den *läsa katalogdata* behörighet och nyckeln skapades som användes som sedan den *ida: ClientSecret* i den  **Web.config** fil.

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

