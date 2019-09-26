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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1453821561ab7bb361fbb3e5d57634cf23a7be2c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310065"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webbapp som loggar in användare – kod konfiguration

Lär dig hur du konfigurerar koden för din webbapp som loggar in användare.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotek som används för att skydda Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotek som används för att skydda en webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitets modells tillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions för .NET används direkt av ASP.NET och ASP.NET Core och avser en uppsättning dll: er som kör både på .NET Framework och .NET Core. Från en ASP.NET/ASP.NET Core-webbapp kan du kontrol lera token-verifiering med **TokenValidationParameters** -klassen (särskilt i vissa ISV-scenarier) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL för Java – för närvarande i offentlig för hands version |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL för python – för närvarande i offentlig för hands version |

Välj den flik som motsvarar den plattform som du är intresse rad av:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Kodfragment i den här artikeln och följande extraheras från den stegvisa självstudien för [ASP.net Core Web App, kapitel 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Du kanske vill referera till den här självstudien för fullständig implementerings information.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kodfragment i den här artikeln och följande extraheras från [ASP.net-webbappens exempel](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="javatabjava"></a>[Java](#tab/java)

Kodfragment i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j-webbappens exempel

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kodfragment i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Exempel på python-webbapp

Du kanske vill referera till det här exemplet för fullständig implementerings information.

---

## <a name="configuration-files"></a>Konfigurationsfiler

Webb program som loggar in användare med Microsoft Identity Platform konfigureras vanligt vis via konfigurationsfiler. De inställningar som du behöver fylla i är:

- molnet `Instance` om du vill att din app ska köras (till exempel i nationella moln)
- mål gruppen i`tenantId`
- `clientId` för ditt program, som kopieras från Azure Portal.

Ibland kan program parametrized av `authority`, som är sammanfogningen `instance` av och`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core finns de här inställningarna i filen [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) i avsnittet "AzureAD".

```Json
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

I ASP.net Core finns det en annan fil- [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) som innehåller URL:`applicationUrl`en () och SSL-`sslPort`porten () för ditt program och olika profiler.

```Json
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
  
På samma sätt skulle utloggnings-URI: n vara inställd på `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET konfigureras programmet via [Web. config-](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) fil raderna 12-15

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

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. det vill säga. `https://localhost:44326/`

# <a name="javatabjava"></a>[Java](#tab/java)

I Java finns konfigurationen i filen [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , som finns under`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha de redirectUris som definieras av programmet, det `http://localhost:8080/msal4jsample/secure/aad` vill säga och`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Här är python-konfigurationsfilen i [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Den här snabb starten planerar att lagra klient hemligheten i konfigurations filen för enkelhetens skull. I din webbapp skulle du vilja använda andra sätt att lagra din hemliga information, till exempel ett nyckel valv eller en miljö variabel enligt beskrivningen i dokumentationen för flaskor: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initierings kod

Initierings koden skiljer sig beroende på plattform. För ASP.NET Core-och ASP.NET delegeras inloggnings användare till OpenIDConnect mellanprogram. I dag har ASP.NET/ASP.NET Core-mallen generera webb program för Azure AD v 1.0-slutpunkten. Därför krävs en bit-konfiguration för att anpassa dem till Microsoft Identity Platform (v 2.0) slut punkten. När det gäller Java hanteras det av våren med programmets samarbete.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.net Core Web Apps (och webb-API: er) skyddas programmet eftersom du har ett `[Authorize]` attribut på styrenheterna eller styrenhets åtgärderna. Det här attributet kontrollerar att användaren är autentiserad. Koden som gör att programmet initieras finns i `Startup.cs` filen och, för att lägga till autentisering med Microsoft Identity Platform (tidigare Azure AD v 2.0), måste du lägga till följande kod. Kommentarerna i koden bör vara själv för klar Ande.

  > [!NOTE]
  > Om du startar ditt projekt med standard ASP.net Core-webbprojektet i Visual Studio `dotnet new mvc` eller använder `AddAzureAD` -metoden är tillgänglig som standard eftersom de relaterade paketen läses in automatiskt.
  > Men om du skapar ett projekt från grunden och försöker använda koden nedan rekommenderar vi att du lägger till NuGet-paketet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** i projektet för att göra `AddAzureAD` metoden tillgängligt.
  
Följande kod är tillgänglig från [Start. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

Är en tilläggs metod som definieras i [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) `AddMicrosoftIdentityPlatformAuthentication` Företaget

- lägger till Autentiseringstjänsten
- Konfigurera alternativ för att läsa konfigurations filen
- konfigurerar anslutnings alternativen för OpenID så att den använda utfärdaren är Microsoft Identity Platform (tidigare Azure AD v 2.0) slut punkt
- utfärdaren av token verifieras
- anspråken som motsvarar namnet mappas från "preferred_username"-anspråket i ID-token 

Förutom konfigurationen kan du ange när du anropar `AddMicrosoftIdentityPlatformAuthentication`:

- namnet på konfigurations avsnittet (som standard AzureAD)
- Om du vill spåra OpenIdConnect mellanprogram, som kan hjälpa dig att felsöka ditt webb program om autentiseringen inte fungerar: inställningen `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` till `true` visar hur information ska visas med en uppsättning ASP.net Core mellanprogram vara när den fortskrider från HTTP-svaret till användarens identitet i `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator` Klassen aktiverar att utfärdaren av token verifieras i många fall (v 1.0 eller v 2.0-token, en enskild klient eller flera klient program eller program som loggar in användare med sina personliga Microsoft-konton, i det offentliga Azure-molnet eller nationella moln). Den är tillgänglig från [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Koden som är relaterad till autentisering i ASP.NET webb program/webb-API: er finns i filen [App_Start/startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
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

# <a name="javatabjava"></a>[Java](#tab/java)

Java-exemplet använder våren-ramverket. Programmet skyddas eftersom du implementerar ett `Filter`, vilket fångar varje http-svar. I snabb starten av Java-webbappen finns `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`det här filtret. Filtret bearbetar OAuth 2,0 Authorization Code Flow och därför:

- verifierar om användaren är autentiserad (`isAuthenticated()` metod)
- Om användaren inte är autentiserad, beräknar den URL: en för Azure AD-auktoriserans slut punkter och omdirigerar webbläsaren till denna URI
- När svaret anländer, som innehåller det auth Code-flöde som kan msal4j förvärva token.
- När den slutligen tar emot token från token-slutpunkten (på omdirigerings-URI: n) är användaren inloggad.

Mer information finns `doFilter()` i metoden i [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Koden för `doFilter()` är skriven i en något annorlunda ordning, men flödet är det som beskrivs.

Se [Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md) för information om auktoriseringskod som utlöses av den här metoden

# <a name="pythontabpython"></a>[Python](#tab/python)

Python-exemplet använder kolv. Utinitieringen av kolven och MSAL. Python görs i [appen. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Nästa steg

I nästa artikel får du lära dig hur du utlöser inloggning och utloggning.

> [!div class="nextstepaction"]
> [Logga in och logga ut](scenario-web-app-sign-user-sign-in.md)
