---
title: Skicka och ta emot händelser med hjälp av .NET Core - Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett .NET Core-program som skickar händelser till Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: e572a9e19da144be61faefacc370cf512f5079ac
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725405"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs med .NET Core
Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här kursen visar hur du skapar .NET Core-program i C# skicka händelser till eller ta emot händelser från en händelsehubb. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Microsoft Visual Studio 2015 eller 2017](https://www.visualstudio.com). I exemplen i självstudien används Visual Studio 2017, men Visual Studio 2015 stöds också.
- [.NET Core Visual Studio 2015- eller 2017-verktyg](https://www.microsoft.com/net/core). 
- **Skapa ett Event Hubs-namnområde och en event hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Sedan, i **anslutningssträngen för händelsehubbens namnområde** genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubb. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

Lägg till [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Core-bibliotekets NuGet-paket till projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter ”Microsoft.Azure.EventHubs” och markera paketet **Microsoft.Azure.EventHubs**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter för `Program`-klassen för händelsehubbens anslutningssträng och entitetssökväg (enskilt händelsehubbnamn). Ersätt platshållare inom hakparentes med rätt värden som erhölls när du skapade händelsehubben. Kontrollera att `{Event Hubs connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Lägg till en ny metod som heter `MainAsync` till `Program`-klassen enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Lägg till en ny metod som heter `SendMessagesToEventHub` till `Program`-klassen enligt följande:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Lägg till följande kod till `Main`-metoden i `Program`-klassen:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Så här bör din Program.cs se ut.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Kör programmet och kontrollera att det inte finns några fel.

## <a name="receive-events"></a>Ta emot händelser
Det här avsnittet visas hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en event hub med den [Eventprocessorhost](event-hubs-event-processor-host.md). [Värden för händelsebearbetning](event-hubs-event-processor-host.md) är en .NET-klass som förenklar mottagandet av händelser från händelsehubbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från händelsehubbar. Med hjälp av värden för händelsebearbetning kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder värden för händelsebearbetning för en enda mottagare.
> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver). Ersätt strängarna `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` och `StorageContainerName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

Lägg till [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) och [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard-bibliotekets NuGet-paket till projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **Microsoft.Azure.EventHubs** och markera paketet **Microsoft.Azure.EventHubs**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
3. Upprepa steg 1 och 2 och installera paketet **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till** och sedan på **Klass**. Ge den nya klassen namnet **SimpleEventProcessor**.

2. Öppna filen SimpleEventProcessor.cs och lägg till följande `using`-uttryck överst i filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementera gränssnittet `IEventProcessor`. Ersätt hela innehållet i klassen `SimpleEventProcessor` med följande kod:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Uppdatera Main-metoden till att använda SimpleEventProcessor

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter till `Program`-klassen för händelsehubbens anslutningssträng, händelsehubbens namn, namn på lagringskontocontainern, lagringskontots namn och lagringskontonyckeln. Lägg till följande kod och ersätt platshållarna med motsvarande värden:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Lägg till en ny metod som heter `MainAsync` till `Program`-klassen enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Lägg till följande kodrad till `Main`-metoden:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Så här bör din Program.cs-fil se ut:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Kör programmet och kontrollera att det inte finns några fel.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)


