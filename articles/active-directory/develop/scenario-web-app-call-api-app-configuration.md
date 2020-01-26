---
title: 'Konfigurera en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du konfigurerar koden för en webbapp som anropar webb-API: er'
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759168"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>En webbapp som anropar webb-API: er kod konfiguration

Som du ser i [webbappen som loggar in i användar](scenario-web-app-sign-user-overview.md) scenariot använder webbappen [OAuth 2,0-auktoriseringskod](v2-oauth2-auth-code-flow.md) för att signera användaren i. Det här flödet har två steg:

1. Begär en auktoriseringskod. Den här delen delegerar en privat dialog med användaren till Microsoft Identity Platform. Under den dialogen loggar användaren in och samverkar till användningen av webb-API: er. När den privata dialog rutan avslutas får webbappen en auktoriseringskod på omdirigerings-URI: n.
1. Begär en åtkomsttoken för API: et genom att lösa in auktoriseringskod.

[Webbappen som loggar in i användar](scenario-web-app-sign-user-overview.md) scenarier täcker bara det första steget. Här kan du lära dig hur du ändrar din webbapp så att den inte bara signerar användare i, men nu anropar webb-API: er.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotek som stöder scenarier med webb program

Följande bibliotek i Microsoft Authentication Library (MSAL) stöder Authorization Code Flow för Web Apps:

| MSAL-bibliotek | Beskrivning |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Stöd för .NET Framework-och .NET Core-plattformar. Stöds inte Universell Windows-plattform (UWP), Xamarin. iOS och Xamarin. Android, eftersom dessa plattformar används för att bygga offentliga klient program. |
| ![MSAL python](media/sample-v2-code/logo_python.png) <br/> MSAL för Python | Stöd för python-webbprogram. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL för Java | Stöd för Java-webbprogram. |

Välj fliken för den plattform som du är intresse rad av:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Eftersom användar inloggning är delegerad till mellanprogram varan Open ID Connect (OIDC) måste du interagera med OIDC-processen. Hur du interagerar beror på vilket ramverk du använder.

För ASP.NET Core prenumererar du på mellanprogram OIDC händelser:

- Du får ASP.NET Core begära en auktoriseringskod med hjälp av Open-ID Connect-mellanprogram. ASP.NET eller ASP.NET Core gör det möjligt för användaren att logga in och medgivande.
- Du prenumererar på webbappen för att ta emot auktoriseringsregeln. Den här prenumerationen görs med hjälp C# av ett ombud.
- När auktoriseringskod tas emot använder du MSAL-bibliotek för att lösa in den. De resulterande åtkomsttoken och uppdaterade tokens lagras i token-cachen. Cachen kan användas i andra delar av programmet, till exempel kontrollanter, för att hämta andra token tyst.

Kod exempel i den här artikeln och följande extraheras från den [stegvisa självstudien för ASP.net Core Web App, kapitel 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Du kanske vill referera till den här självstudien för fullständig implementerings information.

> [!NOTE]
> För att fullständigt förstå kod exemplen måste du vara bekant med [ASP.net Core fundament ALS](https://docs.microsoft.com/aspnet/core/fundamentals), särskilt med [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) och [alternativ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Eftersom användar inloggning är delegerad till mellanprogram varan Open ID Connect (OIDC) måste du interagera med OIDC-processen. Hur du interagerar beror på vilket ramverk du använder.

För ASP.NET ska du prenumerera på OIDC händelser för mellanprogram:

- Du får ASP.NET Core begära en auktoriseringskod med hjälp av Open-ID Connect-mellanprogram. ASP.NET eller ASP.NET Core gör det möjligt för användaren att logga in och medgivande.
- Du prenumererar på webbappen för att ta emot auktoriseringsregeln. Den här prenumerationen görs med hjälp C# av ett ombud.
- När auktoriseringskod tas emot använder du MSAL-bibliotek för att lösa in den. De resulterande åtkomsttoken och uppdaterade tokens lagras i token-cachen. Cachen kan användas i andra delar av programmet, till exempel kontrollanter, för att hämta andra token tyst.

Kod exempel i den här artikeln och följande extraheras från exemplet på [ASP.net-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Du kanske vill referera till det exemplet för fullständig implementerings information.

# <a name="javatabjava"></a>[Java](#tab/java)

Kod exempel i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), ett webb-app-exempel som använder MSAL för Java.
Exemplet tillåter för närvarande MSAL för Java att producera URL: en för auktoriseringskod och hanterar navigeringen till Authorization-slutpunkten för Microsoft Identity Platform. Du kan också använda Sprint säkerhet för att logga in användaren i. Du kanske vill referera till exemplet för fullständig implementerings information.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kod exempel i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), ett exempel på en webbapp som använder MSAL. Python.
Exemplet tillåter för närvarande MSAL. Python genererar URL: en för auktoriseringskod och hanterar navigeringen till slut punkten för auktorisering för Microsoft Identity Platform. Du kanske vill referera till exemplet för fullständig implementerings information.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod som löser in auktoriseringskod

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

I ASP.NET Core i `Startup.cs`-filen prenumererar du på händelsen `OnAuthorizationCodeReceived` OpenID Connect. I den här händelsen anropar du metoden MSAL.NET `AcquireTokenFromAuthorizationCode`. Den här metoden lagrar följande tokens i token-cachen:

- Åtkomsttoken *för den* begärda `scopes`.
- En *uppdateringstoken*. Denna token används för att uppdatera åtkomsttoken när den är nära förfallo datum, eller för att hämta en annan token åt samma användare, men för en annan resurs.

I [självstudien för ASP.net Core webb program](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) får du en återanvändbar kod för dina webbappar.

Följande är koden från [startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). IT-funktioner anropar:

- `AddMicrosoftIdentityPlatformAuthentication`-metoden, som lägger till autentisering till webbappen.
- Metoden `AddMsal`, som lägger till möjligheten att anropa webb-API: er.
- `AddInMemoryTokenCaches`-metoden, som är om att välja en token-cache-implementering.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` definieras i [konstanter. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Du har redan undersökat innehållet i `AddMicrosoftIdentityPlatformAuthentication` i [en webbapp som loggar in i användarnas kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metoden AddMsal

Koden för `AddMsal` finns i [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Metoden `AddMsal` säkerställer att:

- Webbappen för ASP.NET Core begär både en ID-token för användaren och en kod för autentisering (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- `offline_access` omfånget läggs till. Det här omfånget hämtar användar medgivande för programmet för att hämta en uppdateringstoken.
- Appen prenumererar på OIDC `OnAuthorizationCodeReceived`-händelsen och löser anropet med hjälp av MSAL.NET, som kapslas in här i en återanvändbar komponent som implementerar `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoden TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync`-metoden finns i [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Det säkerställer att:

- ASP.NET försöker inte lösa in autentiseringsmetoden parallellt med MSAL.NET (`context.HandleCodeRedemption();`).
- Anspråk i ID-token är tillgängliga för MSAL för att beräkna en token cache-nyckel för användarens konto.
- En instans av MSAL.NET-programmet skapas vid behov.
- Koden löses av MSAL.NET-programmet.
- Den nya ID-token delas med ASP.NET Core under anropet till `context.HandleCodeRedemption(null, result.IdToken);`. Åtkomsttoken delas inte med ASP.NET Core. Den finns kvar i MSAL.NET token-cachen som är associerad med användaren, där den är redo att användas i ASP.NET Core kontrollanter.

Här är den relevanta koden för `TokenAcquisition`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Metoden TokenAcquisition. BuildConfidentialClientApplication

I ASP.NET Core använder det konfidentiella klient programmet information som finns i `HttpContext`. Den `HttpContext` som är associerad med begäran nås med hjälp av egenskapen `CurrentHttpContext`. `HttpContext` innehåller information om webb adressen till webbappen och om den inloggade användaren (i en `ClaimsPrincipal`). 

Metoden `BuildConfidentialClientApplication` använder också ASP.NET Core-konfigurationen. Konfigurationen innehåller avsnittet "AzureAD" och är också kopplat till båda följande element:

- `_applicationOptions` data struktur av typen [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- `azureAdOptions`-instansen av typen [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), som definieras i ASP.net Core `Authentication.AzureAD.UI`.

Slutligen måste programmet hantera token-cacheminnen. Du lär dig mer om det i nästa avsnitt.

Koden för `GetOrBuildConfidentialClientApplication()`-metoden finns i [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Den använder medlemmar som har matats in av beroende inmatning (skickades i konstruktorn för `TokenAcquisition` i [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Här är koden för `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Sammanfattning

`AcquireTokenByAuthorizationCode` är i själva verket den metod som används för att lösa in den auktoriseringskod som ASP.NET begär, och som hämtar de token som läggs till i MSAL.NET cache för användar-token. I cachen används tokens i ASP.NET Core styrenheter.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET hanterar saker på samma sätt som ASP.NET Core, förutom att konfigurationen av OpenID Connect och prenumerationen på `OnAuthorizationCodeReceived` inträffar i [App_Start \STARTUP.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -filen. Begreppen liknar dem i ASP.NET Core, förutom att i ASP.NET måste du ange `RedirectUri` i [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Den här konfigurationen är mindre stabil än den som finns i ASP.NET Core eftersom du måste ändra den när du distribuerar programmet.

Här är koden för Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Se [webbapp som loggar in användare: kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) för att förstå hur Java-exemplet hämtar auktoriseringskod. När appen har tagit emot koden [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegerar till `AuthHelper.processAuthenticationCodeRedirect`-metoden i [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Anropar `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Metoden `getAuthResultByAuthCode` definieras i [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Den skapar en MSAL `ConfidentialClientApplication`och anropar sedan `acquireToken()` med `AuthorizationCodeParameters` som skapats från auktoriseringskod.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Flow-flödet begärs som det visas i [webbappen som loggar in användare: kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Koden tas sedan emot i `authorized`-funktionen som drar vägar från `/getAToken` URL. Se [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) för den fullständiga kontexten för den här koden:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

I stället för en klient hemlighet kan det konfidentiella klient programmet också bevisa sin identitet genom att använda ett klient certifikat eller en klient kontroll.
Användningen av klientens intyg är ett avancerat scenario, som beskrivs i [klient kontroll](msal-net-client-assertions.md).

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> Implementeringen av token-cache för webbappar eller webb-API: er skiljer sig från implementeringen av Skriv bords program, som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache).
> Av säkerhets-och prestanda skäl är det viktigt att säkerställa att för webbappar och webb-API: er det finns ett token-cache per användar konto. Du måste serialisera token-cachen för varje konto.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I självstudien om ASP.NET Core används beroende inmatning för att bestämma hur token cache ska implementeras i Startup.cs-filen för ditt program. Microsoft. Identity. Web levereras med förbyggda token cache-serialiserare som beskrivs i [cachelagring av token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). En intressant möjlighet är att välja ASP.NET Core [distribuerade cacheminnen](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Mer information om token-cache-providers finns även i [självstudierna för ASP.net Core Web Apps | Fasen token Caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) i självstudien.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Implementeringen av token-cache för webbappar eller webb-API: er skiljer sig från implementeringen av Skriv bords program, som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementeringen av webbappar kan använda ASP.NET-sessionen eller Server minnet. Se till exempel hur cache-implementeringen kopplas efter att MSAL.NET-programmet har skapats i [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java tillhandahåller metoder för att serialisera och deserialisera token cache. Java-exemplet hanterar serialiseringen från sessionen, som du ser i metoden `getAuthResultBySilentFlow` i [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Detaljerna i `SessionManagementHelper`-klassen finns i [MSAL-exemplet för Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-exemplet är en cache per konto säkerställd genom att du återskapar ett konfidentiellt klient program för varje begäran och sedan serialiserar det i kolvens cache:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Nästa steg

När användaren loggar in lagras en token i token-cachen i det här läget. Nu ska vi se hur det används i andra delar av webbappen.

> [!div class="nextstepaction"]
> [En webbapp som anropar webb-API: ta bort konton från cachen vid global utloggning](scenario-web-app-call-api-sign-in.md)
