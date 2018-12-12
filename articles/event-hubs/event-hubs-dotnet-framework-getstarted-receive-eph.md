---
title: Ta emot händelser med .NET Framework - Azure Event Hubs | Microsoft Docs
description: Följ den här självstudien för att ta emot händelser från Azure Event Hubs med .NET Framework.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8052b797707f7913fdd678f4dd51822754623104
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077264"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Ta emot händelser från Azure Event Hubs med .NET Framework

## <a name="introduction"></a>Introduktion

Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här kursen visar hur du skriver ett .NET Framework-konsolprogram som tar emot meddelanden från en event hub med den [Eventprocessorhost](event-hubs-event-processor-host.md). [Värden för händelsebearbetning](event-hubs-event-processor-host.md) är en .NET-klass som förenklar mottagandet av händelser från händelsehubbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från händelsehubbar. Med hjälp av värden för händelsebearbetning kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder värden för händelsebearbetning för en enda mottagare. Den [Utskalad händelsebearbetning] [ Scale out Event Processing with Event Hubs] exemplet visar hur du använder värden för händelsebearbetning med flera mottagare.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* [Microsoft Visual Studio 2017 eller senare](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan enligt följande steg i den här självstudien.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Mottagare**.
   
![Skapa konsolprogram](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Högerklicka på projektet **Mottagare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**.
2. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klicka på **Installera** och godkänn användningsvillkoren.
   
    ![Sök efter Event Processor Host NuGet-paketet](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus Event Hub –EventProcessorHost NuGet-paket ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alla sina beroenden.

## <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på **Mottagare**-projektet, klicka på **Lägg till** och klicka sedan på **Klass**. Kalla den nya klassen för **SimpleEventProcessor** och klicka sedan på **Lägg till** för att skapa klassen.
   
    ![Lägg till SimpleEventProcessor-klassen](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Lägg till följande uttryck överst i filen SimpleEventProcessor.cs:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Ersätt följande kod för innehållet i klassen:
    
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

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Uppdatera Main-metoden till att använda SimpleEventProcessor

1. I **Program**-klassen lägger du till följande `using`-uttryck överst i filen:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Ersätt den `Main` -metod i den `Program` klassen med följande kod, och Ersätt namnet på händelsehubben och anslutningssträngen på namnområdesnivå som du sparade tidigare, samt lagringskontot och nyckeln som du kopierade i föregående avsnitt. 
    
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
    
3. Kör programmet och kontrollera att det inte finns några fel.
  
Grattis! Du har nu fått meddelanden från en händelsehubb med värden för händelsebearbetning.


> [!NOTE]
> Den här guiden använder en enda instans av [EventProcessorHost](event-hubs-event-processor-host.md). För att öka genomströmning rekommenderar vi att du kör flera instanser av [EventProcessorHost](event-hubs-event-processor-host.md), enligt exemplet [Utskalad händelsebearbetning](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. 

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du .NET Framework-program som har fått meddelanden från en event hub. Om du vill lära dig mer om att skicka händelser till en event hub med .NET Framework, se [skicka händelser från event hub - .NET Framework](event-hubs-dotnet-framework-getstarted-send.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
