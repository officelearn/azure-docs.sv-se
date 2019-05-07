---
title: Skyddade webb-API - konfiguration av kod | Azure
description: Lär dig hur du skapar ett skyddat webb-API och konfigurera din programkod.
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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074972"
---
# <a name="protected-web-api---code-configuration"></a>Skyddade webb-API - kod-konfiguration

För att konfigurera koden för dina skyddade webb-API, måste du förstå vad som gör API: er som skyddas, vad du behöver konfigurera ägartoken och hur du verifierar token.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Vad gör ASP.NET/ASP.NET Core API: er skyddas?

Som i web Apps, ASP.NET/ASP.NET core web API: er är ”skyddad” eftersom deras controller åtgärder har prefixet i `[Authorize]` attribut. Därför kan åtgärderna som domänkontrollant endast anropas om API: et anropas med en identitet som har behörighet.

Tänk på följande frågor:

- Hur vet identiteten för det program som anropar den (endast ett program kan anropa ett webb-API) i webb-API?
- Om programmet heter webb-API för en användares räkning, vad är användarens identitet?

## <a name="bearer-token"></a>Ägartoken

Information om identiteten för programmet och om användaren (om inte webbappen accepterar tjänst-till-tjänst-anrop från daemon-program), lagras i ägartoken som anges i huvudet vid anrop av programmet.

Här är en C# kodexempel som visar en klient som anropar API: et när du hämtar en token med MSAL.NET.

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
> Ägartoken begärdes av ett klientprogram till Microsoft identity-plattformen slutpunkten **för webb-API**. Web API är det enda program som ska verifiera token och titta på de anspråk som den innehåller. Klientprogrammen aldrig ska titta på anspråk i token (webb-API kan bestämma, i framtiden, den kräver att token krypteras och detta bryter klientprogrammet som kan knäcka öppna åtkomsttoken).

## <a name="jwtbearer-configuration"></a>JwtBearer konfiguration

Det här avsnittet beskriver vad du behöver konfigurera ägartoken.

### <a name="config-file"></a>Konfigurationsfil

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

### <a name="code-initialization"></a>Initieringen av kod

När programmet anropas på domänkontrollanten åtgärd anläggningen en `[Authorize]` attribut, ASP.NET/ASP.NET core tittar på ägartoken i auktoriseringshuvudet för anropande begäran och extraherar åtkomst-token som sedan vidarebefordras till JwtBearer mellanprogram som anropar Modelltilläggen för Microsoft-identitet för .NET.

Den här mellanprogram initieras i ASP.NET Core i den `Startup.cs` filen.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanprogrammet läggs till webb-API med följande anvisningar:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 För närvarande kan skapa mallar för ASP.NET Core Azure AD v1.0 webb-API: er. Du kan enkelt ändra dem för att använda Microsoft identity-plattformen slutpunkten genom att lägga till följande kod i den `Startup.cs` filen.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Verifieringen av åtkomsttoken

JwtBearer middleware, som OpenID Connect-mellanprogram i web apps, dirigeras genom den `TokenValidationParameters` att validera token. Token dekrypteras (vid behov), anspråken har extraherats och signaturen verifieras. Sedan kan verifieras token genom att söka efter följande data:

- Det är avsett för webb-API (målgrupp)
- Det har utfärdats för ett program som kan anropa webb-API (under)
- Det har utfärdats av en från Security Token Server (STS) (utfärdare)
- Livslängd för token är i intervallet (förfallodatum)
- Den inte har manipulerats (signatur)

Det kan också vara särskilda verifieringar. Det är exempelvis möjligt att verifiera att Signeringsnycklar (när den är inbäddad i en token) är betrodda och att token inte som spelas. Slutligen kräver vissa protokoll specifika verifieringar.

### <a name="validators"></a>Systemhälsoverifierare

Stegen valideringen ska samlas in i systemhälsoverifierare som finns i den [Microsoft Identity modell-tillägget för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source-biblioteket, i en källfil: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Systemhälsoverifierare beskrivs i följande tabell:

| Verifieraren | Beskrivning |
|---------|---------|
| `ValidateAudience` | Garanterar att token är för det program som verifierar token (för mig). |
| `ValidateIssuer` | Säkerställer att token har utfärdats av en betrodd STS (från någon jag litar på). |
| `ValidateIssuerSigningKey` | Säkerställer programmet verifierar token förtroenden den nyckel som användes för att signera token (särskilda fall där nyckeln bäddas in i den token som vanligtvis krävs inte). |
| `ValidateLifetime` | Säkerställer att token är giltig fortfarande (eller redan). Klar genom att kontrollera som livslängd för token (`notbefore`, `expires` anspråk) är inom räckhåll. |
| `ValidateSignature` | Säkerställer att token inte har ändrats. |
| `ValidateTokenReplay` | Säkerställer token inte återupprepas (specialfall för vissa genomfört Använd protokoll). |

Systemhälsoverifierare som är associerade med egenskaperna för den `TokenValidationParameters` klassen själva initierats från konfigurationen ASP.NET/ASP.NET Core. I de flesta fall behöver du inte ändra parametrarna. Det finns ett undantag för program som inte är enkel klienter (det vill säga webbappar som kan accepterar användare från en organisation eller personliga Microsoft-konton) – i det här fallet utfärdaren måste verifieras.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-protected-web-api-production.md)
