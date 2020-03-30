---
title: Konfigurera en webbapp som loggar in användare – Microsoft identity platform | Azure
description: Lär dig hur du skapar en webbapp som loggar in användare (kodkonfiguration)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51cd7ff97af4588139721930bd4d08ffd0f95e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297549"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webbapp som loggar in användare: Kodkonfiguration

Läs om hur du konfigurerar koden för webbappen som signerar användare.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotek för att skydda webbappar

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotek som används för att skydda en webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitetsmodelltillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Model Extensions för .NET används direkt av ASP.NET och ASP.NET Core och föreslår en uppsättning DLL-filer som körs på både .NET Framework och .NET Core. Från en ASP.NET eller ASP.NET Core-webbapp kan du styra tokenvalidering med hjälp av klassen **TokenValidationParameters** (i synnerhet i vissa partnerscenarier). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Stöd för Java-webbapplikationer |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Stöd för Python-webbprogram |

Välj fliken som motsvarar den plattform du är intresserad av:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Kodavsnitt i den här artikeln och följande extraheras från [den inkrementella självstudiekursen för ASP.NET Core-webbappen, kapitel 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Du kanske vill hänvisa till den här självstudien för fullständig implementeringsinformation.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kodavsnitt i den här artikeln och följande extraheras från [exemplet ASP.NET webbapp](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Du kanske vill läsa det här exemplet för fullständig implementeringsinformation.

# <a name="java"></a>[Java](#tab/java)

Kodavsnitt i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft graph-exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp) i MSAL Java.

Du kanske vill läsa det här exemplet för fullständig implementeringsinformation.

# <a name="python"></a>[Python](#tab/python)

Kodavsnitt i den här artikeln och följande extraheras från [Python-webbprogrammet som anropar Microsoft graph-exemplet](https://github.com/Azure-Samples/ms-identity-python-webapp) i MSAL Python.

Du kanske vill läsa det här exemplet för fullständig implementeringsinformation.

---

## <a name="configuration-files"></a>Konfigurationsfiler

Webbprogram som loggar in användare med hjälp av Microsofts identitetsplattform konfigureras vanligtvis via konfigurationsfiler. De inställningar som du behöver fylla i är:

- Molninstansen (`Instance`) om du vill att appen ska köras i nationella moln, till exempel
- Målgruppen i klient-ID (`TenantId`)
- Klient-ID`ClientId`( ) för ditt program, som kopierat från Azure-portalen

Ibland kan applikationer parametrized `Authority`av , vilket är `Instance` `TenantId`en sammanfogning av och .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core finns dessa inställningar i [filen appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) i avsnittet "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

I ASP.NET Core innehåller en annan fil[(properties\launchSettings.json)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)URL (`applicationUrl`) och`sslPort`TLS/SSL-porten ( ) för ditt program och olika profiler.

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

I Azure-portalen måste de svars-URI:er som du måste registrera på **sidan Autentisering** för ditt program matcha dessa webbadresser. För de två föregående konfigurationsfilerna skulle de vara `https://localhost:44321/signin-oidc`. Anledningen är `applicationUrl` `http://localhost:3110`att `sslPort` , men anges (44321). `CallbackPath`är `/signin-oidc`, enligt `appsettings.json`definitionen i .

På samma sätt skulle ut logga ut URI ställas in på `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET konfigureras programmet via filen [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) raderna 12 till 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
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

I Azure-portalen måste de svars-URI:er som du måste registrera på **sidan Autentisering** för ditt program matcha dessa webbadresser. Det vill säga, `https://localhost:44326/`de borde vara .

# <a name="java"></a>[Java](#tab/java)

I Java finns konfigurationen i filen [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) som finns under `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

I Azure-portalen måste de svars-URI:er som du måste `redirectUri` registrera på **autentiseringssidan** för ditt program matcha de instanser som programmet definierar. Det vill säga, `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`de borde vara och .

# <a name="python"></a>[Python](#tab/python)

Här är Python-konfigurationsfilen i [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Den här snabbstarten föreslår att du lagrar klienthemligheten i konfigurationsfilen för enkelhetens skull. I din produktionsapp vill du använda andra sätt att lagra din hemlighet, till exempel ett nyckelvalv eller en miljövariabel enligt beskrivningen i [Flasks dokumentation](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initieringskod

Initieringskoden är olika beroende på plattform. För ASP.NET Core och ASP.NET delegeras inloggningsanvändare till OpenID Connect mellanprogram. Mallen ASP.NET eller ASP.NET Core genererar webbprogram för Azure Active Directory (Azure AD) v1.0-slutpunkten. Vissa konfigurationer krävs för att anpassa dem till slutpunkten för Microsoft Identity Platform (v2.0). När det gäller Java, det hanteras av våren med samarbete med ansökan.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core-webbappar (och webb-API:er) skyddas programmet eftersom du har ett `[Authorize]` attribut på styrenheterna eller styrenhetens åtgärder. Det här attributet kontrollerar att användaren är autentiserat. Koden som initierar programmet finns i Startup.cs filen.

Om du vill lägga till autentisering med Microsofts identitetsplattform (tidigare Azure AD v2.0) måste du lägga till följande kod. Kommentarerna i koden bör vara självförklarande.

> [!NOTE]
> Om du startar projektet med standard-ASP.NET Core-webbprojekt i `dotnet new mvc`Visual studio `AddAzureAD` eller med hjälp av , är metoden som standard. Det beror på att de relaterade paketen läses in automatiskt.
>
> Om du skapar ett projekt från grunden och försöker använda följande kod föreslår vi att du lägger till NuGet-paketet **Microsoft.AspNetCore.Authentication.AzureAD.UI** i projektet för att göra `AddAzureAD` metoden tillgänglig.

Följande kod är tillgänglig från [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
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

Tilläggsmetoden `AddMicrosoftIdentityPlatformAuthentication` definieras i [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Det:

- Lägger till autentiseringstjänsten.
- Konfigurerar alternativ för att läsa konfigurationsfilen.
- Konfigurerar OpenID Connect-alternativen så att den använda behörigheten är slutpunkten för Microsoft-identitetsplattform (tidigare Azure AD v2.0).
- Validerar utfärdaren av token.
- Säkerställer att anspråk som motsvarar namn `preferred_username` mappas från anspråket i ID-token.

Förutom konfigurationen kan du ange namnet på konfigurationsavsnittet när du anropar `AddMicrosoftIdentityPlatformAuthentication`. Som standard är `AzureAd`det .

Om du spårar OpenId Connect mellanprogram kan du felsöka webbprogrammet om autentiseringen inte fungerar. Inställning `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` till visar hur information utarbetas av uppsättningen av ASP.NET Core mellanprogram som det fortskrider från HTTP-svar på identiteten för användaren i `HttpContext.User`.

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
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
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
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

Klassen `AadIssuerValidator` gör det möjligt för utfärdaren av token att valideras i många fall. Den här klassen fungerar med en v1.0- eller v2.0-token, ett program med en eller flera innehavare eller ett program som loggar in användare med sina personliga Microsoft-konton i Det offentliga Azure-molnet eller nationella molnen. Den är tillgänglig från [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Koden som är relaterad till autentisering i en ASP.NET webbapp och webb-API finns i filen [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java-exemplet använder ramverket Våren. Programmet är skyddat eftersom du implementerar ett filter som fångar upp varje HTTP-svar. I snabbstarten för Java-webbappar finns `AuthFilter` det här filtret i `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

Filtret bearbetar Auktoriseringskodflödet OAuth 2.0`isAuthenticated()` och kontrollerar om användaren autentiseras (metoden). Om användaren inte autentiseras beräknas URL:en för slutpunkterna för Azure AD-auktorisering och webbläsaren omdirigeras till den här URI:n.

När svaret anländer, som innehåller auktoriseringskoden, hämtas token med hjälp av MSAL Java. När den slutligen tar emot token från tokenslutpunkten (på omdirigera URI) loggas användaren in.

Mer information finns `doFilter()` i metoden i [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Koden för `doFilter()` är skriven i en något annorlunda ordning, men flödet är den som beskrivs.

Mer information om det auktoriseringskodflöde som den här metoden utlöser finns i [Microsoft identity platform och OAuth 2.0 auktoriseringskodflöde](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Python-exemplet använder Kolv. Initieringen av Flask och MSAL Python görs i [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

I nästa artikel får du lära dig hur du utlöser inloggning och ut logga ut.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Inloggning och utloggning](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Inloggning och utloggning](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Inloggning och utloggning](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Inloggning och utloggning](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
