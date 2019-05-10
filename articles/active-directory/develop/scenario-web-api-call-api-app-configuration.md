---
title: 'Webb-API som anrop underordnat webb API: er (appens kod konfiguration) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API att anrop webb-API: er (konfiguration av appens kod)'
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
ms.openlocfilehash: 3dedef2d22df9c8c81410296bdb0c4814bd98b80
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507120"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webb-API att anrop webb-API: er – kod konfiguration

När du har registrerat ditt webb-API, kan du konfigurera koden för programmet.

Koden för att konfigurera ditt webb-API så att den anropar underordnade webb-API: er som bygger på den kod som används för att projicera ett webb-API. Mer information finns i [skyddade webb-API - appkonfiguration](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Felkod som prenumererar på OnTokenValidated

Du måste prenumerera valideringen av ägartoken som tas emot när ditt API anropas ovanpå kod-konfigurationen för alla skyddade webb-API: er:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of-flöde

Metoden AddAccountToCacheFromJwt() måste:

- Skapa en instans av ett konfidentiellt MSAL klientprogram.
- Anropa `AcquireTokenOnBehalf` att utbyta ägartoken som köptes av klienten för webb-API, mot en ägartoken för samma användare, men för vårt API för att anropa en underordnad API.

### <a name="instantiate-a-confidential-client-application"></a>Skapa en instans av ett konfidentiellt klientprogram

Det här flödet är endast tillgänglig i konfidentiell klientflödet så skyddade webb-API ger klientens autentiseringsuppgifter (klienthemlighet eller certifikat) till den [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) via den `WithClientSecret` eller `WithCertificate`metoder, respektive.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

### <a name="how-to-call-on-behalf-of"></a>Hur du anropar on-behalf-of

On-behalf-of (OBO)-anrop görs genom att anropa den [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) metoden på den `IConfidentialClientApplication` gränssnitt.

Den `ClientAssertion` är byggd från ägartoken som tagits emot av webb-API från sina egna klienter. Det finns [två konstruktorer](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), en som tar en JWT-ägarautentisering token och en som tar alla slags användaren försäkran (en annan typ av säkerhetstoken, vilka sedan har angetts i en extra parameter med namnet `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

I praktiken OBO flödet ofta används för att hämta en token för en underordnad API och lagra den på MSAL.NET användaren tokens cacheminne så att andra delar av webb-API kan senare anropa på den [åsidosätter](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) av ``AcquireTokenOnSilent`` att anropa de API: er för underordnade. Detta påverkar den för att uppdatera token, om det behövs.

```CSharp
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

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

## <a name="protocol"></a>Protokoll

Läs mer om on-behalf-of-protokollet, [Microsoft identity-plattformen och OAuth 2.0 Behalf flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token som appen](scenario-web-api-call-api-acquire-token.md)
