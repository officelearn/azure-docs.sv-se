---
title: Strömma live med Azure Media Services v3 med .NET Core | Microsoft Docs
description: Den här självstudien går igenom stegen för att strömma live med Media Services v3 med .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: 82ef04993b597778c808d649032603a0f3672e4c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110338"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Strömma live med Azure Media Services v3 med .NET Core

I Media Services ansvarar [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En LiveEvent tillhandahåller en slutpunkt (infognings-URL) som du sedan vidarebefordrar till en livekodare. LiveEvent tar emot live indataströmmar från livekodaren och gör den tillgänglig för strömning via en eller flera [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). Kanalen tillhandahåller också en slutpunkt för förhandsvisning (förhandsvisnings-URL) som du använder för att förhandsgranska och validera din ström inför vidare behandling och leverans. Den här självstudien visar hur du använder .NET Core för att skapa en **genomströmnings**typ av en livehändelse. 

> [!NOTE]
> Var noga att du kollar igenom [Liveuppspelning med Media Services v3](live-streaming-overview.md) innan du fortsätter. 

Självstudien visar hur du:    

> [!div class="checklist"]
> * Skapa ett Media Services-konto
> * Åtkomst till Media Services API
> * Konfigurera exempelappen
> * Granska den kod som utför du liveuppspelningen
> * Titta på händelsen med [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) på http://ampdemo.azureedge.net
> * Rensa resurser

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att kunna genomföra självstudien.

* Installera Visual Studio Code eller Visual Studio
* En kamera eller en enhet (som en bärbar dator) som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning. Dataströmmen måste anges i **RTMP**- eller **Smooth Streaming**-format.

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Livesändningsexemplet finns i [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp)-mappen.

> [!IMPORTANT]
> Det här exemplet använder unika suffix för varje resurs. Om du avbryter felsökningen eller avslutar appen utan att köra klart den, kommer du att få flera LiveEvents i ditt konto. <br/>
> Se till att stoppa de LiveEvents som körs. Annars kommer du att **debiteras**!

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Granska den kod som utför du liveuppspelningen

Det här avsnittet går igenom funktionerna som definierades i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) för projektet *MediaV3LiveApp*.

Exemplet skapar ett unikt suffix för varje resurs så att vi inte har namnkonflikter om du kör exemplet flera gånger utan att rensa upp.

> [!IMPORTANT]
> Det här exemplet använder unika suffix för varje resurs. Om du avbryter felsökningen eller avslutar appen utan att köra klart den, kommer du att få flera LiveEvents i ditt konto. <br/>
> Se till att stoppa de LiveEvents som körs. Annars kommer du att **debiteras**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Skapa en livehändelse

Det här avsnittet visar hur du skapar en **pass-through**-typ av LiveEvent (LiveEventEncodingType inställd på None). Om du vill skapa en LiveEvent som är aktiverad för livekodning, ställer du in värdet LiveEventEncodingType till Basic. 

Vissa andra saker som du kan ange när du skapar en livehändelsen är:

* Media Services-plats 
* Strömningsprotokollet för Livehändelsen (för närvarande stöds protokollen RTMP och Smooth Streaming)
       
    Du kan inte ändra protokollalternativ när LiveEvent eller dess associerade LiveOutputs körs. Om du behöver olika protokoll får du skapa separata LiveEvent för varje strömningsprotokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här LiveEvent. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).
    
    Om inga IP-adresser har angetts och det saknas regeldefinitioner, kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

När du skapar händelsen, kan du ange att den ska startas automatiskt. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När kanalen har skapats kan du få infognings-URL:er som du tillhandahåller till livekodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.


```csharp
// Get the input endpoint to configure the on-premises encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on-premises encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Hämta förhandsgransknings-URL:en

Använd previewEndpoint för att förhandsgranska och verifiera att indata från kodaren faktiskt tas emot.

> [!IMPORTANT]
> Kontrollera att videon flödar till förhandsgransknings-URL:en innan du fortsätter!

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Skapa och hantera LiveEvents och LiveOutputs

När dataströmmen väl flödar till LiveEventet kan du påbörja strömningshändelsen genom att skapa en tillgång, ett LiveOutput och en StreamingLocator. Detta arkiverar dataströmmen och gör den tillgänglig för visning via StreamingEndpoint. 

#### <a name="create-an-asset"></a>Skapa en tillgång

Skapa en tillgång för LiveOutput att använda.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>Skapa en LiveOutput

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>Skapa en StreamingLocator

> [!NOTE]
> När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av **standardtyp** till i kontot med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Rensa upp resurserna på ditt Media Services-konto

Följ stegen nedan om du är klar med strömningen av händelser och vill rensa de resurser som etablerades tidigare.

* Stoppa sändningen av dataströmmen från kodaren.
* Stoppa LiveEvent. När LiveEvent stoppats, medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* Du kan avbryta din StreamingEndpoint om du inte vill fortsätta att tillhandahålla arkivet för din livehändelse som en strömning på begäran. Om LiveEvent är i stoppat tillstånd så medför den inga avgifter.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Titta på händelsen

Om du vill titta på händelsen, kopierar du strömnings-URL:en som du fick när du körde koden som beskrivs i [Skapa en StreamingLocator](#create-a-streaminglocator) och använd en valfri spelare. Du kan använda [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) för att testa din dataström på http://ampdemo.azureedge.net. 

Live-händelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas. Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare. Du kan använda verktyget **CloudShell**.

Kör följande kommando i **CloudShell**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Om du lämnar LiveEvent igång så medför det kostnader. Var medveten om att projektet/programmet kraschar eller stängs av någon anledning så kan det lämna LiveEvent igång i ett debiterbart tillstånd.

## <a name="next-steps"></a>Nästa steg

[Strömma filer](stream-files-tutorial-with-api.md)

