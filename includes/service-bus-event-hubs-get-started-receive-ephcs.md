## Empfangen von Nachrichten mit EventProcessorHost

[EventProcessorHost][] ist eine, das empfangende von Ereignissen von Event Hubs durch die Verwaltung von permanenten Prüfpunkten vereinfacht und parallelen Empfangsvorgängen von diesen Event Hubs. Mithilfe von [EventProcessorHost][], können Sie Ereignisse auf mehrere Empfänger aufteilen, selbst wenn in verschiedenen Knoten gehostet. Dieses Beispiel zeigt, wie Sie [EventProcessorHost][] für einen einzelnen Empfänger. Die [skalierte Verarbeitung][] Beispiel veranschaulicht, wie [EventProcessorHost][] mit mehreren Empfängern.

Zur Verwendung [EventProcessorHost][], benötigen Sie ein [Azure Storage-Konto][]:

1. Melden Sie sich auf die [klassischen Azure-Portal][], und klicken Sie auf **Neu** am unteren Bildschirmrand.

2. Klicken Sie auf **Data Services**, dann **Speicher**, dann **Schnellerfassung**, und geben Sie einen Namen für das Speicherkonto. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Speicherkonto erstellen**.

    ![][11]

3. Klicken Sie auf das neu erstellte Speicherkonto aus, und klicken Sie dann auf **Zugriffstastenverwaltung**:

    ![][12]

    Kopieren Sie den Zugriffsschlüssel für die spätere Verwendung in diesem Tutorial.

4. Erstellen Sie in Visual Studio ein neues Visual C#-Desktop-App-Projekt, mit dem **Konsolenanwendungsprojekte** -Projektvorlage. Nennen Sie das Projekt **Empfänger**.

    ![][14]

5. Im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

    Die **NuGet-Pakete verwalten** das Dialogfeld wird angezeigt.

6. Suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    ![][13]

    Daraufhin wird das NuGet-Paket [Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

7. Mit der rechten Maustaste die **Empfänger** Projekt, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Klasse**. Nennen Sie die neue Klasse **SimpleEventProcessor**, und klicken Sie dann auf **OK** die Klasse zu erstellen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei "SimpleEventProcessor.cs" hinzu:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

    Ersetzen Sie anschließend den Text der Klasse durch folgenden Code:

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
    ````

    Diese Klasse wird vom aufgerufen, die **EventProcessorHost** zur Verarbeitung von Ereignissen vom Event Hub empfangen. Beachten Sie, dass die `SimpleEventProcessor` -Klasse eine Stoppuhr verwendet, in regelmäßigen Abständen die Checkpoint-Methode aufrufen, auf die **EventProcessorHost** Kontext. Dadurch wird sichergestellt, dass der Empfänger bei einem Neustart maximal nur die Daten verlieren kann, die in den letzten fünf Minuten verarbeitet wurden.

9. In der **Programm** -Klasse verwenden, fügen Sie die folgenden `using` -Anweisungen am Anfang der Datei:

    ```
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.Threading;
    using System.Threading.Tasks;
    ```

    Ändern Sie dann wie folgt die `Main`-Methode in der `Program`-Klasse, und ersetzen Sie dabei den Namen und die Verbindungszeichenfolge des Event Hubs sowie das Speicherkonto und den Speicherschlüssel, die Sie in den vorherigen Abschnitten kopiert haben.

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{event hub connection string}";
      string eventHubName = "{event hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ````

> [AZURE.NOTE] In diesem Lernprogramm wird eine einzelne Instanz des [EventProcessorHost][]. Um den Durchsatz zu erhöhen, wird empfohlen, dass Sie mehrere Instanzen ausführen [EventProcessorHost][], Siehe die [skalierte Verarbeitung][] Beispiel. In diesen Fällen koordinieren sich die verschiedenen automatisch untereinander, um die Last der eingegangenen Ereignisse ausgeglichen zu verteilen. Wenn Sie möchten, dass mehrere Empfänger für jeden Prozess *alle* die Ereignisse, die Sie verwenden müssen die **ConsumerGroup** Konzept. Wenn Ereignisse von anderen Computern empfangen werden, ist es möglicherweise hilfreich, geben Sie Namen für [EventProcessorHost][] Instanzen basierend auf den Computer (oder Rollen), in denen sie bereitgestellt werden. Weitere Informationen zu diesen Themen finden Sie unter der [Übersicht über Event Hubs][] und [Event Hubs-Programmierhandbuch][] Themen.

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Scaled out event processing]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Azure classic portal]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png



