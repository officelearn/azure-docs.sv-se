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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377219"
---
# <a name="protected-web-api-code-configuration"></a>Skyddat webb-API: kod konfiguration

Om du vill konfigurera koden för ditt skyddade webb-API måste du förstå:

- Vad definierar API: er som skyddade.
- Så här konfigurerar du en Bearer-token.
- Verifiera token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Vad definierar ASP.NET och ASP.NET Core API: er som skyddade?

Precis som Web Apps, är ASP.NET-och ASP.NET Core webb-API: erna skyddade eftersom deras styrenhets åtgärder föregås av attributet **[auktorisera]** . Styrenhets åtgärder kan endast anropas om API: et anropas med en auktoriserad identitet.

Tänk på följande frågor:

- Endast en app kan anropa ett webb-API. Hur känner API: et till identiteten för den app som anropar den?
- Om appen anropar API: et för en användares räkning, vad är användarens identitet?

## <a name="bearer-token"></a>Bearer-token

Bearer-token som anges i rubriken när appen kallas innehåller information om appens identitet. Den innehåller också information om användaren, om inte webbappen accepterar tjänst-till-tjänst-anrop från en daemon-app.

Här är ett C# kod exempel som visar en klient som anropar API: et när den har skaffat en token med Microsoft Authentication Library för .net (MSAL.net):

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
> Ett klient program begär Bearer-token till Microsoft Identity Platform-slutpunkten *för webb-API: et*. Webb-API är det enda program som ska verifiera token och Visa de anspråk som den innehåller. Klient program bör aldrig försöka inspektera anspråk i tokens.
>
> I framtiden kan webb-API: et kräva att token krypteras. Detta krav förhindrar åtkomst för klient program som kan visa åtkomsttoken.

## <a name="jwtbearer-configuration"></a>JwtBearer-konfiguration

I det här avsnittet beskrivs hur du konfigurerar en Bearer-token.

### <a name="config-file"></a>Konfigurations fil

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

### <a name="code-initialization"></a>Kod initiering

När en app anropas på en styrenhets åtgärd som innehåller ett **[auktorisera]** -attribut, ASP.NET och ASP.net Core extrahera åtkomsttoken från Authorization-huvudets Bearer-token. Åtkomsttoken vidarebefordras sedan till JwtBearer mellanprogram, som anropar Microsoft IdentityModel-tillägg för .NET.

I ASP.NET Core initieras det här mellanprogram i Startup.cs-filen.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanprogram läggs till i webb-API: et med den här instruktionen:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 För närvarande skapar ASP.NET Core mallar Azure Active Directory (Azure AD) webb-API: er som loggar in användare i din organisation eller i en organisation. De loggar inte in användare med personliga konton. Men du kan ändra mallarna till att använda Microsoft Identity Platform-slutpunkten genom att lägga till den här koden i Startup.cs:

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

Föregående kodfragment extraheras från den stegvisa självstudien för ASP.NET Core Web API i [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoden **AddProtectedWebApi** , som gör att fler än kodfragmentet visas, anropas från startup.cs.

## <a name="token-validation"></a>Verifiering av token

I föregående kodfragment kontrollerar JwtBearer mellan program varan, t. ex. OpenID Connect mellan program i webbappar, token baserat på värdet för `TokenValidationParameters`. Token dekrypteras vid behov, anspråken extraheras och signaturen verifieras. Mellanprogram validerar sedan token genom att söka efter dessa data:

- Mål grupp: token är avsedd för webb-API: et.
- Sub: det har utfärdats för en app som har tillåtelse att anropa webb-API: et.
- Utfärdare: den har utfärdats av en betrodd säkerhetstokentjänst (STS).
- Expires: livs längden är i intervallet.
- Signatur: den har inte manipulerats.

Det kan också finnas särskilda valideringar. Det är till exempel möjligt att verifiera att signerings nycklar, när de bäddas in i en token, är betrodda och att token inte spelas upp igen. Slutligen kräver vissa protokoll vissa valideringar.

### <a name="validators"></a>Verifierare

Validerings stegen samlas in i verifierare, som tillhandahålls av [Microsoft IdentityModel-tilläggen för .net med](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) öppen källkod. Verifierarna definieras i biblioteks käll filen [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

I den här tabellen beskrivs verifierarna:

| Systemhälsoverifierare | Beskrivning |
|---------|---------|
| **ValidateAudience** | Säkerställer att token är för programmet som validerar token åt dig. |
| **ValidateIssuer** | Säkerställer att token utfärdats av en betrodd STS, vilket innebär att det är från någon som du litar på. |
| **ValidateIssuerSigningKey** | Säkerställer att programmet validerar token-förtroendet för den nyckel som användes för att signera token. Det finns ett särskilt fall där nyckeln är inbäddad i token. Men det här fallet uppstår vanligt vis inte. |
| **ValidateLifetime** | Säkerställer att token fortfarande är giltig eller redan är giltig. Verifieraren kontrollerar om livs längden för token är inom det intervall som anges av **notBefore** och **upphör att gälla** . |
| **ValidateSignature** | Säkerställer att token inte har manipulerats. |
| **ValidateTokenReplay** | Säkerställer att token inte spelas upp. Det finns särskilda fall för vissa Databasmigrering-protokoll. |

Verifierarna är kopplade till egenskaperna för klassen **TokenValidationParameters** . Egenskaperna initieras från ASP.NET och ASP.NET Core konfiguration.

I de flesta fall behöver du inte ändra parametrarna. Appar som inte är enskilda klienter är undantag. Dessa webbappar godkänner användare från vilken organisation som helst eller från personliga Microsoft-konton. Utfärdare i det här fallet måste val IDE ras.

## <a name="token-validation-in-azure-functions"></a>Verifiering av token i Azure Functions

Du kan också validera inkommande åtkomsttoken i Azure Functions. Du kan hitta exempel på sådana verifieringar i [Microsoft .net](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)och [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Verifiera omfattningar och app-roller i din kod](scenario-protected-web-api-verification-scope-app-roles.md)
