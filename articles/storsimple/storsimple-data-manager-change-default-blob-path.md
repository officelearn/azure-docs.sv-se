---
title: Ändra blob-sökväg från standardsökvägen
description: Lär dig hur du konfigurerar en Azure-funktion för att byta namn på en blob-filsökväg
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270632"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Ändra en blob-bana från standardsökvägen

När Tjänsten StorSimple Data Manager omvandlar data placeras som standard de transformerade blobbar i en lagringsbehållare som anges när måldatabasen skapades. När blobbar anländer till den här platsen kanske du vill flytta dessa blobbar till en annan plats. I den här artikeln beskrivs hur du konfigurerar en Azure-funktion för att byta namn på en standardsökväg för blob-fil och därmed flytta blobbar till en annan plats.

## <a name="prerequisites"></a>Krav

Kontrollera att du har en korrekt konfigurerad jobbdefinition i Tjänsten StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Så här skapar du en Azure-funktion:

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Klicka på **+ Skapa en resurs**. Skriv **Funktionsapp** i rutan **Sök** och tryck på **Retur**. Markera och klicka på **Funktionsapp** i listan över appar som visas.

    ![Skriv "Funktionsapp" i sökrutan](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klicka på **Skapa**.

    ![Knappen Skapa funktionsapp](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Gör följande på konfigurationsbladet **för funktionsapp:**

    1. Ange ett unikt **appnamn**.
    2. Välj **prenumerationen**i listrutan . Den här prenumerationen ska vara samma som den som är associerad med Tjänsten StorSimple Data Manager.
    3. Välj **Skapa ny** resursgrupp.
    4. Välj **Förbrukningsplan för**listrutan **Värdplan** .
    5. Ange en plats där funktionen körs. Du vill att samma region där StorSimple Data Manager-tjänsten och lagringskontot som är associerade med jobbdefinitionen finns.
    6. Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Ett lagringskonto används internt för funktionen.

        ![Ange nya konfigurationsdata för funktionsappar](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klicka på **Skapa**. Funktionsappen skapas.
     
        ![Funktionsapp skapad](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Välj **Funktioner**och klicka på **+ Ny funktion**.

    ![Klicka på + Ny funktion](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Välj **C#** för språket. I matrisen med mallpaneler väljer du **C#** i **panelen QueueTrigger-CSharp.**

7. I **köutlösaren:**

    1. Ange ett **namn** för din funktion.
    2. Skriv ditt jobbdefinitionsnamn för dataomvandlingsjobb i rutan **Könamn.**
    3. Klicka på **ny**under **Lagringskontoanslutning**. Välj det konto som är kopplat till jobbdefinitionen i listan över lagringskonton. Anteckna anslutningsnamnet (markerat). Namnet krävs senare i Azure-funktionen.

        ![Skapa en ny C#-funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klicka på **Skapa**. **Funktionen** skapas.

     
10. Kör _CSX-filen i_ funktionsfönstret.

    ![Skapa en ny C#-funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Utför följande steg.

    1. Klistra in följande kod:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Ersätt **STORAGE_CONNECTIONNAME** på rad 11 med din lagringskontoanslutning (se steg 7c).

        ![Kopiera namn på lagringsanslutning](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Spara** funktionen.

        ![Spara funktion](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Om du vill slutföra funktionen lägger du till ytterligare en fil genom att göra följande steg:

    1. Klicka på **Visa filer**.

       ![Länken "Visa filer"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klicka på **+ Lägg till**.
        
        ![Länken "Visa filer"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Skriv **project.json**och tryck sedan på **Retur**. Klistra in följande kod i **filen project.json:**

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klicka på **Spara**.

        ![Länken "Visa filer"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Du har skapat en Azure-funktion. Den här funktionen utlöses varje gång en ny blob genereras av dataomvandlingsjobbet.

## <a name="next-steps"></a>Nästa steg

[Använda StorSimple Data Manager-användargränssnittet för att omvandla dina data](storsimple-data-manager-ui.md)
