---
title: Skyddad Web API – kod konfiguration för app
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8cc02831fa00a3974da1b74b07daf581f50dd22
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569616"
---
# <a name="protected-web-api-code-configuration"></a>Skyddat webb-API: kod konfiguration

Om du vill konfigurera koden för ditt skyddade webb-API måste du förstå vad som definierar API: er som skyddade, hur du konfigurerar en Bearer-token och hur du verifierar token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Vad definierar ASP.NET/ASP.NET Core API: er som skyddade?

Precis som Web Apps är webb-API: erna för ASP.NET/ASP.NET Core "skyddade" eftersom deras styrenhets åtgärder föregås av `[Authorize]`-attributet. Det innebär att kontroll åtgärder endast kan anropas om API: et anropas med en identitet som är auktoriserad.

Tänk på följande frågor:

- Hur känner webb-API: n till identiteten för den app som anropar den? (Endast en app kan anropa ett webb-API.)
- Om appen kallas webb-API för en användares räkning, vad är användarens identitet?

## <a name="bearer-token"></a>Bearer-token

Informationen om appens identitet och om användaren (om inte webbappen accepterar tjänst-till-tjänst-anrop från en daemon-app) lagras i den Bearer-token som anges i rubriken när appen anropas.

Här är ett C# kod exempel som visar en klient som anropar API: et när den har skaffat en token med Microsoft Authentication Library för .net (MSAL.net):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Bearer-token begärdes av ett klient program till Microsoft Identity Platform-slutpunkten *för webb-API: et*. Webb-API är det enda program som ska verifiera token och Visa de anspråk som den innehåller. Klient program bör aldrig försöka inspektera anspråk i tokens. (Webb-API: et kan kräva, i framtiden, att token krypteras. Detta krav förhindrar åtkomst för klient program som kan visa åtkomsttoken.)

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

När en app anropas på en styrenhets åtgärd som innehåller ett `[Authorize]`-attribut, tittar ASP.NET/ASP.NET Core på Bearer-token i Authorization-huvudet för den anropande begäran och extraherar åtkomsttoken. Token vidarebefordras sedan till JwtBearer mellanprogram, som anropar Microsoft IdentityModel-tillägg för .NET.

I ASP.NET Core initieras det här mellanprogram i Startup.cs-filen:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanprogram läggs till i webb-API: et med den här instruktionen:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 För närvarande skapar ASP.NET Core mallar Azure Active Directory (Azure AD) webb-API: er som loggar in användare inom organisationen eller organisationen, inte med personliga konton. Men du kan enkelt ändra dem till att använda Microsoft Identity Platform-slutpunkten genom att lägga till den här koden i Startup.cs-filen:

```CSharp
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

Kodfragmentet hämtas från den stegvisa självstudien för ASP.NET Core Web API i [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoden `AddProtectedWebApi`, som är mycket mer, anropas från Startup.cs

## <a name="token-validation"></a>Verifiering av token

JwtBearer mellan program vara, t. ex. OpenID Connect mellan program i webbappar, dirigeras av `TokenValidationParameters` för att validera token. Token dekrypteras (om det behövs), anspråken extraheras och signaturen verifieras. Mellanprogram validerar sedan token genom att söka efter dessa data:

- Den är avsedd för webb-API: t (Audience).
- Den utfärdades för en app som har tillåtelse att anropa webb-API: et (sub).
- Den har utfärdats av en betrodd säkerhetstokentjänst (STS) (utfärdare).
- Livs längden är inom räckhåll (Expires).
- Den har inte manipulerats med (signatur).

Det kan också finnas särskilda valideringar. Det är till exempel möjligt att verifiera att signerings nycklar (när de bäddas in i en token) är betrodda och att token inte spelas upp igen. Slutligen kräver vissa protokoll vissa valideringar.

### <a name="validators"></a>Verifierare

Validerings stegen samlas in i verifierare, som finns i [Microsoft IdentityModel-tillägg för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open-source-bibliotek, i en källfil: [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Verifierarna beskrivs i den här tabellen:

| Systemhälsoverifierare | Beskrivning |
|---------|---------|
| `ValidateAudience` | Säkerställer att token är för programmet som validerar token (för mig). |
| `ValidateIssuer` | Säkerställer att token utfärdats av ett betrott STS (från någon jag litar på). |
| `ValidateIssuerSigningKey` | Säkerställer att programmet validerar token-förtroendet för den nyckel som användes för att signera token. (Specialfall där nyckeln är inbäddad i token. Normalt krävs det inte.) |
| `ValidateLifetime` | Säkerställer att token fortfarande är (eller redan) giltig. Verifieraren kontrollerar om token för token (`notbefore` och `expires` anspråk) är inom räckhåll. |
| `ValidateSignature` | Säkerställer att token inte har manipulerats. |
| `ValidateTokenReplay` | Säkerställer att token inte spelas upp. (Specialfall för vissa Databasmigrering använder protokoll.) |

Verifierarna är associerade med egenskaperna för `TokenValidationParameters`-klassen, som i sin tur initieras från ASP.NET/ASP.NET Core-konfigurationen. I de flesta fall behöver du inte ändra parametrarna. Det finns ett undantag för appar som inte är enskilda klienter. (Det vill säga webbappar som godkänner användare från vilken organisation som helst eller från personliga Microsoft-konton.) I det här fallet måste utfärdaren verifieras.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Verifiera omfattningar och app-roller i din kod](scenario-protected-web-api-verification-scope-app-roles.md)
