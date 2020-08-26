---
title: 'Konfigurera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er (appens kod konfiguration)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e9faea3462ae953e474b5053b651808b03f07c23
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855458"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Ett webb-API som anropar webb-API: er kod konfiguration

När du har registrerat ditt webb-API kan du konfigurera koden för programmet.

Den kod som du använder för att konfigurera ditt webb-API så att det anropar underordnade webb-API: er som bygger på den kod som används för att skydda ett webb-API. Mer information finns i [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klient hemligheter eller klient certifikat

Eftersom ditt webb-API nu anropar ett underordnat webb-API måste du ange en klient hemlighet eller ett klient certifikat i *appsettings.js* filen.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

I stället för en klient hemlighet kan du ange ett klient certifikat. Följande kodfragment visar hur du använder ett certifikat som lagras i Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft. Identity. Web tillhandahåller flera olika sätt att beskriva certifikat, både genom konfiguration eller kod. Mer information finns i [Microsoft. Identity. Web wiki – använda certifikat](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) på GitHub.

## <a name="startupcs"></a>Startup.cs

Med hjälp av Microsoft. identitet. Web, om du vill att ditt webb-API ska anropa underordnade webb-API: er, lägger du till `.EnableTokenAcquisitionToCallDownstreamApi()` raden efter `.AddMicrosoftIdentityWebApi(Configuration)` och väljer sedan en implementation av tokenbaserad cache, till exempel `.AddInMemoryTokenCaches()` i *startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
                .EnableTokenAcquisitionToCallDownstreamApi()
                .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Precis som med Web Apps kan du välja olika implementationer för cachelagring av token. Mer information finns i [Microsoft Identity Web wiki-cachelagring för token](https://aka.ms/ms-id-web/token-cache-serialization) i GitHub.

# <a name="java"></a>[Java](#tab/java)

OBO-flödet (on-behalf-of) används för att hämta en token för att anropa det underordnade webb-API: et. I det här flödet tar ditt webb-API emot en Bearer-token med användar delegerade behörigheter från klient programmet och utbyter sedan denna token för en annan åtkomsttoken för att anropa det underordnade webb-API: et.

I koden nedan används våren Security Framework `SecurityContextHolder` i webb-API: et för att hämta den validerade Bearer-token. Den använder sedan MSAL Java-biblioteket för att hämta en token för underordnad API med hjälp av `acquireToken` anropet med `OnBehalfOfParameters` . MSAL cachelagrar token så att efterföljande anrop till API: et kan användas `acquireTokenSilently` för att hämta den cachelagrade token.

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

OBO-flödet (on-behalf-of) används för att hämta en token för att anropa det underordnade webb-API: et. I det här flödet tar ditt webb-API emot en Bearer-token med användar delegerade behörigheter från klient programmet och utbyter sedan denna token för en annan åtkomsttoken för att anropa det underordnade webb-API: et.

Ett python-webb-API måste använda vissa mellanprogram för att verifiera Bearer-token som tas emot från klienten. Webb-API: et kan sedan hämta åtkomsttoken för underordnad API med hjälp av MSAL python Library genom att anropa [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metoden. Ett exempel på hur du använder det här API: et finns i [test koden för Microsoft-Authentication-Library-for-python på GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Se även diskussionen om [problem 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) i samma lagrings plats för en metod som kringgår behovet av ett program på mellan nivå.

---

Du kan också se ett exempel på OBO Flow-implementering i [Node.js och Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

Mer information om OBO-protokollet finns i [Microsoft Identity Platform och OAuth 2,0 on-behalf-of Flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md)