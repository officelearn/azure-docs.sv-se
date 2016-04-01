## Bereitstellen eines Azure-Speicherkontos
Da vom simulierten Gerät eine Datei in ein Azure Storage-BLOB hochgeladen wird, müssen Sie über ein Azure-Speicherkonto verfügen. Sie können eine vorhandene oder führen Sie die Schritte [About Azure Storage] um einen neuen zu erstellen. Notieren Sie die Verbindungszeichenfolge für das Speicherkonto.

## Senden eines Azure-BLOB-URIs an das simulierte Gerät

In diesem Abschnitt ändern Sie die **SendCloudtoDevice** Konsolen-app, die Sie in [Send-Cloud-zu-Gerät-Nachrichten mit IoT Hub] zum Einschließen von Azure erstellt blob-URI mit SAS. Dadurch ist das Cloud-Back-End in der Lage, ausschließlich dem Empfänger der C2D-Nachricht Schreibzugriff auf das Blob zu gewähren.

1. In Visual Studio mit der rechten Maustaste die **SendCloudtoDevice** Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung. 

    Heruntergeladen, installiert und fügt einen Verweis auf die [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. In der **Program.cs** Datei, fügen Sie am Anfang der Datei die folgende Anweisung:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. In der **Programm** -Klasse die folgende Klassenfelder, und Ersetzen Sie dabei die Verbindungszeichenfolge für das Speicherkonto hinzu.

        static string storageConnectionString = "{storage connection string}";

    Fügen Sie die folgende Methode hinzu (Sie können alle Blob-Containername ersetzen, die in diesem Lernprogramm wird **Iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Diese Methode erstellt einen neuen blobverweis und eine shared Access Signature-URI generiert, wie in beschrieben [Erstellen und Verwenden einer SAS mit dem Blob-Dienst](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-2/). Beachten Sie, dass die oben genannte Methode einen Signatur-URI generiert, der 24 Stunden gültig ist. Wenn das Zielgerät mehr Zeit zum Hochladen der Datei benötigt (weil es z. B. selten eine Verbindung herstellt, eine unzuverlässige Verbindung zum Hochladen einer umfangreichen Datei nutzt), könnten Sie längere Ablaufzeiten für Signaturen in Betracht ziehen.

5. Ändern der **SendCloudToDeviceMessageAsync** auf folgende Weise:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Diese Methode sendet eine Cloud-zu-Gerät-Nachricht, die zwei Anwendungseigenschaften enthält: Eine identifiziert diese Nachricht als Befehl zum Hochladen einer Datei, und die andere enthält den BLOB-URI. Sie fordert zusätzlich vollständige Übermittlungsbestätigungen an. Beachten Sie, dass die Informationen in den beiden Anwendungseigenschaften in einen Nachrichtentext serialisiert werden können. Das Serialisieren und Deserialisieren der Informationen würde jedoch zusätzlichen Verarbeitungsaufwand verursachen.

<!-- Links -->

[About Azure Storage]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->









