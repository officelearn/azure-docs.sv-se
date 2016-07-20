## Ta emot meddelanden med EventProcessorHost

[EventProcessorHost][] är en .NET-klass som förenklar mottagandet av händelser från Event Hubs genom att hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs. Med hjälp av [EventProcessorHost][] kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder [EventProcessorHost][] för en enda mottagare. Exemplet på [utskalad händelsebearbetning][] visar hur man använder [EventProcessorHost][] med flera mottagare.

För att kunna använda [EventProcessorHost][] behöver du ett [Azure Storage-konto][]:

1. Logga in på den [klassiska Azure-portalen][] och klicka på **NY** längst ned på skärmen.

2. Klicka på **Data Services**, sedan på **Storage**, sedan på **Snabbregistrering** och ange ett namn för ditt lagringskonto. Välj önskad region och klicka sedan på **Skapa lagringskonto**.

    ![][11]

3. Klicka på det nyligen skapade lagringskontot och klicka sedan på **Hantera åtkomstnycklar**:

    ![][12]

    Kopiera den primära åtkomstnyckeln för senare användning i den här guiden.

4. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Mottagare**.

    ![][14]

5. Högerklicka på lösningen i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**.

6. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kontrollera att projektnamnet (**Mottagare**) har angetts i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren.

    ![][13]

    [NuGet-paketet EventProcessorHost för Azure Service Bus Event Hub ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alla sina beroenden, hämtas och installeras, och en referens till det läggs till.

7. Högerklicka på **Mottagare**-projektet, klicka på **Lägg till** och klicka sedan på **Klass**. Kalla den nya klassen för **SimpleEventProcessor** och klicka sedan på **Lägg till** för att skapa klassen.

    ![][15]

8. Lägg till följande uttryck överst i filen SimpleEventProcessor.cs:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Ersätt sedan följande kod för innehållet i klassen:

    ```
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

9. I **Program**-klassen lägger du till följande `using`-uttryck överst i filen:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Ersätt sedan `Main`-metoden i `Program`-klassen med följande kod, vilket ersätter det Event Hub-namn och anslutningssträngen för namnområdesnivån som du sparade tidigare, samt lagringskontot och nyckeln som du kopierade i föregående avsnitt. 

    ```
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

> [AZURE.NOTE] Den här guiden använder en enda instans av [EventProcessorHost][]. För att öka genomströmning rekommenderas att du kör flera instanser av [EventProcessorHost][], enligt exemplet [utskalad händelsebearbetning][]. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för [EventProcessorHost][]-instanser baserat på de datorer (eller roller) som de har distribuerats i. Mer information om de här ämnena finns i [Översikt över Event Hubs][] och [Programmeringsguide för Event Hubs][].

<!-- Links -->
[Översikt över Event Hubs]: event-hubs-overview.md
[Programmeringsguide för Event Hubs]: event-hubs-programming-guide.md
[utskalad händelsebearbetning]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure Storage-konto]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[klassiska Azure-portalen]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp1.png
[15]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp2.png




<!--HONumber=Jun16_HO2-->


