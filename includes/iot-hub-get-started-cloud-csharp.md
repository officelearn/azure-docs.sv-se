## Erstellen einer Geräteidentität

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die eine neue Geräteidentität in der Identitätsregistrierung Ihres IoT Hubs erstellt. Ein Gerät kann nur eine Verbindung mit dem IoT Hub herstellen, wenn in der Geräteidentitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Finden Sie in der **Registrierung des Geräts Identität** Teil der [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity] Weitere Informationen. Beim Ausführen dieser Konsolenanwendung werden eine eindeutige Geräte-ID und ein Schlüssel erstellt, mit denen sich das Gerät beim Senden von D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) beim IoT Hub identifizieren kann.

1. Fügen Sie in Visual Studio ein neues Visual C#-herkömmlichen Windows-Desktop-Projekt in der aktuellen Projektmappe die **Konsolenanwendungsprojekte** -Projektvorlage. Nennen Sie das Projekt **CreateDeviceIdentity**.

    ![][10]

2. Im Projektmappen-Explorer mit der rechten Maustaste die **CreateDeviceIdentity** Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. In der **NuGet Package Manager** Fenster, stellen Sie sicher, dass die **Vorabversion einschließen** Option aktiviert ist. Suchen Sie nach **Microsoft Azure-Geräten**, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    ![][11]

4. Heruntergeladen, installiert und fügt einen Verweis auf die [Microsoft Azure Geräte SDK][lnk-nuget-device-sdk] NuGet-Paket.

4. Fügen Sie die folgenden `using` Anweisungen am Anfang der **Program.cs** Datei:

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Fügen Sie die folgenden Felder mit den **Programm** Ersetzen des Werts für die Platzhalter durch die Verbindungszeichenfolge für den im vorherigen Abschnitt erstellten IoT Hub-Klasse:

        static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Fügen Sie die folgende Methode, die die **Programm** Klasse:

        private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Diese Methode erstellt eine neue geräteidentität mit der ID **MyFirstDevice** (Wenn diese Geräte-ID in der Registrierung bereits vorhanden ist, der Code einfach abgerufen werden Informationen zu den vorhandenen Geräten). Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel im simulierten Gerät, um eine Verbindung mit dem IoT Hub herzustellen.

7. Schließlich fügen Sie folgende Zeilen, die **Main** Methode:

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Führen Sie diese Anwendung aus, und notieren Sie den Geräteschlüssel.

    ![][12]

> [AZURE.NOTE] Die IoT Hub-Registrierung Identität speichert nur die Geräte-Identitäten, um sicheren Zugriff auf den Hub zu aktivieren. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung verlangt, dass weitere gerätespezifische Metadaten gespeichert werden, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Finden Sie unter [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity] Weitere Informationen.

## Empfangen von Gerät-an-Cloud-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die D2C-Nachrichten (Device-to-Cloud) aus dem IoT Hub liest. IoT Hub macht eine [Event Hubs][lnk-event-hubs-overview]-kompatibler Endpunkt, um das Gerät-zu-Cloud-Nachrichten lesen können. Zur Vereinfachung wird in diesem Tutorial ein einfacher Reader erstellt, der für eine Bereitstellung mit hohem Durchsatz nicht geeignet ist. Die [Gerät-zu-Cloud-Nachrichten verarbeiten][lnk-processd2c-tutorial] Lernprogramm erfahren Sie, wie Gerät-zu-Cloud-Nachrichten individuell verarbeitet und die [Erste Schritte mit Event Hubs][lnk-eventhubs-tutorial] Lernprogramm enthält weitere Informationen zum Verarbeiten von Nachrichten aus Event Hubs.

1. Fügen Sie in Visual Studio ein neues Visual C#-herkömmlichen Windows-Desktop-Projekt in der aktuellen Projektmappe die **Konsolenanwendungsprojekte** -Projektvorlage. Nennen Sie das Projekt **ReadDeviceToCloudMessages**.

    ![][10]

2. Im Projektmappen-Explorer mit der rechten Maustaste die **ReadDeviceToCloudMessages** Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. In der **NuGet Package Manager** Fenster, stellen Sie sicher, dass die **Vorabversion einschließen** Option aktiviert ist. Suchen Sie nach **WindowsAzure.ServiceBus**, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    Heruntergeladen, installiert und fügt einen Verweis auf die [Azure Service Bus][lnk-servicebus-nuget], mit allen Abhängigkeiten.

4. Fügen Sie die folgenden `using` Anweisung am Anfang der **Program.cs** Datei:

        using Microsoft.ServiceBus.Messaging;

5. Fügen Sie die folgenden Felder mit den **Programm** -Klasse, und Ersetzen Sie den Platzhalterwert durch die Verbindungszeichenfolge für den erstellten in IoT Hub die *IoT Hub erstellen* Abschnitt:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Fügen Sie die folgende Methode, die die **Programm** Klasse:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Diese Methode verwendet eine **EventHubReceiver** Instanz zum Empfangen von Nachrichten aus allen der IoT Hub-Gerät-zu-Cloud Partitionen erhalten. Beachten Sie, wie Sie übergeben ein `DateTime.Now` Parameter beim Erstellen der **EventHubReceiver** Objekt, sodass es nur gesendete Nachrichten empfängt, nachdem dieser gestartet wurde.

7. Schließlich fügen Sie folgende Zeilen, die **Main** Methode:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: event-hubs-overview.md

[lnk-nuget-device-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png


