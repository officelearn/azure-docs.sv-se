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
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011631"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Ändra sökväg till en blob från standardsökvägen

När tjänsten StorSimple Data Manager omvandlar data, som standard placeras transformerade blobbar i en lagringsbehållare som anges under genereringen av måldatabasen. När blobarna som tas emot på den här platsen, kan du flytta dessa blobar till en annan plats. Den här artikeln beskriver hur du ställer in en Azure-funktion att byta namn på en filsökväg för standard-blob och kan därför flytta blobar till en annan plats.

## <a name="prerequisites"></a>Förutsättningar

Se till att du har en korrekt konfigurerad jobbdefinition i StorSimple Data Manager-tjänsten.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Utför följande steg för att skapa en Azure-funktion:

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Klicka på **+ skapa en resurs**. I den **Search** skriver **Funktionsapp** och tryck på **RETUR**. Välj och klicka på **funktionsapp** i listan över appar som visas.

    ![Skriv ”Function-App” i sökrutan](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klicka på **Skapa**.

    ![Knappen ”Skapa” Funktionsapp fönster](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. På den **Funktionsapp** konfigurationsbladet, utför följande steg:

    1. Ange ett unikt **appnamn**.
    2. Välj den nedrullningsbara listan den **prenumeration**. Den här prenumerationen ska vara samma som det som är associerade med StorSimple Data Manager-tjänsten.
    3. Välj **Skapa nytt** resursgrupp.
    4. För den **som är värd för planera** listrutan, väljer **Standardförbrukningsplanen**.
    5. Ange en plats där din funktion körs. Vill du samma region där StorSimple Data Manager-tjänsten och lagringskontot som associerats med jobbdefinitionen, finns.
    6. Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Ett lagringskonto används internt för funktionen.

        ![Ange ny Funktionsapp konfigurationsdata](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klicka på **Skapa**. Funktionsappen skapas.
     
        ![Funktionsappen skapas](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Välj **Functions**, och klicka på **+ ny funktion**.

    ![Klicka på + ny funktion](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Välj **C#** för språket. I matrisen med mallen paneler, Välj **C#** i den **QueueTrigger-CSharp** panelen.

7. I den **köutlösare**:

    1. Ange en **namn** för din funktion.
    2. I den **könamn** skriver du namnet på jobbdefinitionen dina data omvandling.
    3. Under **lagringskontoanslutning**, klickar du på **nya**. Välj det konto som är associerade med din jobbdefinition listan med lagringskonton. Anteckna anslutningens namn (markerat). Namnet måste senare i Azure-funktion.

        ![Skapa en ny C# funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klicka på **Skapa**. Den **funktionen** skapas.

     
10. I fönstret funktionen kör _.csx_ fil.

    ![Skapa en ny C# funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Ersätt **STORAGE_CONNECTIONNAME** på rad 11 med anslutning till ditt lagringskonto (se steg 7 c).

        ![Kopiera anslutningsnamn för lagring](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Spara** funktionen.

        ![Spara funktionen](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Lägg till en mer fil genom att göra följande steg för att slutföra funktionen:

    1. Klicka på **visa filer**.

       ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klicka på **+ Lägg till**.
        
        ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.json**, och tryck sedan på **RETUR**. I den **project.json** fil, klistra in följande kod:

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

Du har skapat en Azure-funktion. Den här funktionen utlöses varje gång en ny blob skapas av omvandlingsjobb.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI och omvandla data](storsimple-data-manager-ui.md)
