---
title: Ansluta till Azure Media Services v3-API – .NET
description: Lär dig hur du ansluter till Media Services v3-API med .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 8f8a1434af768180e34afcaacd6e92ab402ad8cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736147"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ansluta till Media Services v3-API – .NET

Den här artikeln visar hur du ansluter till Azure Media Services v3 .NET SDK med hjälp av metoden huvudsaklig inloggning på tjänsten.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto
- Installera ett verktyg som du vill använda för .NET-utveckling. Stegen i den här artikeln visar hur du använder [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Du kan använda Visual Studio Code, se [arbeta med C# ](https://code.visualstudio.com/docs/languages/csharp). Eller du kan använda en annan Kodredigerare.

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio. 
1. Från den **filen** -menyn klickar du på **New** > **projekt**. 
1. Skapa en **.NET Core** -konsolapp.

Exempelappen i det här avsnittet riktar sig mot `netcoreapp2.0`. Koden använder 'async huvudsidan ”, som är tillgängliga från och med C# 7.1. Se den här [blogg](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) för mer information.

## <a name="add-required-nuget-packages"></a>Lägg till NuGet-paket som krävs

1. I Visual Studio väljer **verktyg** > **NuGet-Pakethanteraren** > **NuGet Manager-konsolen**.
2. I den **Pakethanterarkonsolen** fönstret Använd `Install-Package` att lägga till följande NuGet-paket. Till exempel `Install-Package Microsoft.Azure.Management.Media`.

|Paket|Beskrivning|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Om du vill kontrollera att du använder det senaste Azure Media Services-paketet, kontrollera [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL-autentiseringsbibliotek för Azure SDK för NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Läs konfigurationsvärden från miljövariabler och lokala JSON-filer|
|`Microsoft.Extensions.Configuration.Json`|Läs konfigurationsvärden från miljövariabler och lokala JSON-filer
|`WindowsAzure.Storage`|Lagrings-SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Skapa och konfigurera appfilen

### <a name="create-appsettingsjson"></a>Skapa appsettings.json

1. Gå go **Allmänt** > **textfil**.
1. Ge den namnet ”appsettings.json”.
1. Ange egenskapen ”Copy to Output Directory” för JSON-fil till ”kopiera om nyare” (så att programmet kan komma åt den när publicerade).

### <a name="set-values-in-appsettingsjson"></a>Ange värden i appsettings.json

Kör den `az ams account sp create` kommandot enligt beskrivningen i [åtkomst API: er](access-api-cli-how-to.md). Kommandot returnerar json som du bör kopiera till ”appsettings.json”.
 
## <a name="add-configuration-file"></a>Lägga till en konfigurationsfil

Lägg till en konfigurationsfil som ansvarar för att läsa värdena från ”appsettings.json” för enkelhetens skull.

1. Lägg till en ny .cs klass i projektet. Ge den namnet `ConfigWrapper`. 
1. Klistra in följande kod i den här filen (det här exemplet förutsätts att du har namnområdet är `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Anslut till .NET-klient

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I koden nedan skapar funktionen GetCredentialsAsync objektet ServiceClientCredentials utifrån de autentiseringsuppgifter som anges i lokala konfigurationsfilen.

1. Öppna `Program.cs`.
1. Klistra in följande kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Ladda upp, koda och strömma videor – .NET](stream-files-tutorial-with-api.md) 
- [Självstudie: Strömma live med Media Services v3 – .NET](stream-live-tutorial-with-api.md)
- [Självstudie: Analysera videor med Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Skapa jobbindata från en lokal fil – .NET](job-input-from-local-file-how-to.md)
- [Skapa jobbindata från en HTTPS-URL – .NET](job-input-from-http-how-to.md)
- [Koda med en anpassad transformering – .NET](customize-encoder-presets-how-to.md)
- [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans – .NET](protect-with-aes128.md)
- [Använda dynamisk DRM-kryptering och tjänsten för licensleverans – .NET](protect-with-drm.md)
- [Hämta en signeringsnyckel från den befintliga principen – .NET](get-content-key-policy-dotnet-howto.md)
- [Skapa filter med Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Avancerad exempel på video på begäran för Azure Functions v2 med Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Se också

[.NET-referens](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
