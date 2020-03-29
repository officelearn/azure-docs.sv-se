---
title: Avsökningsoperationer för långvariga val | Microsoft-dokument
description: Azure Media Services erbjuder API:er som skickar förfrågningar till Media Services för att starta åtgärder (till exempel skapa, starta, stoppa eller ta bort en kanal), dessa åtgärder körs länge. Det här avsnittet visar hur du avsöker tidskrävande åtgärder.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887190"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Leverera livestreaming med Azure Media Services

## <a name="overview"></a>Översikt

Microsoft Azure Media Services erbjuder API:er som skickar förfrågningar till Media Services för att starta åtgärder (till exempel skapa, starta, stoppa eller ta bort en kanal). Dessa operationer är långvariga.

Media Services .NET SDK tillhandahåller API:er som skickar begäran och väntar på att åtgärden ska slutföras (internt är API:erna avsökning för förlopp med vissa intervall). Till exempel när du ringer kanal. Start(), återgår metoden när kanalen har startats. Du kan också använda den asynkrona versionen: vänta på kanal. StartAsync() (för information om uppgiftsbaserat asynkront mönster finns i [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). API:er som skickar en åtgärdsbegäran och sedan avsöker statusen tills åtgärden är slutförd kallas "avsökningsmetoder". Dessa metoder (särskilt Async-versionen) rekommenderas för avancerade klientprogram och/eller tillståndskänsliga tjänster.

Det finns scenarier där ett program inte kan vänta på en tidskrävande http-begäran och vill avsöka för åtgärdens förlopp manuellt. Ett typiskt exempel är en webbläsare som interagerar med en tillståndslös webbtjänst: när webbläsaren begär att skapa en kanal initierar webbtjänsten en tidskrävande åtgärd och returnerar åtgärds-ID:t till webbläsaren. Webbläsaren kan sedan be webbtjänsten att få driftstatus baserat på ID. Media Services .NET SDK tillhandahåller API:er som är användbara för det här scenariot. Dessa API:er kallas "icke-avsökningsmetoder".
"Icke-avsökningsmetoder" har följande namngivningsmönster: Skicka*OperationName-åtgärd*(till exempel SendCreateOperation). Skicka*OperationName-funktionsmetoder* **returnerar IOperation-objektet.** Det returnerade objektet innehåller information som kan användas för att spåra åtgärden. Metoderna Send*OperationName*OperationAsync returnerar **Task\<IOperation>**.

För närvarande stöder följande klasser metoder för icke-avsökning: **Kanal**, **StreamingEndpoint**och **Program**.

Om du vill söka efter åtgärdsstatus använder du metoden **GetOperation** i klassen **OperationBaseCollection.** Använd följande intervall för att kontrollera driftstatus: Använd 30 sekunder för **Channel-** och **StreamingEndpoint-åtgärder.** **för** programoperationer, använd 10 sekunder.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Exempel

I följande exempel definieras en klass som kallas **ChannelOperations**. Den här klassdefinitionen kan vara en utgångspunkt för definitionen av webbtjänstklass. För enkelhetens skull använder följande exempel icke-asynkronaversioner av metoder.

Exemplet visar också hur klienten kan använda den här klassen.

### <a name="channeloperations-class-definition"></a>Klassdefinition för ChannelOperations

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

