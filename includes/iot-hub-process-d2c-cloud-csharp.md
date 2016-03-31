## Verarbeiten von Device-to-Cloud (D2C)-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die D2C-Nachrichten von IoT Hub verarbeitet. IOT Hub macht eine [Event Hubs][Event Hubs Overview]-kompatibler Endpunkt Gerät-zu-Cloud-Nachrichten zu lesen. In diesem Lernprogramm wird [EventProcessorHost] verarbeitet diese Nachrichten in einer Konsolen-app. Weitere Informationen zum Verarbeiten von Nachrichten aus Event Hubs erhalten Sie die [Get Started with Event Hubs] Lernprogramm.

Die größte Herausforderung beim Implementieren der zuverlässigen Speicherung von Datenpunktnachrichten oder Weiterleiten von interaktiven Nachrichten besteht darin, dass für die Event Hubs-Ereignisverarbeitung der Nachrichtenconsumer seinen Status überprüfen muss. Darüber hinaus sollten Prüfpunkte zur Erzielung eines hohen Durchsatzes beim Lesen von Event Hubs in großen Batches verarbeitet werden. Hierbei besteht die Möglichkeit, dass eine große Zahl von Nachrichten doppelt verarbeitet wird. In diesem Lernprogramm wird veranschaulicht, wie Azure-Speicherschreibvorgänge und Service Bus-Deduplizierungsfenster mit Ereignisprozessorhost-Prüfpunkten synchronisiert werden.

Um Nachrichten zuverlässig in Azure-Speicher zu schreiben, stellen wir verwenden die [block-Blobs][Azure Block Blobs] Feature von einzelnen Block-Commit. Der Ereignisprozessor akkumuliert Nachrichten im Arbeitsspeicher, bis es an der Zeit ist, einen Prüfpunktvorgang auszuführen (also nachdem der akkumulierte Puffer die Größe der maximalen Blockgröße von 4 Mb übersteigt oder nachdem die Service Bus-Deduplizierungszeitfenster abgelaufen sind). Vor dem Setzen von Prüfpunkten wird dann ein Commit für einen neuen Block an den Blob durchgeführt.

Der Ereignisprozessor nutzt Event Hubs-Nachrichten-Offsets als Block-IDs. Dies ermöglicht eine Überprüfung der Deduplizierung vor dem Ausführen eines Commits für den neuen Block an den Speicher, und ein etwaiger Konflikt zwischen einem Block mit durchgeführtem Commit und dem Prüfpunkt wird verhindert.

> [AZURE.NOTE] Dieses Lernprogramm verwendet ein einzelnes Speicherkonto zum Schreiben von alle Nachrichten, die IoT Hub abgerufen. Finden Sie unter [Azure Storage scalability Guidelines] entscheiden, ob Sie mehrere Azure Storage-Konto in Ihre Lösung benötigen.

Um Duplikate bei der Verarbeitung von interaktiven Nachrichten zu vermeiden, nutzen wir die Service Bus-Deduplizierungsfunktion. Indem jede interaktive Nachricht mit einer eindeutigen `MessageId`-Kennzeichnung versehen wird, kann Service Bus sicherstellen, dass im angegebenen Deduplizierungszeitfenster nicht zwei Nachrichten mit der gleichen `MessageId` an die Empfänger übermittelt werden. Diese Deduplizierung sorgt zusammen mit der Abschlusssemantik pro Nachricht von Service Bus-Warteschlangen dafür, dass die zuverlässige Verarbeitung von interaktiven Nachrichten deutlich vereinfacht wird.

Um sicherzustellen, dass keine Nachrichten außerhalb des Deduplizierungsfensters erneut übermittelt werden, synchronisieren wir den Prüfpunktmechanismus des Ereignisprozessorhosts mit dem Deduplizierungsfenster der Service Bus-Warteschlange. Dies wird erreicht, indem ein Prüfpunkt mindestens einmal pro Zeitfenster abläuft (in diesem Lernprogramm ist dies eine Stunde).

> [AZURE.NOTE] Dieses Lernprogramm verwendet eine einzelne partitionierte Service Bus-Warteschlange zu verarbeiten, die alle interaktiven Nachrichten aus IoT Hub abgerufen. Abhängig von der Projektmappe werden müssen, finden Sie unter [Service Bus documentation] Weitere Informationen zur Verwendung von Service Bus-Warteschlangen.

### Bereitstellen eines Azure-Speicherkontos und einer Service Bus-Warteschlange
Zur Verwendung [EventProcessorHost], benötigen Sie ein Azure Storage-Konto. Sie können eine vorhandene oder führen Sie die Schritte [About Azure Storage] um einen neuen zu erstellen. Notieren Sie die Verbindungszeichenfolge für das Speicherkonto.

Sie benötigen außerdem eine Service Bus-Warteschlange, um die zuverlässige Verarbeitung von interaktiven Nachrichten zu aktivieren. Sie Erstellen einer Warteschlange können programmgesteuert eine Stunde Deduplizierung im Fenster wie in erläutert [mit Service Bus-Warteschlangen][Service Bus Queue], oder verwenden Sie die [Azure portal], folgende Schritte:

1. Klicken Sie auf **Neu** in der unteren linken Ecke, dann **Anwendungsdienste**, dann **Service Bus**, dann **Warteschlange**, dann **benutzerdefinierte**, wählen Sie den Namen **d2ctutorial**, wählen Sie dann ein Fenster Deduplizierung von 1 Stunde.

    ![][30]

2. Klicken Sie auf den Namen der Warteschlange (**d2ctutorial**), dann **konfigurieren**, und erstellen Sie zwei SAS-Richtlinien, eine mit der Bezeichnung **Senden** mit **Senden** Berechtigungen, und einen **Lauschen** mit **Abhören** Berechtigungen. Klicken Sie auf **Speichern** am Ende, wenn fertig.

    ![][31]

3. Klicken Sie auf **Dashboard** in der oberen **Verbindungsinformationen** ein Ende, beachten Sie die beiden Verbindungszeichenfolgen.

    ![][32]

### Erstellen des Ereignisprozessors

1. In der aktuellen Visual Studio-Projektmappe, klicken Sie auf **File-hinzufügen -> Projekt** erstellen ein neues Visual C#-Desktop-App-Projekt, mit dem **Konsolenanwendungsprojekte** Projektvorlage. Nennen Sie das Projekt **ProcessDeviceToCloudMessages**.

    ![][10]

2. Im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

    Die **NuGet-Pakete verwalten** das Dialogfeld wird angezeigt.

3. Suchen Sie nach `WindowsAzure.ServiceBus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    Heruntergeladen, installiert und fügt einen Verweis auf die [Azure Service Bus NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus), mit allen Abhängigkeiten.

4. Suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    Daraufhin wird das NuGet-Paket [Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

5. Mit der rechten Maustaste die **ProcessDeviceToCloudMessages** Projekt, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Klasse**. Nennen Sie die neue Klasse **StoreEventProcessor**, und klicken Sie dann auf **OK** die Klasse zu erstellen.

6. Fügen Sie die folgenden Anweisungen am Anfang der Datei "StoreEventProcessor.cs" hinzu:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Ersetzen Sie anschließend den Text der Klasse durch folgenden Code:

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }

                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    Diese Klasse wird vom aufgerufen, die **EventProcessorHost** Gerät-zu-Cloud-Nachrichten verarbeiten, die von IoT Hub und implementiert die Hauptlogik zum zuverlässigen Speichern von Nachrichten in einem blobcontainer und Weiterleitung von interaktiven Nachrichten an Service Bus-Warteschlange empfangen.
    Die `OpenAsync()` -Methode initialisiert die  `currentBlockInitOffset` Variable, die verfolgt den aktuellen Offset der ersten Nachricht, die von diesem Ereignisprozessor gelesen wird (Beachten Sie, dass jeder Prozessor für eine einzelne Partition zuständig ist).

    Die `ProcessEventsAsync()`-Methode empfängt einen Batch mit Nachrichten von IoT Hub, die dann wie folgt verarbeitet werden. Interaktive Nachrichten werden an die Service Bus-Warteschlange gesendet, während Datenpunktnachrichten an das memoryBuffer-Element mit dem Namen `toAppend` angefügt werden. Falls der Arbeitsspeicherpuffer das Blocklimit erreicht (4 Mb) oder die Service Bus-Deduplizierungszeitfenster seit dem letzten Prüfpunkt abgelaufen sind (in diesem Lernprogramm eine Stunde), wird ein Prüfpunkt ausgelöst.

    Die `AppendAndCheckpoint()`-Methode generiert zuerst eine blockId für den Block, der angefügt werden soll. Da es für Azure-Speicher erforderlich ist, dass alle Block-IDs die gleiche Länge haben, wird der Offset mit führenden Nullen (`currentBlockInitOffset.ToString("0000000000000000000000000")`) aufgefüllt. Wenn ein Block mit dieser ID im Blob bereits vorhanden ist, überschreibt die Methode ihn mit dem aktuellen Block.

> [AZURE.NOTE] Um den Code zu vereinfachen, werden mit diesem Programm eine einzelnes Blob-Datei pro Partition zum Speichern von Nachrichten verwendet. Für eine echte Lösung würde das „File Rolling“ implementiert werden, indem zusätzliche Dateien erstellt werden, wenn sie eine bestimmte Größe erreichen (ein Azure-Blob kann höchstens 195 Gb aufweisen) oder wenn ein bestimmter Zeitraum verstrichen ist (z. B. `fileName_{partitionId}_{date-time}`).

7. In der **Programm** -Klasse verwenden, fügen Sie die folgenden `using` -Anweisungen am Anfang:

        using Microsoft.ServiceBus.Messaging;

    Ändern Sie dann die **Main** Methode, um die **Programm** wie unten dargestellt, und Ersetzen Sie dabei den IoT Hub **Iothubowner** Verbindungszeichenfolge (aus [erste Schritte mit IoT Hub] Lernprogramm), Verbindungszeichenfolge des Speichers und der Service Bus-Verbindungszeichenfolge mit **Senden** Berechtigungen für die Warteschlange mit dem Namen **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE] Aus Gründen der Einfachheit halber in diesem Lernprogramm verwendet eine einzelne Instanz von [EventProcessorHost]. Finden Sie unter [Event Hubs Programming Guide] und das Lernprogramm [Prozess-Gerät-zu-Cloud-Nachrichten] Weitere Informationen zum Gerät-zu-Cloud-Nachrichten verarbeiten.

## Empfangen von interaktiven Nachrichten
In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die interaktive Nachrichten aus der Service Bus-Warteschlange empfängt. Finden Sie unter [Multi-Tier-Anwendungsentwicklung mit Service Bus][] für Weitere Informationen zum Aufbau einer Lösung mit Service Bus.

1. In der aktuellen Visual Studio-Projektmappe, erstellen Sie ein neues Visual C#-Desktop-App-Projekt, mit dem **Konsolenanwendung** -Projektvorlage. Nennen Sie das Projekt **ProcessD2cInteractiveMessages**.

2. Im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**.

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `WindowsAzure.Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    Heruntergeladen, installiert und fügt einen Verweis auf die [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus), mit allen Abhängigkeiten.

4. Fügen Sie die folgenden `using` Anweisung am Anfang der **Program.cs** Datei:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Schließlich fügen die folgenden Zeilen der **Main** -Methode, und Ersetzen Sie dabei die Verbindungszeichenfolge mit **Überwachen** Berechtigungen für die Warteschlange mit dem Namen **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Get Started with Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[Service Bus documentation]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Event Hubs Programming Guide]: ../event-hubs/event-hubs-programming-guide.md

[Azure preview portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[Azure Portal]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Build multi-tier applications with Service Bus]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md


<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png


