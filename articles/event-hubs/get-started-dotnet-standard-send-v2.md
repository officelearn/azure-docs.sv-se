---
title: Skicka eller ta emot händelser från Azure Event Hubs med .NET (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett .NET Core-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure. Messaging. EventHubs-paketet.
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
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: c8c6e2741eeeadf2afc0c027da8f9cf957c29c95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023250"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs-.NET Core (Azure. Messaging. EventHubs) 
Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i Event Hubs kan du lagra data med ett lagrings kluster eller process händelser. Du kan till exempel transformera händelse data med hjälp av en real tids analys-Provider. Den här storskaliga händelse insamlings-och bearbetnings funktionen är en viktig komponent i moderna program arkitekturer, inklusive Sakernas Internet (IoT). En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien visar hur du skickar händelser till eller tar emot händelser från en Event Hub med hjälp av Event Hubs .NET Core SDK. 

> [!IMPORTANT]
> Den här snabb starten använder det nya **Azure. Messaging. EventHubs** -biblioteket. En snabb start som använder det gamla **Microsoft. Azure. EventHubs** -biblioteket finns i [den här artikeln](event-hubs-dotnet-standard-getstarted-send.md). 

## <a name="prerequisites"></a>Krav

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klient biblioteket för Azure Event Hubs använder nya funktioner som introducerades i C# 8,0.  Du kan fortfarande använda biblioteket med äldre versioner av C#, men vissa av dess funktioner är inte tillgängliga.  Om du vill aktivera dessa funktioner måste du vara [mål för .net Core 3,0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) eller [Ange den språk version](/dotnet/csharp/language-reference/configure-language-version#override-a-default) som du vill använda (8,0 eller senare). Om du använder Visual Studio är tidigare versioner av Visual Studio 2019 inte kompatibla med de verktyg som behövs för att C# bygga 8,0-projekt. Visual Studio 2019, inklusive den kostnads fria community-versionen, kan hämtas [här](https://visualstudio.microsoft.com/vs/)
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubben. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio 2019. 
1. Välj **skapa ett nytt projekt**. 
1. I dialog rutan **skapa ett nytt projekt** gör du följande: om du inte ser den här dialog rutan väljer du **fil** på menyn, väljer **ny**och väljer sedan **projekt**. 
    1. Välj **C#** för programmeringsspråket.
    1. Välj **konsol** för typen av program. 
    1. Välj **konsol program (.net Core)** i resultat listan. 
    1. Välj sedan **Nästa**. 

        ![Dialogrutan Nytt projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Ange **EventHubsSender** som projekt namn, **EventHubsQuickStart** för lösningens namn och välj sedan **OK** för att skapa projektet. 

    ![C#>-Konsol program](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen** på menyn. 
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande `using`-instruktioner överst i **program.cs** -filen:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Lägg till konstanter i `Program`-klassen för Event Hubs anslutnings strängen och händelse hubbens namn. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben. Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Ersätt `Main`-metoden med följande `async Main`-metod. Se kod kommentarer för mer information. 

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
5. Bygg projektet och se till att det inte finns några fel.
6. Kör programmet och vänta på bekräftelse meddelandet. 
7. I Azure Portal kan du kontrol lera att händelsehubben har tagit emot meddelandena. Växla till vyn **meddelanden** i avsnittet **mått** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits. 

    [![verifiera att händelsehubben tog emot meddelanden](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Ta emot händelser
Det här avsnittet visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en Event Hub med en händelse processor. Händelse processorn fören klar mottagandet av händelser från Event Hub genom att hantera permanenta kontroll punkter och parallella mottaganden från dessa händelse nav. En händelse processor är associerad med en speciell händelsehubben och en konsument grupp. Den tar emot händelser från flera partitioner i händelsehubben och skickar dem till en hanterare som delegeras för bearbetning med hjälp av kod som du anger. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en BLOB-behållare
I den här snabb starten använder du Azure Storage som kontroll punkts arkiv. Följ dessa steg om du vill skapa ett Azure Storage-konto. 

1. [Skapa ett Azure Storage konto](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anteckna anslutnings strängen och behållar namnet. Du kommer att använda dem i Receive-koden. 


### <a name="create-a-project-for-the-receiver"></a>Skapa ett projekt för mottagaren

1. I fönstret Solution Explorer högerklickar du på lösningen **EventHubQuickStart** , pekar på **Lägg till**och väljer **nytt projekt**. 
1. Välj **konsol program (.net Core)** och välj **Nästa**. 
1. Event **EventHubsReceiver** för **projekt namnet**och välj **skapa**. 

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen** på menyn. 
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs. processor** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Uppdatera main-metoden 

1. Lägg till följande `using`-instruktioner överst i **program.cs** -filen.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Lägg till konstanter i `Program`-klassen för Event Hubs anslutnings strängen och händelse hubbens namn. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben och lagrings kontot (åtkomst nycklar – primär anslutnings sträng). Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Ersätt `Main`-metoden med följande `async Main`-metod. Se kod kommentarer för mer information. 

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
1. Lägg nu till följande händelse-och fel hanterings metoder i klassen. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Bygg projektet och se till att det inte finns några fel.

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Kör mottagarens program. 
1. Du bör se ett meddelande om att händelsen har mottagits. 

    ![Händelse mottagen](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Dessa händelser är de tre händelser som du skickade till händelsehubben tidigare genom att köra avsändar programmet. 


## <a name="next-steps"></a>Nästa steg
Kolla in exemplen på GitHub. 

- [Event Hubs-exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Event processor-exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
