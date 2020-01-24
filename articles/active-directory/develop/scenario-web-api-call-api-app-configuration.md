---
title: 'Konfigurera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er (appens kod konfiguration)'
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dd38cb58e6e6db9767be9adb8f299107601de580
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701781"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Ett webb-API som anropar webb-API: er kod konfiguration

När du har registrerat ditt webb-API kan du konfigurera koden för programmet.

Den kod som du använder för att konfigurera ditt webb-API så att det anropar underordnade webb-API: er som bygger på den kod som används för att skydda ett webb-API. Mer information finns i [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod prenumererad på OnTokenValidated

Ovanpå kod konfigurationen för alla skyddade webb-API: er måste du prenumerera på verifieringen av Bearer-token som du får när ditt API anropas:

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

## <a name="on-behalf-of-flow"></a>On-behalf-of-flöde

Metoden AddAccountToCacheFromJwt () måste vara:

- Instansiera ett Microsoft Authentication Library (MSAL)-konfidentiellt klient program.
- Anropa metoden `AcquireTokenOnBehalf`. Det här anropet utbyter Bearer-token som hämtades av klienten för webb-API: et mot en Bearer-token för samma användare, men har API-anropet till ett underordnat API.

### <a name="instantiate-a-confidential-client-application"></a>Instansiera ett konfidentiellt klient program

Det här flödet är endast tillgängligt i det konfidentiella klient flödet, så att det skyddade webb-API: et tillhandahåller klientautentiseringsuppgifter (klient hemlighet eller certifikat) till [ConfidentialClientApplicationBuilder-klassen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via antingen `WithClientSecret`-eller `WithCertificate`-metoden.

![Lista över IConfidentialClientApplication-metoder](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

Slutligen, i stället för att bevisa sin identitet via en klient hemlighet eller ett certifikat, kan konfidentiella klient program bevisa sin identitet genom att använda klientens kontroll.
Mer information om det här avancerade scenariot finns i [konfidentiell klient kontroll](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Så här anropar du på uppdrag av

Du gör OBO-anropet genom att anropa [AcquireTokenOnBehalf-metoden](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) i `IConfidentialClientApplication`-gränssnittet.

Klassen `UserAssertion` skapas utifrån Bearer-token som tas emot av webb-API: et från sina egna klienter. Det finns [två konstruktorer](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* En som tar en JSON Web Token (JWT) Bearer-token
* En som har någon typ av användar kontroll, en annan typ av säkerhetstoken, vars typ sedan anges i en ytterligare parameter med namnet `assertionType`

![Egenskaper och metoder för UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

I praktiken används ofta OBO-flödet för att hämta en token för ett underordnat API och lagra det i MSAL.NET-användartoken. Det gör du så att andra delar av webb-API: n senare kan anropa de [åsidosättningar](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) av ``AcquireTokenOnSilent`` som anropar de underordnade API: erna. Det här anropet kommer att uppdatera tokens, om det behövs.

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

Du kan också se ett exempel på OBO Flow-implementering i [Node. js och Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

Mer information om OBO-protokollet finns i [Microsoft Identity Platform och OAuth 2,0 on-behalf-of Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md)
