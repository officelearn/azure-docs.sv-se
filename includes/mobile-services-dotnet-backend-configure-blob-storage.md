
##Installieren des Speicherclients im Mobildienstprojekt

Um eine SAS für das Hochladen von Bildern in den Blob-Speicher generieren zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert. 

1. In **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste den mobilen Dienst-Projekt, und wählen Sie dann **NuGet-Pakete verwalten**.

2. Wählen Sie im linken Bereich die **Online** Kategorie wählen **nur Stabile**, suchen Sie nach **WindowsAzure.Storage**, klicken Sie auf **Installieren** auf die **Azure-Speicher** Paket, und stimmen Sie dem Lizenzvertrag. 

    ![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

    Die Clientbibliothek für Azure-Speicherdienste wird dem Projekt für den mobilen Dienst hinzugefügt.

##Aktualisieren der TodoItem-Definition im Datenmodell

Die TodoItem-Klasse definiert das Datenobjekt, und Sie müssen dieser Klasse dieselben Eigenschaften hinzufügen, die Sie dem Client hinzugefügt haben.

1. In Visual Studio 2013 öffnen Sie Ihr Projekt für den mobilen Service, erweitern Sie den DataObjects-Ordner und öffnen Sie dann die Projektdatei TodoItem.cs.
    
2. Fügen Sie die folgenden neuen Eigenschaften zu den **TodoItem** Klasse:

        public string containerName { get; set; }
        public string resourceName { get; set; }
        public string sasQueryString { get; set; }
        public string imageUri { get; set; } 

    Diese Eigenschaften werden zum Generieren der SAS und zum Speichern der Bilddaten verwendet. Beachten Sie, dass die Groß-/Kleinschreibung dieser Eigenschaften der JavaScript-Backend-Version entspricht. 

    >[AZURE.NOTE] Bei Verwendung des Standard-datenbankinitialisierers Entity Framework und die Datenbank neu erstellen, wenn es eine Änderung des Datenmodells in Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Der Standard-Initialisierer kann nicht für eine SQL-Datenbank in Azure angewendet werden. Weitere Informationen finden Sie unter [wie mit Code First-Migrationen zum Aktualisieren des Modells Daten](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##Aktualisieren des TodoItem-Controllers zur Erzeugung einer freigegebenen Zugriffssignatur SAS 

Die vorhandene **TodoItemController** wird aktualisiert, damit die **PostTodoItem** -Methode eine SAS generiert, wenn ein neues TodoItem eingefügt wird. Außerdem gilt: 

0. Wenn Sie das Speicherkonto noch nicht erstellt haben, finden Sie unter [How To Create a Storage Account].

1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Speicher**, klicken Sie auf das Speicherkonto ein, und klicken Sie dann **Schlüssel verwalten**. 

2. Notieren Sie sich die **Speicherkontoname** und **Zugriffstaste**.
 
3. In Ihrem mobilen Dienst, klicken Sie auf die **konfigurieren** Registerkarte, einen Bildlauf nach unten zum **App-Einstellungen** und geben Sie einen **Namen** und **Wert** Paar für jedes der folgenden Elemente, die Sie aus dem Speicherkonto zu erhalten, und klicken Sie dann **Speichern**.

    + `STORAGE_ACCOUNT_NAME`
    + `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    Der Zugriffsschlüssel des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können auf diesen Schlüssel von jedem beliebigen Serverskript zur Laufzeit zugreifen. Weitere Informationen finden Sie unter [App settings].

4. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei Web.config für das Projekt des mobilen Services, und fügen Sie die folgenden neuen App-Einstellungen hinzu, wobei Sie die Platzhalter durch den Speicherkontonamen und den Zugriffsschlüssel ersetzen, die als App-Einstellungen im Portal festgelegt sind:

        <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
        <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    Der mobile Service verwendet diese gespeicherten Einstellungen, wenn er auf dem lokalen Computer läuft, sodass Sie den Code testen können, bevor Sie ihn veröffentlichen. Bei Ausführung in Azure verwendet der mobile Service hingegen die im Portal festgelegten App-Einstellungswerte und ignoriert diese Projekteinstellungen. 

7.  Im Ordner, öffnen Sie die Datei TodoItemController.cs, und fügen Sie die folgenden **mit** Direktiven:

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Ersetzen Sie die vorhandene **PostTodoItem** -Methode durch den folgenden Code:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Diese POST-Methode generiert eine neue SAS für das eingefügte Element, die 5 Minuten lang gültig ist, und weist den Wert der generierten SAS der `sasQueryString`-Eigenschaft des zurückgegebenen Elements zu. Die `imageUri`-Eigenschaft wird auch auf den Ressourcenpfad des neuen BLOB festgelegt, um die Imageanzeige während der Bindung in der Client-UI zu ermöglichen.

    >[AZURE.NOTE] Dieser Code erstellt eine SAS für ein einzelnes BLOB. Wenn Sie mehrere Blobs in einen Container mit derselben SAS hochladen müssen, können Sie stattdessen Aufrufen der <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">GenerateSharedAccessSignature-Methode</a> mit einem leeren Blob-Ressourcennamen wie folgt: 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Als Nächstes aktualisieren Sie die Quickstart-App, um unter Verwendung der beim Einfügevorgang generierten SAS Funktionalität zum Hochladen von Images hinzuzufügen.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[How To Create a Storage Account]: ../articles/storage/storage-create-storage-account.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7


