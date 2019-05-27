---
title: 'Skrivbordsappen att anrop webb-API: er (kod-konfiguration) – Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp att anrop webb-API: er (konfiguration av appens kod)'
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
ms.openlocfilehash: bc0042d6392891e8282c563afea2212031a0f49a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121873"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Att anrop webb-API: er – kod configuration-skrivbordsapp

Nu när du har skapat ditt program, lär hur du konfigurerar koden med programmets koordinater.

## <a name="msal-libraries"></a>MSAL bibliotek

Det enda MSAL biblioteket som stöder program idag är MSAL.NET

## <a name="public-client-application"></a>Offentliga klientprogram

Från en kod synvinkel skrivbordsprogram är offentlig klientprogram och det är därför du skapa och ändra MSAL.NET `IPublicClientApplication`. Igen är saker lite olika om du använder interaktiv autentisering eller inte.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Uteslutande av kod

Följande kod skapar en instans av en offentlig klientprogrammet, logga in användare i det offentliga molnet i Microsoft Azure, med ett arbets- och skolkonto, eller ett personligt microsoftkonto.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Om du planerar att använda interaktiv autentisering som visas ovan, som du vill använda den `.WithRedirectUri` modifierare:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="using-configuration-files"></a>Använda konfigurationsfiler

Följande kod skapar en instans av en offentlig klientprogram från ett konfigurationsobjekt som kan vara ifyllda programmässigt eller läsa från en konfigurationsfil

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="more-elaborated-configuration"></a>Mer framtagna konfiguration

Du kan utveckla program som att skapa genom att lägga till ett antal modifierare. Till exempel om du vill att programmet ska vara ett program med flera innehavare i ett nationella moln (här US Government), kan du skriva:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET innehåller också en modifierare för AD FS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Om du vill hämta token för en Azure AD B2C-klient kan dessutom ange dina klient enligt följande kodavsnitt:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Lär dig mer

Mer information om hur du konfigurerar en MSAL.NET skrivbordsprogram:

- Lista över alla modifierare som är tillgängliga på `PublicClientApplicationBuilder`, finns i referensdokumentationen [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Beskrivning av alla alternativ som visas i `PublicClientApplicationOptions` Se [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), i referensdokumentationen

## <a name="complete-example-with-configuration-options"></a>Komplett exempel konfigurationsalternativ

Tänk dig ett .NET Core-konsolprogram som har följande `appsettings.json` konfigurationsfil:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Du har lite kod för att läsa den här filen med hjälp av .NET som tillhandahålls configuration framework;

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Nu för att skapa ditt program, behöver du bara skriva följande kod:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
           .Build();
```

och innan anropet till den `.Build()` metod, som du kan åsidosätta konfigurationen med anrop till `.WithXXX` metoder som visas tidigare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token för en skrivbordsapp](scenario-desktop-acquire-token.md)
