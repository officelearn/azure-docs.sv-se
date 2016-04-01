## Senden einer Cloud-zu-Gerät-Nachricht vom App-Back-End

In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die Cloud-zu-Gerät-Nachrichten an die simulierte Geräte-App sendet.

1. In der aktuellen Visual Studio-Projektmappe, erstellen Sie ein neues Visual C#-Desktop-App-Projekt, mit dem **Konsolenanwendung** -Projektvorlage. Nennen Sie das Projekt **SendCloudToDevice**.

    ![][20]

2. Im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `Microsoft Azure Devices`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung. 

    Heruntergeladen, installiert und fügt einen Verweis auf die [Azure IoT - Service SDK NuGet package].

4. Fügen Sie die folgenden `using` Anweisung am Anfang der **Program.cs** Datei:

        using Microsoft.Azure.Devices;

5. Fügen Sie die folgenden Felder mit den **Programm** -Klasse, und Ersetzen Sie dabei den Platzhalterwert mit der IoT Hub-Verbindungszeichenfolge aus [Get started with IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Fügen Sie die folgende Methode, die die **Programm** Klasse:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Diese Methode sendet eine neue Cloud-zu-Gerät-Nachricht an das Gerät mit der ID `myFirstDevice`. Ändern Sie diesen Parameter entsprechend für den Fall, dass Sie geändert haben, von den [Get started with IoT Hub].

7. Schließlich fügen Sie folgende Zeilen, die **Main** Methode:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. In Visual Studio die Projektmappe mit der rechten Maustaste und wählen **festlegen Startprojekte...**. Wählen Sie **mehrere Startprojekte**, und wählen Sie dann die **Start** Aktion für **ProcessDeviceToCloudMessages**, **SimulatedDevice**, und **SendCloudToDevice**.

9.  Drücken Sie **F5**, sehen Sie alle drei Anwendung starten. Wählen Sie die **SendCloudToDevice** Windows, und drücken Sie **EINGABETASTE**: sollte die Meldung von der simulierten Anwendung angezeigt.

    ![][21]

## Empfangen von Übermittlungsfeedback
Es ist möglich, für jede Cloud-zu-Gerät-Nachricht Übermittlungsbestätigungen (oder Ablaufbestätigungen) von IoT Hub anzufordern. Auf diese Weise kann die Wiederholungs- oder Kompensierungslogik einfach vom Cloud-Back-End informiert werden. Finden Sie in der [IoT Hub-Entwicklerhandbuch][IoT Hub Developer Guide - C2D] Weitere Informationen zu Cloud-zu-Gerät-Feedback.

In diesem Abschnitt ändern Sie die **SendCloudToDevice** Feedback anfordern und Empfangen von IoT Hub-app.

1. In Visual Studio in der **SendCloudToDevice** Projekt, fügen Sie die folgende Methode, die die **Programm** Klasse.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Beachten Sie, dass das Empfangsmuster mit dem Muster zum Empfangen von Cloud-zu-Gerät-Nachrichten von der Geräte-App identisch ist.

2. Fügen Sie die folgende Methode in der **Main** Methode direkt nach der `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` Zeile:

        ReceiveFeedbackAsync();

3. Um Feedback für die Zustellung der Nachricht Cloud-zu-Gerät anzufordern, müssen Sie angeben, eine Eigenschaft in der **SendCloudToDeviceMessageAsync** Methode. Fügen Sie direkt nach der Zeile `var commandMessage = new Message(...);` folgende Zeile hinzu:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Drücken Sie die apps auszuführen **F5**, sehen Sie alle drei Anwendung starten. Wählen Sie die **SendCloudToDevice** Windows, und drücken Sie **EINGABETASTE**: die empfangene Nachricht durch die simulierte Anwendung und nach ein paar Sekunden, die Feedback-Nachricht erhält, sollte Ihre **SendCloudToDevice** app.

    ![][22]

> [AZURE.NOTE] Aus Gründen der Einfachheit halber wird in diesem Lernprogramm keine wiederholungsrichtlinie nicht implementiert. In Produktionscode ist es empfohlen, zum Implementieren von wiederholungsrichtlinien (z. B. Exponentielles Backoff), wie in der MSDN-Artikel [Transient Fault Handling].

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png








