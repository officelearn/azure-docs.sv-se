---
title: "Ta emot händelser från Azure Event Hubs med .NET standardbiblioteket | Microsoft Docs"
description: "Börja ta emot meddelanden med EventProcessorHost i .NET Standard"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: a88b5da8fa504e0528caa7fa212d4cec26d1cf66
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Börja ta emot meddelanden med den värd för händelsebearbetning i .NET Standard

> [!NOTE]
> Det här exemplet är tillgängligt på [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

Den här kursen visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en händelsehubb med hjälp av den **värd för händelsebearbetning** bibliotek. Du kan köra den [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) lösning som-är ersätta strängarna med händelsen NAV- och kontovärden. Eller du kan följa stegen i den här kursen hjälper dig att skapa en egen.

## <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio 2015 eller 2017](http://www.visualstudio.com). Exemplen i den här självstudiekursen används Visual Studio 2017 men Visual Studio 2015 stöds också.
* [.NET core Visual Studio 2015 eller 2017 verktyg](http://www.microsoft.com/net/core).
* En Azure-prenumeration.
* Ett namnområde för Händelsehubbar i Azure.
* Ett Azure Storage-konto.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb  

Det första steget är att använda den [Azure-portalen](https://portal.azure.com) att skapa ett namnområde för typen Event Hubs och skaffa autentiseringsuppgifter för hantering som behövs i programmet för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och händelsen NAV, följer du proceduren i [i den här artikeln](event-hubs-create.md), och fortsätt sedan med den här kursen.  

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto  

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. I det vänstra navigeringsfönstret i portalen klickar du på **ny**, klickar du på **lagring**, och klicka sedan på **Lagringskonto**.  
3. Fyll i fälten i fönstret storage-konto och klicka sedan på **skapa**.

    ![Skapa lagringskonto][1]

4. Efter att du ser den **distributioner lyckades** klickar du på namnet på det nya lagringskontot. I den **Essentials** -fönstret klickar du på **Blobbar**. När den **Blob-tjänst** öppnas klickar du på **+ behållare** längst upp. Namnge behållaren och stäng sedan **Blob-tjänst**.  
5. Klicka på **åtkomstnycklar** i det vänstra fönstret och kopiera namnet på lagringsbehållaren, storage-konto och värdet för **key1**. Spara dessa värden i anteckningar eller en tillfällig plats.  

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Lägg till Event Hubs NuGet-paketet

Lägg till den [ **Microsoft.Azure.EventHubs** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) och [ **Microsoft.Azure.EventHubs.Processor** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET standardbibliotek NuGet-paket till projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på den **Bläddra** fliken, söka efter **Microsoft.Azure.EventHubs**, och välj sedan den **Microsoft.Azure.EventHubs** paketet. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
3. Upprepa steg 1 och 2 och installera den **Microsoft.Azure.EventHubs.Processor** paketet.

## <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **klassen**. Kalla den nya klassen **SimpleEventProcessor**.

2. Öppna filen SimpleEventProcessor.cs och Lägg till följande `using` instruktioner överst i filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementera den `IEventProcessor` gränssnitt. Ersätt hela innehållet i den `SimpleEventProcessor` klassen med följande kod:

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Skriva en huvudkonsolen-metod som använder SimpleEventProcessor-klassen för att ta emot meddelanden

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter till den `Program` klass för anslutningssträngen för event hub, händelsehubbens namn, behållare för lagringskontonamnet, lagringskontonamnet och lagringskontonyckel. Lägg till följande kod, Ersätt platshållarna med motsvarande värden.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Lägg till en ny metod med namnet `MainAsync` till den `Program` class, enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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

3. Lägg till följande kod för att den `Main` metoden:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Så här bör din Program.cs-fil se ut:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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

Grattis! Du har nu fått meddelanden från en händelsehubb med hjälp av den värd för händelsebearbetning.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
