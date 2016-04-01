Sie können Azure Storage-Warteschlangen erstellen, indem Sie mithilfe von Visual Studio **Server-Explorer**.

![Server-Explorer-BLOBs][Image1]

1. Auf der **Ansicht** Menü wählen **Server-Explorer**.
2. Erweitern Sie im Server-Explorer den **Azure** Knoten für Ihr Abonnement, erweitern Sie die **Speicher** Knoten und den Knoten für das Speicherkonto im Azure-Speicher angegebene verbundenen Dienst.
3. Wählen Sie die **Warteschlangen** Knoten, und wählen Sie **Create Queue** aus dem Kontextmenü.
4. Geben Sie einen Namen für den Container, und wählen Sie **OK**.   

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn Sie die Dateien im Container öffentlich machen möchten, wählen Sie den Container in **Server-Explorer** und drücken Sie die `F4` zum Anzeigen der **Eigenschaften** Fenster. Legen Sie die **öffentlichen Lesezugriff auf** zu **Blob**. Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

