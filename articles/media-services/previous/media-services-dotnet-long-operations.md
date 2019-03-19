---
title: Avsökning långvariga åtgärder | Microsoft Docs
description: Det här avsnittet visar hur du avsöker långvariga åtgärder.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 518cc1d55a83d95daec8f22d0dcfc5db23cc2d38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173846"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Leverera direktsänd strömning med Azure Media Services

## <a name="overview"></a>Översikt

Microsoft Azure Media Services erbjuder API: er som skickar begäranden till Media Services för att starta åtgärder (till exempel: skapa, starta, stoppa eller ta bort en kanal). Dessa åtgärder är långvariga.

Media Services .NET SDK innehåller API: er som skickar begäran och vänta tills åtgärden har slutförts (internt API: erna avsökningen för åtgärdsförlopp med vissa intervall). Till exempel när du anropar kanal. Start(), returnerar-metoden när kanalen har startats. Du kan också använda den asynkrona versionen: await kanal. StartAsync() (information om uppgiftsbaserat asynkront mönster finns i [trycker du på](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). API: er som skickar en begäran om åtgärden och sedan söka efter status tills åtgärden har slutförts kallas ”avsökningen metoder”. Dessa metoder (särskilt den asynkron versionen) rekommenderas för rich-klientprogram och/eller tillståndskänsliga tjänster.

Det finns scenarier där ett program inte kan vänta på en tidskrävande http-begäran och vill söka efter förloppet för åtgärden manuellt. Ett typexempel är en webbläsare som interagerar med en tillståndslös webbtjänst: när webbläsaren skickar en begäran för att skapa en kanal, webbtjänsten initierar en långvarig åtgärd och returnerar åtgärds-ID till webbläsaren. Webbläsaren kan sedan be webbtjänsten för att hämta Åtgärdsstatus för baseras på ID. Media Services .NET SDK innehåller API: er som är användbara för det här scenariot. Dessa API: er kallas ”icke-avsökning metoder”.
”Icke-avsökning metoder” har följande namngivningsmönstret: Skicka*OperationName*åtgärden (till exempel SendCreateOperation). Skicka*OperationName*åtgärdsmetoder returnera den **IOperation** objekt; den returnerade objekt innehåller information som kan användas för att spåra igen. Skicka*OperationName*OperationAsync metoder returnerar **uppgift<IOperation>**.

Följande klasser stöder för närvarande icke-avsökning metoder:  **Kanalen**, **StreamingEndpoint**, och **programmet**.

Om du vill söka efter Åtgärdsstatus, använda den **GetOperation** metoden på den **OperationBaseCollection** klass. Använd följande intervall för att kontrollera Åtgärdsstatus: för **kanal** och **StreamingEndpoint** åtgärder, använder 30 sekunder; för **programmet** åtgärder, använda 10 sekunder.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Exempel

Följande exempel definierar en klass med namnet **ChannelOperations**. Den här klassdefinitionen kan vara en startpunkt för web service klassdefinitionen. I följande exempel används för enkelhetens skull icke asynkrona versioner av metoderna.

Exemplet visar också hur klienten kan använda den här klassen.

### <a name="channeloperations-class-definition"></a>ChannelOperations klassdefinitionen

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Klientkoden

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

