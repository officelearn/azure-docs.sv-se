## Empfangen von Nachrichten auf dem simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung, die Sie in [Erste Schritte mit IoT Hub] erstellt haben, um Cloud-zu-Gerät-Nachrichten von IoT Hub zu empfangen.

1. In Visual Studio in der **SimulatedDevice** Projekt, fügen Sie die folgende Methode, die die **Programm** Klasse.

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Die `ReceiveAsync`-Methode gibt die empfangene Nachricht asynchron zurück, sobald sie vom Gerät empfangen wird. Es gibt *null* nach Ablauf eines festlegbaren Timeout (in diesem Fall wird der Standardwert von 1 Minute verwendet). Falls dies geschieht, soll der Code weiter auf neue Nachrichten warten, was durch die Zeile `if (receivedMessage == null) continue` gewährleistet ist.

    Durch den Aufruf von `CompleteAsync()` wird IoT Hub benachrichtigt, dass die Nachricht erfolgreich verarbeitet wurde und sicher aus der Gerätewarteschlange entfernt werden kann. IoT Hub wird erneut übermittelt werden, wenn ein Fehler ist, der verhindert, dass die Geräte-app die Verarbeitung der Nachricht abgeschlossen aufgetreten, Es ist wichtig, dass die Nachricht Verarbeitungslogik in Geräte-app *Idempotent*, d. h. mehrmals die gleiche Nachricht empfangen, sollten das gleiche Ergebnis erzeugt. Eine Anwendung kann eine Nachricht auch vorübergehend verwerfen (`Abandon`), wodurch die Nachricht von IoT Hub zur zukünftigen Nutzung in der Warteschlange beibehalten werden muss, oder eine Nachricht ablehnen (`Reject`), wodurch sie dauerhaft aus der Warteschlange entfernt wird. Finden Sie unter [IoT Hub-Entwicklerhandbuch][IoT Hub Developer Guide - C2D] Weitere Informationen zum Lebenszyklus Cloud-zu-Gerät-Nachricht.

> [AZURE.NOTE] Wenn als Transport, AMQP HTTP/1 statt der `ReceiveAsync` sofort zurückgegeben werden. Das unterstützte Muster für Cloud-zu-Gerät-Nachrichten mit HTTP/1 entspricht Geräten, die nicht permanent verbunden sind und weniger häufig Nachrichten abrufen (d. h. seltener als alle 25 Minuten). Die Ausgabe von mehr HTTP/1-Empfangsvorgängen führt dazu, dass die Anforderungen von IoT Hub gedrosselt werden. Finden Sie unter [IoT Hub-Entwicklerhandbuch][IoT Hub Developer Guide - C2D] Weitere Informationen zu den Unterschieden zwischen AMQP und HTTP/1-Support und IoT Hub-Einschränkung.

2. Fügen Sie die folgende Methode in der **Main** Methode auf, unmittelbar bevor die `Console.ReadLine()` Zeile:

        ReceiveC2dAsync();

> [AZURE.NOTE] Aus Gründen der Einfachheit halber wird in diesem Lernprogramm keine wiederholungsrichtlinie nicht implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->


