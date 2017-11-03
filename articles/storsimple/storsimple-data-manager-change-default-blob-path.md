---
title: "Ändra blob sökväg från standardvärdet | Microsoft Docs"
description: "Lär dig hur du ställer in en Azure-funktion för att byta namn på en sökväg till filen blob (privat förhandsvisning)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Ändra en blobbsökvägen från standardsökvägen (privat förhandsvisning)

Den här artikeln beskriver hur du ställer in en Azure-funktion för att byta namn på en standardsökväg för blob. 

## <a name="prerequisites"></a>Krav

Se till att du har en jobbdefinition av som har konfigurerats på rätt sätt i en hybrid Dataresurs i en resursgrupp.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Om du vill skapa en Azure-funktion, gör du följande:

1. Gå till [Azure-portalen](http://portal.azure.com/).

2. Överst i det vänstra fönstret klickar du på **ny**. 

3. I den **Sök** skriver **Funktionsapp**, och tryck sedan på RETUR.

    ![Skriv ”Funktionsapp” i sökrutan](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. I den **resultat** klickar du på **Funktionsapp**.

    ![Välj funktionen app-resurs i resultatlistan](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    Den **Funktionsapp** öppnas.

5. Klicka på **Skapa**.

    ![Knappen Funktionsapp fönster ”skapa”](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. På den **Funktionsapp** configuration bladet gör du följande:

    a. I den **appnamn** skriver du namnet på appen.
    
    b. I den **prenumeration** skriver du namnet på prenumerationen.

    c. Under **resursgruppen**, klickar du på **Skapa nytt**, och skriv sedan namnet på resursgruppen.

    d. I den **värd planera** skriver **förbrukning planera**.

    e. I den **plats** skriver platsen.

    f. Under **lagringskonto**, Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Ett lagringskonto används internt för funktionen.

    ![Ange nya Funktionsapp konfigurationsdata](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Klicka på **Skapa**.  
    Funktionen appen skapas.

8. I den vänstra rutan klickar du på **fler tjänster**, och gör sedan följande:
    
    a. I den **Filter** skriver **apptjänster**.
    
    b. Klicka på **Apptjänster**. 

    ![”Fler tjänster” länken i den vänstra rutan](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. I listan över apptjänster, klickar du på namnet på funktionen appen.  
    Funktionen app-sidan öppnas.

10. I den vänstra rutan klickar du på **nya funktionen**, och gör sedan följande: 

    a. I den **språk** väljer **C#**.
    
    b. I matrisen med mallen paneler, Välj **QueueTrigger CSharp**.

    c. I den **namnge din funktion** Skriv ett namn för din funktion.

    d. I den **könamnet** skriver definition för DTS jobbnamn.

    e. Under **konto lagringsanslutning**, klickar du på **nya**, och välj det konto som motsvarar DTS jobbet.  
        Anteckna namnet på anslutningen. Namnet krävs senare i Azure-funktion.

       ![Skapa en ny C#-funktion](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Klicka på **Skapa**.  
    Den **funktionen** öppnas.

11. I den **funktionen** fönstret kör _.csx_ filen och gör sedan följande:

    a. Klistra in följande kod:

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

    b. Ersätt **STORAGE_CONNECTIONNAME** på rad 11 med anslutningens storage-konto (se punkt 8 c).

    c. Längst upp till vänster, klickar du på **spara**.

    ![Spara funktion](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Slutför funktionen genom att lägga till flera filer genom att göra följande:

    a. Klicka på **visa filer**.

       ![Länken ”Visa filer”](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Klicka på **Lägg till**.
    
    c. Typen **project.json**, och tryck sedan på RETUR.
    
    d. I den **project.json** fil, klistra in följande kod:

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

    e. Klicka på **Spara**.

Du har skapat en Azure-funktion. Den här funktionen aktiveras varje gång en ny blob genereras av DTS jobbet.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md)
