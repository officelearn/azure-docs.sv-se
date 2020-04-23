---
title: Konfigurera skrivbordsappar som anropar webb-API:er – Microsoft identity platform | Azure
description: Lär dig hur du konfigurerar koden för en skrivbordsapp som anropar webb-API:er
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e0b43f7563c9dfac6374590f6b081197536fe31e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869013"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Skrivbordsapp som anropar webb-API:er: Kodkonfiguration

Nu när du har skapat ditt program får du lära dig hur du konfigurerar koden med programmets koordinater.

## <a name="microsoft-authentication-libraries"></a>Microsoft-autentiseringsbibliotek

Följande MSALs -program (Microsoft Authentication Libraries) stöder skrivbordsprogram.

  Microsofts autentiseringsbibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Stöder att skapa ett skrivbordsprogram på flera plattformar, till exempel Linux, Windows och macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Stöder att bygga ett skrivbordsprogram på flera plattformar.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Stöder att bygga ett skrivbordsprogram på flera plattformar.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Stöder skrivbordsprogram som endast körs på macOS.

## <a name="public-client-application"></a>Program för offentlig klient

Ur kodsynpunkt är skrivbordsprogram offentliga klientprogram. Konfigurationen blir lite annorlunda beroende på om du använder interaktiv autentisering eller inte.

# <a name="net"></a>[.NET](#tab/dotnet)

Du måste bygga och manipulera `IPublicClientApplication`MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exklusivt kod

Följande kod instansierar ett offentligt klientprogram och loggar in användare i Det offentliga Microsoft Azure-molnet med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Om du tänker använda interaktiv autentisering eller enhetskodflöde, som tidigare sett, använder `.WithRedirectUri` du modifieraren.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Använda konfigurationsfiler

Följande kod instansierar ett offentligt klientprogram från ett konfigurationsobjekt, som kan fyllas i programmässigt eller läsas från en konfigurationsfil.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Mer detaljerad konfiguration

Du kan utveckla programbyggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att din ansökan ska vara ett program med flera trogna i ett nationellt moln, till exempel usa:s regering som visas här, kan du skriva:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET innehåller även en modifierare för Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen, om du vill hämta token för en Azure Active Directory (Azure AD) B2C-klient anger du din klient som visas i följande kodavsnitt:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Läs mer

Så här lär du dig mer om hur du konfigurerar ett MSAL.NET skrivbordsprogram:

- En lista över alla modifierare som finns på `PublicClientApplicationBuilder`finns i referensdokumentationen [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- En beskrivning av alla alternativ `PublicClientApplicationOptions`som visas i finns i [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) i referensdokumentationen.

### <a name="complete-example-with-configuration-options"></a>Komplett exempel med konfigurationsalternativ

Föreställ dig ett .NET Core-konsolprogram som har följande `appsettings.json` konfigurationsfil:

```json
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

Du har lite kod att läsa i den här filen med hjälp av . KONFIGURATIONSRAM FÖR NET:er som tillhandahålls:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Nu, för att skapa ditt program, skriv följande kod:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Innan anropet `.Build()` till metoden kan du åsidosätta konfigurationen med anrop till `.WithXXX` metoder, som tidigare sett.

# <a name="java"></a>[Java](#tab/java)

Här är den klass som används i MSAL Java utvecklingsexempel för att konfigurera exemplen: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[Macos](#tab/macOS)

Följande kod instansierar ett offentligt klientprogram och loggar in användare i Det offentliga Microsoft Azure-molnet med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

### <a name="quick-configuration"></a>Snabb konfiguration

Mål C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Mer detaljerad konfiguration

Du kan utveckla programbyggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att din ansökan ska vara ett program med flera trogna i ett nationellt moln, till exempel usa:s regering som visas här, kan du skriva:

Mål C:

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

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token för en skrivbordsapp](scenario-desktop-acquire-token.md)
