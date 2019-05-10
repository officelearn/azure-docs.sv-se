---
title: Webbapp som loggar in användare (kod-konfiguration) – Microsoft identity-plattformen
description: Lär dig att skapa en webbapp som loggar in användare (konfiguration av kod)
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
ms.openlocfilehash: b2204fe3e08b3c4b909ddc8b7ade4cec219d34fb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406639"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webbapp som loggar in användare - kod-konfiguration

Lär dig hur du konfigurerar koden för din webbapp som loggar in användare.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotek som används för att skydda Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotek som används för att skydda en Webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitet modelltillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity-tillägg för .NET föreslår används direkt av ASP.NET och ASP.NET Core, en uppsättning DLL: er som kör både .NET Framework och .NET Core. Från en ASP.NET/ASP.NET Core webb-app kan du styra tokenvalidering med hjälp av den **TokenValidationParameters** klass (särskilt i vissa situationer ISV) |

## <a name="aspnet-core-configuration"></a>ASP.NET Core-konfiguration

### <a name="application-configuration-files"></a>Konfigurationsfiler för programmet

I ASP.NET Core, en Web application logga in användare med Microsoft identity-plattformen konfigureras via den `appsettings.json` filen. De inställningar som du måste fylla är:

- molnet `Instance` om du vill att din app ska köras i nationella moln
- målgruppen i `tenantId`
- den `clientId` för ditt program, som kopieras från Azure-portalen.

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
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

Det finns en annan fil som innehåller URL: en i ASP.NET Core (`applicationUrl`) och SSL-porten (`sslPort`) för ditt program, samt olika profiler.

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

I Azure-portalen, svars-URI: er som du behöver registrera i den **autentisering** sidan för ditt program måste matcha dessa URL: er, det vill säga för två konfigurationsfilerna ovan kan vara de `https://localhost:44321/signin-oidc` som applicationUrl är `http://localhost:3110` men `sslPort` är angivna (44321), och `CallbackPath` är `/signin-oidc` enligt definitionen i den `appsettings.json`.
  
På samma sätt kan logga ut URI skulle anges till `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Initieringskod

I ASP.NET Core-Webbappar och webb-API: er, kod gör initieringen av finns i den `Startup.cs` filen, och om du vill lägga till autentisering med v2.0 för Microsoft Identity-plattformen (tidigare Azure AD), måste du lägga till följande kod. Kommentarerna i koden ska vara självförklarande.

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

ASP.NET-programmet är konfigurerat via den `Web.Config` fil

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

Koden som är relaterad till autentisering i ASP.NET-webbapp / webb-API: er finns i den `App_Start/Startup.Auth.cs` filen.

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
