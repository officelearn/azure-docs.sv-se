## Senden interaktiver Nachrichten vom simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung so, dass sie interaktive Gerät-zu-Cloud-Nachrichten an IoT Hub sendet.

1. In Visual Studio in der **SimulatedDevice** Projekt, fügen Sie die folgende Methode, die die **Programm** Klasse.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Diese Methode ähnelt der `SendDeviceToCloudMessagesAsync()` -Methode, die in erstellt wurde die [Get started with IoT Hub], der einzige Unterschied, dass die `MessageId` System-Eigenschaft und eine Eigenschaft namens `messageType` sind nun festgelegt.
    Die `MessageId`-Eigenschaft wird auf eine GUID (Globally Unique Identifier) festgelegt, die zum Deduplizieren von Nachrichtenempfangsvorgängen verwendet wird. Die `messageType`-Eigenschaft wird verwendet, um interaktive von Datenpunktnachrichten zu unterscheiden. Diese Informationen übergeben, wird in den Eigenschaften der Nachricht, stattdessen, die in der Nachricht Methodentext, damit der Ereignisprozessor im Back-End nicht deserialisieren die gesamte Nachricht nur für das routing verwenden.

> [AZURE.NOTE] Es ist wichtig, die die `MessageId`, verwendet, um interaktive Nachrichten dedupliziert werden sollen, auf dem Gerät erstellt werden, wie mehrere Retrasmissions derselben Nachricht vom Gerät zeitweilig Netzwerkkommunikation (oder andere Fehler) führen können. Eine semantische Nachrichten-ID (z. B. ein Hash der relevanten Datenfelder der Nachricht) kann auch anstelle einer GUID verwendet werden.

2. Fügen Sie die folgende Methode in der **Main** Methode auf, unmittelbar bevor die `Console.ReadLine()` Zeile:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE] Aus Gründen der Einfachheit halber wird in diesem Lernprogramm keine wiederholungsrichtlinie nicht implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png






