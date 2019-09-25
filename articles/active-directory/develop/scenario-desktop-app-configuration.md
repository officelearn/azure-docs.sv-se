---
title: 'Skriv bords app som anropar webb-API: er (kod konfiguration) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (appens kod konfiguration)'
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
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268418"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Skriv bords app som anropar webb-API: er – kod konfiguration

Nu när du har skapat ditt program får du lära dig hur du konfigurerar koden med programmets koordinater.

## <a name="msal-libraries"></a>MSAL-bibliotek

Det enda MSAL-biblioteket som stöder Skriv bords program på flera plattformar idag är MSAL.NET.

MSAL för iOS och macOS stöder endast Skriv bords program som körs på macOS. 

## <a name="public-client-application-with-msalnet"></a>Offentligt klient program med MSAL.NET

Från en kodvyn i vyn är Skriv bords program offentliga klient program och det är därför som du ska bygga och manipulera MSAL.NET `IPublicClientApplication`. Återigen är det lite annorlunda om du använder interaktiv autentisering eller inte.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Enbart med kod

Följande kod instansierar ett offentligt klient program, inloggnings användare i det Microsoft Azure offentliga molnet, med ett arbets-och skol konto eller en personlig Microsoft-konto.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Om du avser att använda interaktiv autentisering eller enhets kod flöde, som visas ovan, vill du använda `.WithRedirectUri` modifieraren:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Använda konfigurationsfiler

Följande kod instansierar ett offentligt klient program från ett konfigurations objekt, som kan fyllas i program mässigt eller läses från en konfigurations fil

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Mer avancerad konfiguration

Du kan utveckla program byggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET innehåller också en modifierare för ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen, om du vill hämta token för en Azure AD B2C-klient, kan du ange din klient som visas i följande kodfragment:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Lär dig mer

Lär dig mer om hur du konfigurerar ett MSAL.NET Desktop-program:

- Listan över alla modifierare som är tillgängliga på `PublicClientApplicationBuilder`finns i referens dokumentationen [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Beskrivning av alla alternativ som visas i `PublicClientApplicationOptions` se [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)i referens dokumentationen

## <a name="complete-example-with-configuration-options"></a>Slutför exempel med konfigurations alternativ

Föreställ dig ett .net Core-konsolprogram som har `appsettings.json` följande konfigurations fil:

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

Du har lite kod för att läsa den här filen med .NET-angivet konfigurations ramverk.

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

För att skapa programmet behöver du nu bara skriva följande kod:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

och före anropet till `.Build()` -metoden kan du åsidosätta konfigurationen med anrop till `.WithXXX` metoder som visas ovan.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Offentligt klient program med MSAL för iOS och macOS

Följande kod instansierar ett offentligt klient program, inloggnings användare i det Microsoft Azure offentliga molnet, med ett arbets-och skol konto eller en personlig Microsoft-konto.

### <a name="quick-configuration"></a>Snabb konfiguration

Mål-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Införliva
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Mer avancerad konfiguration

Du kan utveckla program byggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

Mål-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Införliva

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en token för en desktop-app](scenario-desktop-acquire-token.md)
