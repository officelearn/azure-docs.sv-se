---
title: "Avsökning långvariga åtgärder | Microsoft Docs"
description: "Det här avsnittet visar hur du avsöker långvariga åtgärder."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 7123a2d44d3b7c332afe30fb0fcea88ca29e313a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Leverera direktsänd strömning med Azure Media Services

## <a name="overview"></a>Översikt

Microsoft Azure Media Services erbjuder API: er som skickar begäranden till Media Services för att starta operations (till exempel: skapa, starta, stoppa eller ta bort en kanal). Dessa åtgärder är långvariga.

Media Services .NET SDK innehåller API: er som skickar begäran och vänta tills åtgärden har slutförts (internt, för API: er avsöker för åtgärden pågår vid vissa intervall). Till exempel när du anropar kanal. Start(), returnerar-metoden när kanalen har startats. Du kan också använda den asynkrona versionen: väntan på kanalen. StartAsync() (information om uppgiftsbaserade asynkront mönster finns [trycker du på](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). API: er som skickar en begäran om åtgärden och söka efter status tills åtgärden har slutförts kallas ”avsökning metoder”. Dessa metoder (särskilt den asynkron versionen) rekommenderas för rich-klientprogram och/eller tillståndskänsliga tjänster.

Det finns scenarier där ett program inte kan vänta på en lång http-begäran och vill söka efter åtgärden förloppet manuellt. Ett typiskt exempel är en webbläsare som interagerar med en tillståndslös webbtjänst: när webbläsaren skickar en begäran för att skapa en kanal, webbtjänsten initierar en långvarig åtgärd och returnerar åtgärds-ID till webbläsaren. Webbläsaren kan sedan ber du webbtjänsten för att hämta Åtgärdsstatus för baserat på ID. Media Services .NET SDK innehåller API: er som är användbara för det här scenariot. Dessa API: er kallas ”icke-avsökning metoder”.
”Icke-avsökning metoder” har följande namngivningsmönstret: skicka*OperationName*åtgärd (till exempel SendCreateOperation). Skicka*OperationName*åtgärden metoder returnerar den **IOperation** objekt; den returnerade objekt innehåller information som kan användas för att spåra igen. Skicka*OperationName*OperationAsync metoder returnerar **aktivitet<IOperation>**.

För närvarande följande klasser stöder inte avsökning metoder: **kanal**, **StreamingEndpoint**, och **programmet**.

Om du vill söka efter Åtgärdsstatus använder den **GetOperation** -metoden i den **OperationBaseCollection** klass. Använd följande intervall för att kontrollera Åtgärdsstatus: för **kanal** och **StreamingEndpoint** åtgärder, använda 30 sekunder; för **programmet** åtgärder, använder 10 sekunder.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Exempel

I följande exempel definierar en klass med namnet **ChannelOperations**. Definitionen av den här klassen kan vara en startpunkt för din web service klassdefinitionen. I följande exempel används ej asynkrona versioner av metoder för enkelhetens skull.

Exemplet visar även hur klienten kan använda den här klassen.

### <a name="channeloperations-class-definition"></a>ChannelOperations klassdefinitionen

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
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        public ChannelOperations()
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
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

### <a name="the-client-code"></a>Klientkoden
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



## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

