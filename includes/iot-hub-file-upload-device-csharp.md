## Hochladen einer Datei von einem simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung, die Sie in [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub] erstellt haben, um Cloud-zu-Gerät-Nachrichten von IoT Hub zu empfangen.

1. In Visual Studio mit der rechten Maustaste die **SimulatedDevice** Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung. 

    Heruntergeladen, installiert und fügt einen Verweis auf die [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. In der **Program.cs** Datei, fügen Sie am Anfang der Datei die folgende Anweisung:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. In der **Programm** Klasse, ändern Sie die **ReceiveC2dAsync** Methode wie folgt:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    Auf diese Weise die **ReceiveC2dAsync** unterscheiden, Nachrichten mit der `command` Eigenschaft festgelegt, um `FileUpload`, wird die vom behandelt die **UploadFileToBlobAsync** Methode.

    Fügen Sie die Methode unten hinzu, um die Befehle für Dateiuploads zu behandeln.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Diese Methode verwendet das Azure Storage SDK zum Hochladen eines zufällig generierten 10 MB-BLOBs unter dem angegebenen URI. Finden Sie unter [Azure Storage - How to use blobs] Weitere Informationen zum Hochladen von Blobs.

> [AZURE.NOTE] Beachten Sie, wie diese Implementierung der simulierten Gerät der Cloud-zu-Gerät-Nachricht abgeschlossen ist, wenn das Blob hochgeladen wurde. Dieser Ansatz vereinfacht die Verarbeitung der hochgeladenen Dateien im Back-End, da die Übermittlungsbestätigung die Verfügbarkeit der hochgeladenen Datei für die Verarbeitung bestätigt. Wie in erläutert die [IoT Hub-Entwicklerhandbuch][IoT Hub Developer Guide - C2D], kann jedoch eine Nachricht, die nicht, bevor beendet ist die *Sichtbarkeits-Zeitlimit* (in der Regel 1 Minute) wird in die Warteschlange, zurückgestellt und **ReceiveAsync()** Methode erneut empfangen. Bei Szenarien, in denen der Dateiupload länger dauern kann, empfiehlt es sich u. U. für das simulierte Gerät, die aktuellen Uploadaufträge in einem permanenten Speicher vorzuhalten. Dadurch kann das simulierte Gerät die Cloud-zu-Gerät-Nachricht vor Beendigung des Dateiuploads abschließen und dann das Back-End in einer Gerät-zu-Cloud-Nachricht über den Abschluss informieren.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure Storage - How to use blobs]: https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->







