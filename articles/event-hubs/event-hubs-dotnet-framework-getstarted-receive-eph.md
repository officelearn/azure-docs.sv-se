---
title: "Ta emot händelser från Azure Event Hubs med .NET Framework| Microsoft Docs"
description: "Följ den här självstudien för att ta emot händelser från Azure Event Hubs med .NET Framework."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: 4a3656d09f216e5c895bac5d7d680b44d76f4df8


---
# <a name="get-started-with-event-hubs-using-the-net-framework"></a>Komma igång med Event Hubs med .NET Framework

## <a name="receive-messages-with-eventprocessorhost"></a>Ta emot meddelanden med EventProcessorHost
[EventProcessorHost][EventProcessorHost] är en .NET-klass som förenklar mottagandet av händelser från Event Hubs genom att hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs. Med hjälp av [EventProcessorHost][EventProcessorHost] kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder [EventProcessorHost][EventProcessorHost] för en enda mottagare. Exemplet på [Utskalad händelsebearbetning][Scale out Event Processing with Event Hubs] visar hur man använder [EventProcessorHost][EventProcessorHost] med flera mottagare.

För att kunna använda [EventProcessorHost][EventProcessorHost] behöver du ett [Azure Storage-konto][Azure Storage account]:

1. Logga in på [Azure Portal][Azure portal] och klicka på **Ny** högst upp till vänster på skärmen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. På bladet **Skapa lagringskonto** anger du ett namn för lagringskontot. Välj en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen. Klicka sedan på **Skapa**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Klicka på det nyligen skapade lagringskontot i listan över lagringskonton.
5. På bladet för lagringskontot klickar du på **Åtkomstnycklar**. Kopiera värdet för **nyckel1** och använd det senare i de här självstudierna.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)
6. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Mottagare**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
7. Högerklicka på lösningen i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**.
8. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kontrollera att projektnamnet (**Mottagare**) har angetts i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus Event Hub –EventProcessorHost NuGet-paket ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alla sina beroenden.
9. Högerklicka på **Mottagare**-projektet, klicka på **Lägg till** och klicka sedan på **Klass**. Kalla den nya klassen för **SimpleEventProcessor** och klicka sedan på **Lägg till** för att skapa klassen.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
10. Lägg till följande uttryck överst i filen SimpleEventProcessor.cs:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     Ersätt sedan följande kod för innehållet i klassen:
    
     ```csharp
     class SimpleEventProcessor : IEventProcessor
     {
         Stopwatch checkpointStopWatch;
    
         async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
         {
             Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
             if (reason == CloseReason.Shutdown)
             {
                 await context.CheckpointAsync();
             }
         }
    
         Task IEventProcessor.OpenAsync(PartitionContext context)
         {
             Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
             this.checkpointStopWatch = new Stopwatch();
             this.checkpointStopWatch.Start();
             return Task.FromResult<object>(null);
         }
    
         async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
         {
             foreach (EventData eventData in messages)
             {
                 string data = Encoding.UTF8.GetString(eventData.GetBytes());
    
                 Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                     context.Lease.PartitionId, data));
             }
    
             //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
             if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
             {
                 await context.CheckpointAsync();
                 this.checkpointStopWatch.Restart();
             }
         }
     }
     ```
    
     Den här klassen kommer att anropas av **EventProcessorHost** för att bearbeta händelser som tagits emot från Event Hub. Observera att `SimpleEventProcessor`-klassen använder sig av ett stoppur för att regelbundet anropa kontrollpunktsmetoden i **EventProcessorHost**-kontexten. Detta garanterar att högst fem minuters bearbetningsarbete försvinner om mottagaren startas om.
11. I **Program**-klassen lägger du till följande `using`-uttryck överst i filen:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     ```
    
     Ersätt sedan `Main`-metoden i `Program`-klassen med följande kod, vilket ersätter det Event Hub-namn och anslutningssträngen för namnområdesnivån som du sparade tidigare, samt lagringskontot och nyckeln som du kopierade i föregående avsnitt. 
    
     ```csharp
     static void Main(string[] args)
     {
       string eventHubConnectionString = "{Event Hub connection string}";
       string eventHubName = "{Event Hub name}";
       string storageAccountName = "{storage account name}";
       string storageAccountKey = "{storage account key}";
       string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
    
       string eventProcessorHostName = Guid.NewGuid().ToString();
       EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
       Console.WriteLine("Registering EventProcessor...");
       var options = new EventProcessorOptions();
       options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
       eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
    
       Console.WriteLine("Receiving. Press enter key to stop worker.");
       Console.ReadLine();
       eventProcessorHost.UnregisterEventProcessorAsync().Wait();
     }
     ```

> [!NOTE]
> Den här guiden använder en enda instans av [EventProcessorHost][EventProcessorHost]. För att öka dataflödet rekommenderas att du kör flera instanser av [EventProcessorHost][EventProcessorHost], enligt exemplet [Utskalad händelsebearbetning][Utskalad händelsebearbetning]. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för [EventProcessorHost][EventProcessorHost]-instanser baserat på de datorer (eller roller) som de har distribuerats i. Mer information om de här ämnena finns i [Översikt över Event Hubs][Event Hubs Overview] och [Programmeringsguide för Event Hubs][Event Hubs Programming Guide].
> 
> 

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar och tar emot data kan lära dig mer genom att besöka följande länkar:

* [Värd för händelsebearbetning](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Översikt av händelsehubbar][Event Hubs overview]
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3


<!--HONumber=Feb17_HO1-->


