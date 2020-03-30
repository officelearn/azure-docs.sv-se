---
title: Konfigurera skyddade webb-API-appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API och konfigurerar programmets kod.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262521"
---
# <a name="protected-web-api-code-configuration"></a>Skyddat webb-API: Kodkonfiguration

Om du vill konfigurera koden för ditt skyddade webb-API måste du förstå:

- Vad definierar API:er som skyddade.
- Så här konfigurerar du en innehavartoken.
- Så här validerar du token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Vad definierar ASP.NET och ASP.NET Core API:er som skyddade?

Precis som webbappar skyddas webb-API:erna ASP.NET och ASP.NET Core eftersom deras styrenhetsåtgärder föregås av attributet **[Authorize].** Styrenhetsåtgärderna kan anropas endast om API:et anropas med en auktoriserad identitet.

Överväg följande frågor:

- Endast en app kan anropa ett webb-API. Hur känner API:et till identiteten på appen som anropar den?
- Om appen anropar API:et för en användare, vad är användarens identitet?

## <a name="bearer-token"></a>Bärare token

Innehavartoken som anges i huvudet när appen anropas innehåller information om appens identitet. Den innehåller också information om användaren om inte webbappen accepterar service-to-service-samtal från en daemonapp.

Här är ett C#-kodexempel som visar en klient som anropar API:et efter att ha hämtat en token med Microsoft Authentication Library for .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Ett klientprogram begär innehavarens token till slutpunkten för Microsoft identity platform *för webb-API:et*. Webb-API:et är det enda program som ska verifiera token och visa anspråken som den innehåller. Klientappar bör aldrig försöka granska anspråken i token.
>
> I framtiden kan webb-API:et kräva att token krypteras. Det här kravet skulle förhindra åtkomst för klientappar som kan visa åtkomsttoken.

## <a name="jwtbearer-configuration"></a>JwtBearer-konfiguration

I det här avsnittet beskrivs hur du konfigurerar en innehavartoken.

### <a name="config-file"></a>Config-fil

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Initiering av kod

När en app anropas på en controller-åtgärd som innehåller ett **[Authorize]-attribut** ASP.NET och ASP.NET Core extrahera åtkomsttoken från auktoriseringshuvudets innehavartoken. Åtkomsttoken vidarebefordras sedan till JwtBearer middleware, som anropar Microsoft IdentityModel Extensions för .NET.

I ASP.NET Core initieras denna middleware i Startup.cs filen.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanmaterialet läggs till i webb-API:et med den här instruktionen:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 För närvarande skapar ASP.NET Core-mallar Azure Active Directory (Azure AD) webb-API:er som loggar in användare inom din organisation eller någon organisation. De loggar inte in användare med personliga konton. Men du kan ändra mallarna för att använda slutpunkten för Microsoft identity platform genom att lägga till den här koden i Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Det föregående kodavsnittet extraheras från den inkrementella självstudien ASP.NET för kärnweet-webb-API i [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). **Metoden AddProtectedWebApi,** som gör mer än kodavsnittet visar, anropas från Startup.cs.

## <a name="token-validation"></a>Tokenvalidering

I föregående utdrag validerar JwtBearer middleware, som OpenID Connect middleware i webbappar, token `TokenValidationParameters`baserat på värdet av . Token dekrypteras efter behov, anspråken extraheras och signaturen verifieras. Mellanmaterialet validerar sedan token genom att söka efter dessa data:

- Målgrupp: Token är avsedd för webb-API: et.
- Sub: Det har utfärdats för en app som får anropa webb-API: et.
- Utfärdare: Den utfärdades av en betrodd säkerhetstokentjänst (STS).
- Utgångsdatum: Dess livslängd är inom räckhåll.
- Signatur: Det var inte manipuleras.

Det kan också finnas speciella valideringar. Det är till exempel möjligt att verifiera att signeringsnycklar, när de är inbäddade i en token, är betrodda och att token inte spelas upp igen. Slutligen kräver vissa protokoll specifika valideringar.

### <a name="validators"></a>Validators

Valideringsstegen fångas in i validerare, som tillhandahålls av [Microsoft IdentityModel-tilläggen för .NET-bibliotek](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) med öppen källkod. Validerarna definieras i bibliotekskällfilen [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

I den här tabellen beskrivs validerarna:

| Validator | Beskrivning |
|---------|---------|
| **Valideraaudience** | Säkerställer att token är för programmet som validerar token för dig. |
| **ValideraIssuer** | Säkerställer att token har utfärdats av en betrodd STS, vilket innebär att den kommer från någon du litar på. |
| **ValideraIssuerSigningKey** | Säkerställer att programmet som validerar token litar på nyckeln som användes för att signera token. Det finns ett specialfall där nyckeln är inbäddad i token. Men det här fallet brukar inte uppstå. |
| **ValideraLivstid** | Säkerställer att token fortfarande är giltig eller redan giltig. Valideraren kontrollerar om tokens livstid ligger i det intervall som anges av **icke före** och upphör **att gälla.** |
| **Validerasignatur** | Säkerställer att token inte har manipulerats. |
| **ValideraTokenReplay** | Säkerställer att token inte spelas upp igen. Det finns ett specialfall för vissa engångsprotokoll. |

Validerarna är associerade med egenskaperna för klassen **TokenValidationParameters.** Egenskaperna initieras från konfigurationen ASP.NET och ASP.NET Core.

I de flesta fall behöver du inte ändra parametrarna. Appar som inte är enskilda klienter är undantag. Dessa webbappar accepterar användare från alla organisationer eller från personliga Microsoft-konton. Emittenter i detta fall måste valideras.

## <a name="token-validation-in-azure-functions"></a>Tokenvalidering i Azure-funktioner

Du kan också validera inkommande åtkomsttoken i Azure Functions. Du kan hitta exempel på sådan validering i [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)och [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Verifiera scope och approller i koden](scenario-protected-web-api-verification-scope-app-roles.md)
