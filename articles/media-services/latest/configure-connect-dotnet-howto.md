---
title: Ansluta till Azure Media Services v3 API - .NET
description: Den här artikeln visar hur du ansluter till Media Services v3 API med .NET.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269814"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ansluta till Media Services v3 API - .NET

Den här artikeln visar hur du ansluter till Azure Media Services v3 .NET SDK med hjälp av tjänstens huvudinloggningsmetod.

## <a name="prerequisites"></a>Krav

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppsnamnet och medietjänstkontonamnet
- Installera ett verktyg som du vill använda för .NET-utveckling. Stegen i den här artikeln visar hur du använder [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Du kan använda Visual Studio-kod, se [Arbeta med C#](https://code.visualstudio.com/docs/languages/csharp). Du kan också använda en annan kodredigerare.

> [!IMPORTANT]
> Granska [namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio. 
1. Klicka på **Nytt** > **projekt**på **Arkiv-menyn** . 
1. Skapa ett **.NET** Core-konsolprogram.

Exempelappen i det `netcoreapp2.0`här avsnittet riktar sig till . Koden använder "async main", som är tillgänglig från och med C# 7.1. Se den här [bloggen](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) för mer information.

## <a name="add-required-nuget-packages"></a>Lägg till nödvändiga NuGet-paket

1. I Visual Studio väljer du **Tools** > **NuGet Package Manager** > **NuGet Manager Console**.
2. I fönstret **Package Manager** `Install-Package` Console använder du kommandot för att lägga till följande NuGet-paket. Till exempel `Install-Package Microsoft.Azure.Management.Media`.

|Paket|Beskrivning|
|---|---|
|`Microsoft.Azure.Management.Media`|Sdk för Azure Media Services. <br/>Kontrollera att du använder det senaste Azure Media Services-paketet [microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL-autentiseringsbibliotek för Azure SDK för NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Läsa konfigurationsvärden från miljövariabler och lokala JSON-filer|
|`Microsoft.Extensions.Configuration.Json`|Läsa konfigurationsvärden från miljövariabler och lokala JSON-filer
|`WindowsAzure.Storage`|SDK för lagring|

## <a name="create-and-configure-the-app-settings-file"></a>Skapa och konfigurera filen för appinställningar

### <a name="create-appsettingsjson"></a>Skapa appsettings.json

1. Gå gå **till allmän** > **textfil**.
1. Nämn den "appsettings.json".
1. Ange egenskapen "Kopiera till utdatakatalog" för .json-filen till "Kopiera om den är nyare" (så att programmet kan komma åt den när det publiceras).

### <a name="set-values-in-appsettingsjson"></a>Ange värden i appsettings.json

Kör `az ams account sp create` kommandot enligt beskrivningen i [access-API:er](access-api-cli-how-to.md). Kommandot returnerar json som du ska kopiera till din "appsettings.json".
 
## <a name="add-configuration-file"></a>Lägga till en konfigurationsfil

För enkelhetens skull lägger du till en konfigurationsfil som ansvarar för att läsa värden från "appsettings.json".

1. Lägg till en ny CS-klass i projektet. Ge den namnet `ConfigWrapper`. 
1. Klistra in följande kod i den här filen (det `ConsoleApp1`här exemplet förutsätter att du har namnområdet är ).

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

## <a name="connect-to-the-net-client"></a>Anslut till .NET-klienten

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I koden nedan skapar funktionen GetCredentialsAsync objektet ServiceClientCredentials baserat på autentiseringsuppgifterna som anges i den lokala konfigurationsfilen.

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

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
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

- [Självstudiekurs: Ladda upp, koda och strömma videor - .NET](stream-files-tutorial-with-api.md) 
- [Självstudiekurs: Streama live med Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Självstudiekurs: Analysera videor med Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Skapa jobbindata från en lokal fil – .NET](job-input-from-local-file-how-to.md)
- [Skapa jobbindata från en HTTPS-URL – .NET](job-input-from-http-how-to.md)
- [Koda med en anpassad transformering – .NET](customize-encoder-presets-how-to.md)
- [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans – .NET](protect-with-aes128.md)
- [Använda dynamisk DRM-kryptering och tjänsten för licensleverans – .NET](protect-with-drm.md)
- [Hämta en signeringsnyckel från den befintliga principen – .NET](get-content-key-policy-dotnet-howto.md)
- [Skapa filter med Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Avancerad exempel på video på begäran för Azure Functions v2 med Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Se även

* [.NET-referens](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Fler kodexempel finns i [repo-exemplen för .NET SDK.](https://github.com/Azure-Samples/media-services-v3-dotnet)
