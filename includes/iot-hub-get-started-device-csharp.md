## Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Windows-Konsolenanwendung, die ein Gerät simuliert, das D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) an einen IoT Hub sendet.

1. Fügen Sie in Visual Studio ein neues Visual C#-herkömmlichen Windows-Desktop-Projekt in der aktuellen Projektmappe die **Konsolenanwendungsprojekte** -Projektvorlage.  Nennen Sie das Projekt **SimulatedDevice**.

    ![][30]

2. Im Projektmappen-Explorer mit der rechten Maustaste die **SimulatedDevice** Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. In der **NuGet Package Manager** Fenster, stellen Sie sicher, dass die **Vorabversion einschließen** Option aktiviert ist. Suchen Sie nach **Microsoft Azure Geräte Client**, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung.

    Heruntergeladen, installiert und fügt einen Verweis auf die [Azure IoT - Geräts SDK NuGet-Paket][lnk-device-nuget].

4. Fügen Sie die folgenden `using` Anweisung am Anfang der **Program.cs** Datei:

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Fügen Sie die folgenden Felder zu der **Programm** -Klasse, und Ersetzen Sie dabei die Platzhalterwerte durch IoT Hub Hostnamen, der Sie, in abgerufen der *IoT Hub erstellen* Abschnitt und den Schlüssel des Geräts abgerufen, der *geräteidentität erstellen* Abschnitt:

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Fügen Sie die folgende Methode, die die **Programm** Klasse:

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

    Diese Methode sendet jede Sekunde eine neue D2C-Nachricht. Die Nachricht enthält ein JSON-serialisiertes Objekt mit der Geräte-ID sowie eine zufällig generierte Zahl, um einen Windgeschwindigkeitssensor zu simulieren.

7. Schließlich fügen Sie folgende Zeilen, die **Main** Methode:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  In der Standardeinstellung die **Erstellen** -Methode erstellt eine **DeviceClient** die das AMQP-Protokoll zur Kommunikation mit IoT Hub verwendet. Um das HTTPS-Protokoll verwenden möchten, verwenden Sie die Außerkraftsetzung von der **Erstellen** -Methode, die Ihnen ermöglicht, das Protokoll angeben. Wenn Sie das HTTPS-Protokoll verwenden möchten, sollten Sie auch hinzufügen der **Microsoft.AspNet.WebApi.Client** NuGet-Paket zu Ihrem Projekt enthalten die **System.Net.Http.Formatting** Namespace.


> [AZURE.NOTE] In diesem Lernprogramm wird keine wiederholungsrichtlinie, aus Gründen der Einfachheit, nicht implementiert. In Produktionscode, implementieren Sie die wiederholungsrichtlinien (z. B. ein Exponentielles Backoff), wie in der MSDN-Artikel [Handhabung vorübergehender Fehler][lnk-transient-faults].

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png


