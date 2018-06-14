---
title: Ändra blob sökväg från standardvärdet | Microsoft Docs
description: Lär dig hur du ställer in en Azure-funktion för att byta namn på en blob-filsökväg
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862408"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Ändra en blobbsökvägen från standardsökvägen

När StorSimple Data Manager-tjänsten överför informationen, som standard placeras omvandlade blobbar i en lagringsbehållare som anges under genereringen av måldatabasen. När blobar anländer på denna plats, kanske du vill flytta dessa BLOB till en alternativ plats. Den här artikeln beskriver hur du ställer in en Azure-funktion att byta namn på en standardsökväg för blob och därför flytta blobar till en annan plats.

## <a name="prerequisites"></a>Förutsättningar

Se till att du har en korrekt konfigurerad jobbdefinitionen i StorSimple Data Manager-tjänsten.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Utför följande steg för att skapa en Azure-funktion:

1. Gå till [Azure-portalen](http://portal.azure.com/).

2. Klicka på **+ skapa en resurs för**. I den **Sök** skriver **Funktionsapp** och tryck på **RETUR**. Välj och klicka på **funktionsapp** i listan över appar som visas.

    ![Skriv ”Funktionsapp” i sökrutan](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klicka på **Skapa**.

    ![Knappen Funktionsapp fönster ”skapa”](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. På den **Funktionsapp** configuration-bladet, utför följande steg:

    1. Ange ett unikt **appnamn**.
    2. I listrutan väljer du den **prenumeration**. Den här prenumerationen ska vara samma som den som är kopplade till StorSimple Data Manager-tjänsten.
    3. Välj **Skapa nytt** resursgruppen.
    4. För den **värd planera** listrutan, Välj **förbrukning planera**.
    5. Ange en plats där din funktion körs. Vill du samma region där StorSimple Data Manager-tjänsten och lagringskontot som är associerade med jobbdefinitionen finns.
    6. Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Ett lagringskonto används internt för funktionen.

        ![Ange nya Funktionsapp konfigurationsdata](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klicka på **Skapa**. Funktionen appen skapas.
     
        ![Funktionsapp skapas](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Välj **funktioner**, och klicka på **+ nya funktionen**.

    ![Klicka på + ny funktion](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Välj **C#** för språket. I matrisen med mallen paneler, Välj **C#** i den **QueueTrigger CSharp** panelen.

7. I den **kö utlösaren**:

    1. Ange en **namn** för din funktion.
    2. I den **könamnet** skriver du namnet på data transformation jobbet definition.
    3. Under **konto lagringsanslutning**, klickar du på **nya**. Välj det konto som är associerade med din jobbdefinitionen från listan över storage-konton. Anteckna namnet på anslutningen (markerat). Namnet krävs senare i Azure-funktion.

        ![Skapa en ny C#-funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klicka på **Skapa**. Den **funktionen** skapas.

     
10. I fönstret funktionen kör _.csx_ fil.

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

    2. Ersätt **STORAGE_CONNECTIONNAME** på rad 11 med anslutningens storage-konto (se steg 7 c).

        ![Kopiera lagring anslutningsnamn](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Spara** funktionen.

        ![Spara funktion](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Slutför funktionen genom att lägga till flera filer genom att göra följande:

    1. Klicka på **visa filer**.

       ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klicka på **+ Lägg till**.
        
        ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typen **project.json**, och tryck sedan på **RETUR**. I den **project.json** fil, klistra in följande kod:

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

        ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Du har skapat en Azure-funktion. Den här funktionen aktiveras varje gång en ny blob genereras av data transformation jobb.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md)
