---
title: Skicka eller ta emot händelser från Azure Event Hubs med .NET (senaste)
description: Den här artikeln innehåller en genomgång för att skapa ett .NET Core-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure.Messaging.EventHubs-paketet.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: spelluru
ms.openlocfilehash: bc8ec6cf4845eb100af5dcd80101f17102d2b7ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682143"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Skicka händelser till och ta emot händelser från Azure Event Hubs - .NET Core (Azure.Messaging.EventHubs) 
Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsehubb med **azure.messaging.eventhubs** .NET Core-biblioteket. 

> [!IMPORTANT]
> Den här snabbstarten använder det nya **Azure.Messaging.EventHubs-biblioteket.** En snabbstart som använder det gamla **Microsoft.Azure.EventHubs-biblioteket** finns i [Skicka och ta emot händelser med Microsoft.Azure.EventHubs-biblioteket](event-hubs-dotnet-standard-getstarted-send.md). 



## <a name="prerequisites"></a>Krav
Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Azure Event Hubs-klientbiblioteket använder sig av nya funktioner som introducerades i C# 8.0.  Du kan fortfarande använda biblioteket med äldre versioner av C#, men vissa av dess funktioner är inte tillgängliga.  Om du vill aktivera dessa funktioner måste du [inrikta dig på .NET Core 3.0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) eller [ange vilken språkversion](/dotnet/csharp/language-reference/configure-language-version#override-a-default) du vill använda (8.0 eller senare). Om du använder Visual Studio är versioner före Visual Studio 2019 inte kompatibla med de verktyg som behövs för att skapa C# 8.0-projekt. Visual Studio 2019, inklusive den kostnadsfria Community-utgåvan, kan laddas ner [här](https://visualstudio.microsoft.com/vs/)
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutningssträngen för namnområdet Event Hubs** genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutningssträngen senare i den här snabbstarten.

## <a name="send-events"></a>Skicka händelser 
I det här avsnittet visas hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubb. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio 2019. 
1. Välj **Skapa ett nytt projekt**. 
1. Gör följande i dialogrutan **Skapa ett nytt projekt:** Om du inte ser den här dialogrutan väljer du **Arkiv** på menyn, väljer **Nytt**och väljer sedan **Projekt**. 
    1. Välj **C#** för programmeringsspråket.
    1. Välj **Konsol** för typen av program. 
    1. Välj **Console App (.NET Core)** i resultatlistan. 
    1. Välj sedan **Nästa**. 

        ![Dialogrutan Nytt projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Ange **EventHubsSender** för projektnamnet, **EventHubsQuickStart för lösningsnamnet** och välj sedan **OK** för att skapa projektet. 

    ![C# > konsolapp](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **Verktyg** > **NuGet Package Manager** > **Package Manager Console** på menyn. 
1. Kör följande kommando för att installera **Azure.Messaging.EventHubs** NuGet-paketet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Lägg till konstanter i `Program` klassen för anslutningssträngen Event Hubs och händelsenavnamnet. Ersätt platshållare inom parentes med rätt värden som du fick när du skapade händelsehubben. Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Ersätt `Main` metoden med `async Main` följande metod. Mer information finns i kodkommentarerna. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Skapa projektet och se till att det inte finns några fel.
6. Kör programmet och vänta på bekräftelsemeddelandet. 
7. I Azure-portalen kan du verifiera att händelsehubben har tagit emot meddelandena. Växla till **meddelandevyn** i avsnittet **Mått.** Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har tagits emot. 

    [![Kontrollera att händelsehubben har tagit emot meddelandena](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > För den fullständiga källkoden med mer informationskommentarer, se [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Ta emot händelser
I det här avsnittet visas hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en händelsehubb med hjälp av en händelsebehandlare. Händelsebehandlaren förenklar mottagning av händelser från händelsehubbar genom att hantera beständiga kontrollpunkter och parallella mottagningar från dessa händelsehubbar. En händelsebehandlare är associerad med en specifik händelsehubb och en konsumentgrupp. Den tar emot händelser från flera partitioner i händelsehubben och skickar dem till ett hanteraredelegat för bearbetning med hjälp av kod som du anger. 


> [!NOTE]
> Om du körs på Azure Stack Hub kan den plattformen ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure. Om du till exempel kör [på Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod för att rikta lagringstjänstens API-version 2017-11-09. Ett exempel på hur du inriktar dig på en specifik storage API-version finns i [det här exemplet på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). Mer information om Azure Storage-tjänstversionerna som stöds på Azure Stack Hub finns i [Azure Stack Hub storage: Skillnader och överväganden](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en blob-behållare
I den här snabbstarten använder du Azure Storage som kontrollpunktslagring. Följ dessa steg för att skapa ett Azure Storage-konto. 

1. [Skapa ett Azure Storage-konto](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anteckna anslutningssträngen och behållarnamnet. Du använder dem i mottagningskoden. 


### <a name="create-a-project-for-the-receiver"></a>Skapa ett projekt för mottagaren

1. Högerklicka på **SolutionHubQuickStart-lösningen** i fönstret Solution Explorer, peka **på Lägg**till och välj **Nytt projekt**. 
1. Välj **Console App (.NET Core)** och välj **Nästa**. 
1. Ange **EventHubsReceiver** för **projektnamnet**och välj **Skapa**. 

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **Verktyg** > **NuGet Package Manager** > **Package Manager Console** på menyn. 
1. Kör följande kommando för att installera **Azure.Messaging.EventHubs** NuGet-paketet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Kör följande kommando för att installera **paketet Azure.Messaging.EventHubs.Processor** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Uppdatera huvudmetoden 

1. Lägg till `using` följande satser högst upp i **Program.cs-filen.**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Lägg till konstanter i `Program` klassen för anslutningssträngen Event Hubs och händelsenavnamnet. Ersätt platshållare inom parentes med rätt värden som du fick när du skapade händelsehubben. Ersätt platshållare inom parentes med rätt värden som du fick när du skapade händelsehubben och lagringskontot (åtkomstnycklar - primär anslutningssträng). Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Ersätt `Main` metoden med `async Main` följande metod. Mer information finns i kodkommentarerna. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Lägg nu till följande händelse- och felhanterarmetoder i klassen. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Skapa projektet och se till att det inte finns några fel.

    > [!NOTE]
    > Den här filen finns i den [här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Kör mottagarprogrammet. 
1. Du bör se ett meddelande om att händelsen har tagits emot. 

    ![Mottagen händelse](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Dessa händelser är de tre händelser som du skickade till händelsehubben tidigare genom att köra avsändningsprogrammet. 


## <a name="next-steps"></a>Nästa steg
Kolla in exemplen på GitHub. 

- [Exempel på eventhubbar på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Exempel på händelsebehandlare på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Exempel på rollbaserad åtkomstkontroll (RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
