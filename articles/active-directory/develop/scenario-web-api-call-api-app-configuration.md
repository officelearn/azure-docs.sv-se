---
title: 'Konfigurera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett webb-API som anropar webb-API:er (appens kodkonfiguration)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991015"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Ett webb-API som anropar webb-API:er: Kodkonfiguration

När du har registrerat webb-API:et kan du konfigurera koden för programmet.

Koden som du använder för att konfigurera webb-API:et så att det anropar underordnade webb-API:er bygger ovanpå den kod som används för att skydda ett webb-API. Mer information finns i [Skyddat webb-API: Appkonfiguration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod som prenumererar på OnTokenValidated

Utöver kodkonfigurationen för skyddade webb-API:er måste du prenumerera på valideringen av innehavartoken som du får när ditt API anropas:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>För flödet för första ä 2018

Metoden AddAccountToCacheFromJwt() måste:

- Instansiera ett msal-klientprogram (Microsoft Authentication Library).
- Anropa `AcquireTokenOnBehalf` metoden. Det här anropet utbyter innehavartoken som har förvärvats av klienten för webb-API:et mot en innehavartoken för samma användare, men den har API-anropet för ett nedströms-API.

### <a name="instantiate-a-confidential-client-application"></a>Instansiera ett konfidentiellt klientprogram

Det här flödet är endast tillgängligt i det konfidentiella klientflödet, så att det skyddade webb-API:et tillhandahåller klientuppgifter `WithCertificate` (klienthemlighet eller certifikat) till [klassen ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via antingen metoden `WithClientSecret` eller metoden.

![Lista över metoder för IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Slutligen, i stället för att bevisa sin identitet via en klienthemlighet eller ett certifikat, kan konfidentiella klientprogram bevisa sin identitet med hjälp av klientpåståenden.
Mer information om det här avancerade scenariot finns i [Konfidentiella klientpåståenden](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Hur man ringer på uppdrag av

Du ringer OBO-anropet (On-Behalf-Of) genom att anropa metoden `IConfidentialClientApplication` [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) i gränssnittet.

Klassen `UserAssertion` är byggd från innehavartoken som tas emot av webb-API:et från sina egna klienter. Det finns [två konstruktörer:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* En som tar en JVV-tecken (JWT) (JWT)
* En som tar någon form av användarpåstående, en annan typ av säkerhetstoken, vars typ sedan anges i en ytterligare parameter med namnet`assertionType`

![Egenskaper och metoder för användarmontering](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

I praktiken används OBO-flödet ofta för att hämta en token för ett nedströms-API och lagra det i MSAL.NET användartokencache. Du gör detta så att andra delar av webb-API:et senare kan anropa [åsidosättningar](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) av ``AcquireTokenOnSilent`` för att anropa underordnade API:er. Det här anropet har effekten att uppdatera token, om det behövs.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

OBO-flödet (On-behalf-of) används för att hämta en token för att anropa det underordnade webb-API:et. I det här flödet tar webb-API:et emot en innehavartoken med användardelegerat behörigheter från klientprogrammet och sedan utbyter den här token för en annan åtkomsttoken för att anropa det underordnade webb-API:et.

Koden nedan använder Spring Security-ramverkets `SecurityContextHolder` i webb-API:et för att hämta den validerade innehavartoken. Den använder sedan MSAL Java-biblioteket för att hämta `acquireToken` en `OnBehalfOfParameters`token för nedströms API med hjälp av anropet med . MSAL cachelagrar token så att efterföljande `acquireTokenSilently` anrop till API kan använda för att hämta den cachelagrade token.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

OBO-flödet (On-behalf-of) används för att hämta en token för att anropa det underordnade webb-API:et. I det här flödet tar webb-API:et emot en innehavartoken med användardelegerat behörigheter från klientprogrammet och sedan utbyter den här token för en annan åtkomsttoken för att anropa det underordnade webb-API:et.

Ett Python-webb-API måste använda vissa mellanprogram för att validera innehavartoken som tagits emot från klienten. Webb-API:et kan sedan hämta åtkomsttoken för nedströms-API med MSAL Python-biblioteket genom att anropa [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metoden. Ett exempel på hur du använder det här API:et finns i [testkoden för microsoft-authentication-library-for-python på GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Se också diskussionen om [problem 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) i samma databas för en metod som kringgår behovet av ett program på mellannivå.

---

Du kan också se ett exempel på OBO-flödesimplementering i [Node.js och Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

Mer information om OBO-protokollet finns i [Microsofts identitetsplattform och OAuth 2.0 on-Behalf-Of-Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API:er: Skaffa en token för appen](scenario-web-api-call-api-acquire-token.md)
