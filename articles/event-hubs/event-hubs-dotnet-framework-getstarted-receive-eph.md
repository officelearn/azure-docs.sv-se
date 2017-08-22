---
title: "Ta emot händelser från Azure Event Hubs med .NET Framework| Microsoft Docs"
description: "Följ den här självstudien för att ta emot händelser från Azure Event Hubs med .NET Framework."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/12/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: c97cd41e503b5f2792f55e27038a2e07e254826e
ms.contentlocale: sv-se
ms.lasthandoff: 06/14/2017

---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Ta emot händelser från Azure Event Hubs med .NET Framework

## <a name="introduction"></a>Introduktion

Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här självstudien får du lära dig att skriva ett .NET Framework-konsolprogram som tar emot meddelanden från en Event Hub med **[värden för händelsebearbetning][EventProcessorHost]**. För att skicka händelser med .NET Framework kan du läsa artikeln [Skicka händelser till Azure Event Hubs med .NET Framework](event-hubs-dotnet-framework-getstarted-send.md) eller klicka på ditt avsändarspråk till vänster i innehållsförteckningen.

[EventProcessorHost][EventProcessorHost] är en .NET-klass som förenklar mottagandet av händelser från händelsehubbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från händelsehubbar. Med hjälp av [EventProcessorHost][Event Processor Host] kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder [EventProcessorHost][EventProcessorHost] för en enda mottagare. Exemplet på att [skala ut händelsebearbetning][Scale out Event Processing with Event Hubs] visar hur man använder [EventProcessorHost][EventProcessorHost] med flera mottagare.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

* [Microsoft Visual Studio 2015 eller senare](http://visualstudio.com). För skärmdumparna i de här självstudierna används Visual Studio 2017.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb

Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan enligt följande steg i den här självstudien.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

För att kunna använda [EventProcessorHost][EventProcessorHost] behöver du ett [Azure Storage-konto][Azure Storage account]:

1. Logga in på [Azure Portal][Azure portal] och klicka på **Ny** högst upp till vänster på skärmen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. På bladet **Skapa lagringskonto** anger du ett namn för lagringskontot. Välj en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen. Klicka sedan på **Skapa**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Klicka på det nyligen skapade lagringskontot i listan över lagringskonton.
5. På bladet för lagringskontot klickar du på **Åtkomstnycklar**. Kopiera värdet för **nyckel1** och använd det senare i de här självstudierna.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>Skapa ett mottagarkonsolprogram

1. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Mottagare**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. Högerklicka på projektet **Mottagare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**.
3. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klicka på **Installera** och godkänn användningsvillkoren.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus Event Hub –EventProcessorHost NuGet-paket ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alla sina beroenden.
4. Högerklicka på **Mottagare**-projektet, klicka på **Lägg till** och klicka sedan på **Klass**. Kalla den nya klassen för **SimpleEventProcessor** och klicka sedan på **Lägg till** för att skapa klassen.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. Lägg till följande uttryck överst i filen SimpleEventProcessor.cs:
    
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
    
  Den här klassen kommer att anropas av **EventProcessorHost** för att bearbeta händelser som tagits emot från händelsehubben. Observera att `SimpleEventProcessor`-klassen använder sig av ett stoppur för att regelbundet anropa kontrollpunktsmetoden i **EventProcessorHost**-kontexten. Detta garanterar att högst fem minuters bearbetningsarbete försvinner om mottagaren startas om.
6. I **Program**-klassen lägger du till följande `using`-uttryck överst i filen:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  Ersätt sedan metoden `Main` i klassen `Program` med följande kod, och ersätt namnet på händelsehubben och anslutningssträngen på namnområdesnivå som du sparade tidigare, samt lagringskontot och nyckeln som du kopierade i föregående avsnitt. 
    
  ```csharp
  static void Main(string[] args)
  {
    string eventHubConnectionString = "{Event Hubs namespace connection string}";
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

7. Kör programmet och kontrollera att det inte finns några fel.
  
Grattis! Du har nu fått meddelanden från en händelsehubb med värden för händelsebearbetning.


> [!NOTE]
> Den här guiden använder en enda instans av [EventProcessorHost][EventProcessorHost]. För att öka dataflödet rekommenderas att du kör flera instanser av [EventProcessorHost][EventProcessorHost], enligt exemplet [Utskalad händelsebearbetning][Utskalad händelsebearbetning]. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för [EventProcessorHost][EventProcessorHost]-instanser baserat på de datorer (eller roller) som de har distribuerats i. Mer information om de här ämnena finns i [Översikt över Event Hubs][Event Hubs overview] och [Programmeringsguide för Event Hubs][Event Hubs Programming Guide].
> 
> 

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar och tar emot data kan du lära dig mer genom att besöka följande länkar:

* [Värd för händelsebearbetning][Event Processor Host]
* [Översikt av händelsehubbar][Event Hubs overview]
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

