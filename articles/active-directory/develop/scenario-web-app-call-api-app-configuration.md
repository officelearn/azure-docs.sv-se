---
title: 'Konfigurera webb program som anropar webb-API: er – Microsoft Identity Platform | Azure'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83523fd12700789fb5c34230d529e06c0b284147
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964993"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webbapp som anropar webb-API: er – kod konfiguration

Som det visas i [scenariot för webbappen loggar in användare](scenario-web-app-sign-user-overview.md), använder webbappen OAuth 2.0 [Authorization Code Flow](v2-oauth2-auth-code-flow.md) för att logga in användaren. Det här flödet är i två delar:

1. Begär en auktoriseringskod. Den här delen delegerar till Microsoft Identity Platform en privat dialog ruta med användaren. Användaren loggar in och medskickas till användningen av webb-API: er. När den här privata dialog rutan avslutas får programmet en auktoriseringskod på omdirigerings-URI: n.
1. Begär en åtkomsttoken för API: et genom att lösa in auktoriseringskod.

[Användar scenariot för webbappens inloggningar](scenario-web-app-sign-user-overview.md) utförde bara det första benet. Lär dig här, hur du ändrar ditt webb-API som loggar in användare, så att det nu anropar webb-API: er.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotek som stöder webb program scenarier

Biblioteken som stöder Authorization Code Flow för Web Apps är:

| MSAL-bibliotek | Beskrivning |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds är .NET Framework-och .NET Core-plattformar (inte UWP, Xamarin. iOS och Xamarin. Android som dessa plattformar används för att bygga offentliga klient program) |
| ![MSAL python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Utveckling pågår – i offentlig för hands version |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Utveckling pågår – i offentlig för hands version |

Välj den flik som motsvarar den plattform som du är intresse rad av:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Med tanke på att att låta en användare logga in delegeras till mellanprogram, Open ID Connect (OIDC), som du vill koppla samman i OIDC-processen. Hur man gör beror på vilket ramverk du använder.
När det gäller ASP.NET Core ska du prenumerera på OIDC-händelser mellan olika händelser. Principen är att:

- Du kommer att låta ASP.NET Core begära en auktoriseringskod genom att ansluta mellan öppna-ID. Genom att göra den här ASP.NET/ASP.NET-kärnan kan användaren logga in och godkänna,
- Du prenumererar på mottagning av auktoriseringskod från webbappen. Den här prenumerationen görs via C# ett ombud.
- När auth-koden tas emot använder du MSAL-bibliotek för att lösa in koden och de resulterande åtkomsttoken och uppdaterade token lagras i token-cachen. Därifrån kan du använda cachen i andra delar av programmet, till exempel i kontrollanter, för att hämta andra token tyst.

Kodfragment i den här artikeln och följande extraheras från den [stegvisa självstudien för ASP.net Core Web App, kapitel 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Du kanske vill referera till den här självstudien för fullständig implementerings information.

> [!NOTE]
> Om du vill förstå fullständigt kodfragmenten nedan måste du vara bekant med [ASP.net Core fundament ALS](https://docs.microsoft.com/aspnet/core/fundamentals)och särskilt [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) och [alternativ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Med tanke på att att låta en användare logga in delegeras till mellanprogram, Open ID Connect (OIDC), som du vill koppla samman i OIDC-processen. Hur man gör beror på vilket ramverk du använder.
När det gäller ASP.NET kommer du att prenumerera på OIDC-händelser med mellanprogram. Principen är att:

- Du kommer att låta ASP.NET Core begära en auktoriseringskod genom att ansluta mellan öppna-ID. Genom att göra den här ASP.NET/ASP.NET-kärnan kan användaren logga in och godkänna,
- Du prenumererar på mottagning av auktoriseringskod från webbappen. Detta är ett C# ombud.
- När auth-koden tas emot använder du MSAL-bibliotek för att lösa in koden. De resulterande åtkomsttoken och uppdateringstoken är, och lagras sedan i token cache. Därifrån kan du använda cachen i andra delar av programmet, till exempel i kontrollanter, för att hämta andra token tyst.

Kodfragment i den här artikeln och följande extraheras från [exemplet på ASP.net-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="javatabjava"></a>[Java](#tab/java)

Kodfragment i den här artikeln och följande extraheras från Java-webbappen som anropar exempel för Java-webbappar i [Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) MSAL.
Exemplet tillåter för närvarande MSAL Java att producera URL: en för auktoriseringskod och hanterar navigeringen till godkännande slut punkten för Microsoft Identity Platform. Du kan också använda Sprint säkerhet för att logga in användaren. Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kodfragment i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Exempel på python-webbapp.
Exemplet tillåter för närvarande MSAL. Python skapar auktoriseringskod-URL: en och hanterar navigeringen till Microsoft Identity Platform godkänner slut punkten. Du kanske vill referera till det här exemplet för fullständig implementerings information.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod som löser in auktoriseringskod

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

I ASP.NET Core är principen i `Startup.cs`s filen. Du vill prenumerera på `OnAuthorizationCodeReceived` öppna ID Connect-händelsen och anropa MSAL från den här händelsen. NET-metoden `AcquireTokenFromAuthorizationCode`, som har att lagra i token cache, åtkomsttoken för den begärda `scopes`och en uppdateringstoken som ska användas för att uppdatera åtkomsttoken när den upphör att gälla, eller för att få en token åt samma användare, men för en annan resurs.

I övningen försöker [ASP.net Core Web App-självstudie](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) att ge dig en återanvändbar kod för dina webb program.

Här är [Start. cs # L40-L42-](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) koden, med anropet till `AddMicrosoftIdentityPlatformAuthentication`-metoden som lägger till autentisering till webbappen och `AddMsal` som lägger till möjligheten att anropa webb-API: er. Anropet till `AddInMemoryTokenCaches` är att välja en token cache-implementering bland de som är möjliga:

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` definieras i [konstanter. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Du har redan undersökt innehållet i `AddMicrosoftIdentityPlatformAuthentication` i [en webbapp som loggar in användare – kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>Metoden AddMsal

Koden för `AddMsal` finns i [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

- ASP.NET Core webbapp begär både en IDToken för användaren och en kod för autentisering (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- `offline_access` omfånget läggs till. Det behövs för att användaren ska kunna hämta en uppdateringstoken.
- appen prenumererar på OIDC `OnAuthorizationCodeReceived`-händelsen och löser anropet med MSAL.NET, som är inkapslat i en återanvändbar komponent som implementerar `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoden TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync`-metoden finns i [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Det säkerställer att:

- ASP.NET försöker inte lösa in autentiseringsmetoden parallellt med MSAL.NET (`context.HandleCodeRedemption();`)
- Anspråk i IDToken är tillgängliga för MSAL för att beräkna en token cache-nyckel för användarens konto
- MSAL.NET-programmet instansieras vid behov
- koden löses av MSAL.NET-programmet
- Den nya ID-token delas med ASP.NET Core (under anropet till `context.HandleCodeRedemption(null, result.IdToken);`). Åtkomsttoken delas inte med ASP.NET Core. Den finns kvar i MSAL.NET token-cachen som är associerad med användaren, där den är redo att användas i ASP.NET Core kontrollanter.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

I ASP.NET Core använder det konfidentiella klient programmet information som finns i HttpContext. Nås via `CurrentHttpContext` egenskap, HttpContext, som är associerat med begäran, vet om webb adressen för webbappen och den inloggade användaren (i en `ClaimsPrincipal`). `BuildConfidentialClientApplication` använder också ASP.NET Core-konfigurationen, som har avsnittet "AzureAD", som är kopplat till båda:

- `_applicationOptions` data struktur av typen [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- `azureAdOptions`-instansen av typen [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) som definierats i ASP.net Core `Authentication.AzureAD.UI`. Slutligen måste programmet hantera token-cacheminnen. Du lär dig mer om det här i nästa avsnitt.

Koden för `GetOrBuildConfidentialClientApplication()`-metoden finns i [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Den använder medlemmar som har matats in av beroende inmatning (skickades i konstruktorn för TokenAcquisition i [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

För att summera, kommer `AcquireTokenByAuthorizationCode` faktiskt lösa in den auktoriseringskod som begärts av ASP.NET och hämtar de token som läggs till i MSAL.NET cache för användartoken. Därifrån används de, i ASP.NET Core kontrollanter.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Hur ASP.NET hanterar saker liknar ASP.NET Core, förutom att konfigurationen av OpenIdConnect och prenumerationen på `OnAuthorizationCodeReceived`-händelsen inträffar i [App_Start \STARTUP.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -filen. Du hittar liknande koncept som i ASP.NET Core, förutom att i ASP.NET måste du ange RedirectUri i [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Den här konfigurationen är mindre stabil än vad som görs i ASP.NET Core, eftersom du behöver ändra den när du distribuerar programmet.

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

Se i [Web App som loggar in användare – kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) för att förstå hur Java-exemplet hämtar auktoriseringskod. När appen har tagits emot av appen [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) ombudet till `AuthHelper.processAuthenticationCodeRedirect`-metoden i [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)och anropar `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

Metoden `getAuthResultByAuthCode` definieras i [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Det skapar en MSAL `ConfidentialClientApplication` och anropar `acquireToken()` med `AuthorizationCodeParameters` som skapats från auktoriseringskod.

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

När auktoriseringskod-flödet har begärts som det visas i [webb program som loggar in användare – kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), tas auktoriseringskod in i den `authorized` funktionen som drar vägar från/getAToken-URL: en. Se [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

I stället för en klient hemlighet kan det konfidentiella klient programmet också bevisa sin identitet med hjälp av ett klient certifikat eller en klient kontroll.
Att använda klient kontroll är ett avancerat scenario som beskrivs i [klientens kontroll](msal-net-client-assertions.md)

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> I Web Apps (eller webb-API: er som själva fakta) skiljer sig implementationen för token-cacheminnet från programappar för cachelagring av appar (som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Det är viktigt att av säkerhets-och prestanda skäl säkerställa att för webbappar och webb-API: er, bör det finnas ett token cache per användare (per konto). Du måste serialisera token-cachen för varje konto.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I självstudien om ASP.NET Core används beroende inmatning för att bestämma hur token cache ska implementeras i Startup.cs-filen för ditt program. Microsoft. Identity. Web levereras med ett antal förskapade token cache-serialiserare som beskrivs i [cachelagring av token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). En intressant möjlighet är att välja ASP.NET Core [distribuerade cacheminnen](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Mer information om token cache-providers finns även i [självstudierna för ASP.net Core Web Apps | Fasen token Caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) i självstudien

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I Web Apps (eller webb-API: er som själva fakta) skiljer sig implementationen för token-cacheminnet från programapparna för cachelagring av appar (som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache). Den kan använda ASP.NET-sessionen eller Server minnet. Se till exempel hur cache-implementeringen kopplas efter att MSAL.NET-programmet har skapats i [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java tillhandahåller metoder för att serialisera och deserialisera token cache. Java-exemplet hanterar serialiseringen från sessionen, som illustreras i metoden `getAuthResultBySilentFlow` i [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Detaljerna i `SessionManagementHelper`-klassen finns i[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-exemplet säkerställs cache by-kontot genom att du återskapar ett konfidentiellt klient program för varje begäran och serialiserar det i kolvens cache:

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

När användaren sedan loggar in en token lagras i token cache. Nu ska vi se hur det används i andra delar av webbappen.

> [!div class="nextstepaction"]
> [Logga in på webbappen](scenario-web-app-call-api-sign-in.md)
