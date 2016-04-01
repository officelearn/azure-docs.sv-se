Ein neues Einfügeskript wird registriert, das beim Einfügen eines neuen Aktivitätselements eine SAS generiert.

0. Wenn Sie das Speicherkonto noch nicht erstellt haben, finden Sie unter [ein Speicherkonto, das Erstellen von](../storage/storage-create-storage-account.md).

1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Speicher**, klicken Sie auf das Speicherkonto ein, und klicken Sie dann **Schlüssel verwalten**. 

2. Notieren Sie sich die **Speicherkontoname** und **Zugriffstaste**.

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. In Ihrem mobilen Dienst, klicken Sie auf die **konfigurieren** Registerkarte, einen Bildlauf nach unten zum **App-Einstellungen** und geben Sie einen **Namen** und **Wert** Paar für jedes der folgenden Elemente, die Sie aus dem Speicherkonto zu erhalten, und klicken Sie dann **Speichern**.

    + `STORAGE_ACCOUNT_NAME`
    + `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    Der Zugriffsschlüssel des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können auf diesen Schlüssel von jedem beliebigen Serverskript zur Laufzeit zugreifen. Weitere Informationen finden Sie unter [App settings].

4. Klicken Sie auf der Registerkarte Konfigurieren sicher, dass [dynamische Schema](http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7) aktiviert ist. "Dynamisches Schema" muss aktiviert sein, damit neue Spalten in der TodoItem-Tabelle eingefügt werden können. "Dynamisches Schema" sollte in Produktionsdiensten nicht aktiviert sein.

4. Klicken Sie auf die **Daten** Registerkarte, und klicken Sie dann auf die **TodoItem** Tabelle. 

5.  In **Todoitem**, klicken Sie auf die **Skript** Registerkarte, und wählen Sie **Einfügen**, ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie dann **Speichern**:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
        
        function insert(item, user, request) {
            // Get storage account settings from app settings. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';
        
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.toLowerCase();
        
                // If it does not already exist, create the container 
                // with public read access for blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {
        
                        // Provide write access to the container for the next 5 mins.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
        
                        // Generate the upload URL with SAS for the new image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
        
                        // Set the query string.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
        
                        // Set the full path on the new new item, 
                        // which is used for data binding on the client. 
                        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
        
                    } else {
                        console.error(error);
                    }
                    request.execute();
                });
            } else {
                request.execute();
            }
        }

    Dadurch wird die Funktion ersetzt, die aufgerufen wird, wenn ein Einfügevorgang in der Tabelle "TodoItem" mit einem neuen Skript erfolgt. Dieses neue Skript generiert eine neue SAS für den Einfügevorgang, die fünf Minuten gültig ist. Dann weist es den Wert der generierten SAS der `sasQueryString`-Eigenschaft des zurückgegebenen Elements zu. Die `imageUri`-Eigenschaft wird auch auf den Ressourcenpfad des neuen BLOB festgelegt, um die Imageanzeige während der Bindung in der Client-UI zu ermöglichen.

    >[AZURE.NOTE] Dieser Code erstellt eine SAS für ein einzelnes BLOB. Wenn Sie mehrere Blobs in einen Container mit derselben SAS hochladen müssen, können Sie stattdessen Aufrufen der [GenerateSharedAccessSignature-Methode](http://go.microsoft.com/fwlink/?LinkId=390455)</a> mit einem leeren Blob-Ressourcennamen wie folgt: 
    >                 
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);

Als Nächstes aktualisieren Sie die Quickstart-App, um unter Verwendung der beim Einfügevorgang generierten SAS Funktionalität zum Hochladen von Images hinzuzufügen.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7


