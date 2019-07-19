---
title: Webbapp som loggar in användare (kod konfiguration) – Microsoft Identity Platform
description: Lär dig hur du skapar en webbapp som loggar in användare (kod konfiguration)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853073"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webbapp som loggar in användare – kod konfiguration

Lär dig hur du konfigurerar koden för din webbapp som loggar in användare.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotek som används för att skydda Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotek som används för att skydda en webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitets modells tillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions för .NET används direkt av ASP.NET och ASP.NET Core och avser en uppsättning dll: er som kör både på .NET Framework och .NET Core. Från en ASP.NET/ASP.NET Core-webbapp kan du kontrol lera token-verifiering med **TokenValidationParameters** -klassen (särskilt i vissa ISV-scenarier) |

## <a name="aspnet-core-configuration"></a>ASP.NET Core konfiguration

Kodfragment i den här artikeln och följande extraheras från den stegvisa självstudien för [ASP.net Core Web App, kapitel 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Du kanske vill referera till den här självstudien för fullständig implementerings information.

### <a name="application-configuration-files"></a>Konfigurationsfiler för program

I ASP.net Core konfigureras inloggnings användare för webb program med Microsoft Identity Platform via `appsettings.json` filen. De inställningar som du behöver fylla i är:

- molnet `Instance` om du vill att din app ska köras i nationella moln
- mål gruppen i`tenantId`
- `clientId` för ditt program, som kopieras från Azure Portal.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

I ASP.net Core finns det en annan fil som innehåller URL: en`applicationUrl`() och SSL-porten`sslPort`() för ditt program samt olika profiler.

```JSon
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. det vill säga för de två konfigurationsfilerna ovan skulle de vara `https://localhost:44321/signin-oidc` som `http://localhost:3110` applicationUrl, `sslPort` men `CallbackPath` har angetts (44321) och är `/signin-oidc` enligt definitionen i `appsettings.json`.
  
På samma sätt skulle utloggnings-URI: n vara inställd `https://localhost:44321/signout-callback-oidc`på.

### <a name="initialization-code"></a>Initierings kod

I ASP.net Core Web Apps (och webb-API: er) finns koden som gör att programmet initieras `Startup.cs` i filen och, för att lägga till autentisering med Microsoft Identity Platform (tidigare Azure AD) v 2.0, måste du lägga till följande kod. Kommentarerna i koden bör vara själv för klar Ande.

  > [!NOTE]
  > Om du startar ditt projekt med standard ASP.net Core-webbprojektet i Visual Studio `dotnet new mvc` eller använder `AddAzureAD` -metoden är tillgänglig som standard eftersom de relaterade paketen läses in automatiskt. Men om du skapar ett projekt från grunden och försöker använda koden nedan rekommenderar vi att du lägger till NuGet-paketet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** i projektet för att göra `AddAzureAD` metoden tillgängligt.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET-konfiguration

I ASP.net konfigureras programmet via `Web.Config` filen

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Koden som är relaterad till autentisering i ASP.net webb program/webb-API: er `App_Start/Startup.Auth.cs` finns i filen.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Logga in och logga ut](scenario-web-app-sign-user-sign-in.md)
