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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536897"
---
# <a name="protected-web-api-code-configuration"></a>Skyddade webb-API: Konfiguration av kod

Om du vill konfigurera koden för dina skyddade webb-API, måste du förstå vad definierar API: er som skyddad, hur du konfigurerar en ägartoken och hur du verifierar token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Vad definierar ASP.NET/ASP.NET Core API: er som skyddad?

T.ex. webbappar, ASP.NET/ASP.NET Core web API: er är ”skyddad” eftersom deras controller åtgärder har prefixet i `[Authorize]` attribut. Så kan controller-åtgärder endast anropas om API: et anropas med en identitet som har behörighet.

Tänk på följande frågor:

- Hur kan identitet som anropar den i ditt webb-API? (Endast en app kan anropa ett webb-API).
- Om appen kallas webb-API för en användares räkning, vad är användarens identitet?

## <a name="bearer-token"></a>Ägartoken

Information om identiteten för appen och användaren (om inte webbappen accepterar tjänst-till-tjänst-anrop från en daemon-app), lagras i ägartoken som anges i huvudet när appen anropas.

Här är en C# kodexempel som visar en klient som anropar API: et när den får en token med Microsoft Authentication Library för .NET (MSAL.NET):

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
> Ägartoken begärdes av ett klientprogram till Microsoft identity-plattformen slutpunkten *för webb-API*. Web API är det enda program som ska verifiera token och visa de anspråk som den innehåller. Klientappar bör aldrig försöka att inspektera anspråken i token. (Webb-API kan det krävas, i framtiden kommer att token krypteras. Detta krav kan förhindra åtkomst för klientappar som kan visa åtkomst-token.)

## <a name="jwtbearer-configuration"></a>JwtBearer konfiguration

Det här avsnittet beskriver hur du konfigurerar en ägartoken.

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

### <a name="code-initialization"></a>Initieringen av kod

När en app anropas på en domänkontrollant-åtgärd som innehåller en `[Authorize]` attribut, ASP.NET/ASP.NET Core tittar på ägartoken i auktoriseringshuvudet för anropande begäran och extraherar den åtkomst-token. Token är sedan vidarebefordras till JwtBearer middleware, som anropar Microsoft IdentityModel-tillägg för .NET.

Den här mellanprogram initieras i ASP.NET Core i filen Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanprogrammet läggs till webb-API med den här instruktionen:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 För närvarande kan skapa ASP.NET Core-mallarna Azure Active Directory (Azure AD) web API: er som loggar in användare inom din organisation eller en organisation, inte med personliga konton. Men du kan enkelt ändra dem för att använda Microsoft identity-plattformen slutpunkten genom att lägga till den här koden i filen Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Verifieringen av åtkomsttoken

JwtBearer middleware, som OpenID Connect-mellanprogram i web apps har fått `TokenValidationParameters` att validera token. Token dekrypteras (vid behov), anspråken har extraherats och signaturen verifieras. Mellanprogrammet verifierar sedan token genom att söka efter dessa data:

- Det är avsett för webb-API (målgrupp).
- Det har utfärdats för en app som har tillåtelse för att anropa webb-API (under).
- Det har utfärdats av en betrodd säkerhetstokentjänst (STS) (utfärdare).
- Livslängden är inom räckhåll (förfallodatum).
- Den inte har manipulerats (signatur).

Det kan också vara särskilda verifieringar. Det är exempelvis möjligt att verifiera att Signeringsnycklar (när den är inbäddad i en token) är betrodda och att token inte som spelas. Slutligen kräver vissa protokoll specifika verifieringar.

### <a name="validators"></a>Systemhälsoverifierare

Verifiering stegen fångas i systemhälsoverifierare som finns i den [Microsoft IdentityModel-tillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source-biblioteket, i en källfil: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Systemhälsoverifierare beskrivs i den här tabellen:

| Verifieraren | Beskrivning |
|---------|---------|
| `ValidateAudience` | Säkerställer token är för det program som verifierar token (för mig). |
| `ValidateIssuer` | Säkerställer att token utfärdats av en betrodd STS (från någon jag litar på). |
| `ValidateIssuerSigningKey` | Säkerställer programmet verifierar token förtroenden den nyckel som användes för att signera token. (Specialfall där nyckeln bäddas in i token. Vanligtvis inte krävs.) |
| `ValidateLifetime` | Säkerställer token är giltig fortfarande (eller redan). Verifieraren kontrollerar om livslängd för token (`notbefore` och `expires` anspråk) är inom räckhåll. |
| `ValidateSignature` | Säkerställer token inte har ändrats. |
| `ValidateTokenReplay` | Säkerställer token inte återupprepas. (Specialfall för vissa genomfört Använd-protokoll.) |

Systemhälsoverifierare som är associerade med egenskaperna för den `TokenValidationParameters` klassen själva initierats från konfigurationen ASP.NET/ASP.NET Core. I de flesta fall behöver du inte ändra parametrarna. Det finns ett undantag för appar som inte är enkel klienter. (Det vill säga webbapp som har stöd för användare från en organisation eller personliga Microsoft-konton.) I det här fallet måste utfärdaren verifieras.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kontrollera scope och roller i din kod](scenario-protected-web-api-verification-scope-app-roles.md)
