---
title: "Kom igång med blob storage och Visual Studio anslutna tjänster (ASP.NET Core) | Microsoft Docs"
description: "Hur du kommer igång med Azure Blob storage i ett projekt i Visual Studio ASP.NET Core när du har skapat ett lagringskonto med hjälp av Visual Studio anslutna tjänster"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 2e8060b44c395ad7c24e7778c0ef65148a3a45de
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob storage och Visual Studio anslutna tjänster (ASP.NET kärnor)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Blob storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ASP.NET Core-projekt med hjälp av dialogrutan Visual Studio Lägg till anslutna tjänster.

Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda blob kan vara valfri storlek. Blobbar kan vara till exempel bilder, ljud- och bildfiler, rådata och dokumentfiler. Den här artikeln beskriver hur du kommer igång med blob storage när du har skapat ett Azure storage-konto med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan i ASP.NET Core-projekt.

Precis som filer live i mappar, live storage-blobbar i behållare. När du har skapat en lagringsenhet, skapar du en eller flera behållare i lagringen. Till exempel i en lagring som kallas ”klippboken”, kan du skapa behållare i lagringen som kallas ”bilder” för att lagra bilder och en annan som heter ”ljud” för att lagra ljudfiler. Du kan ladda upp enskilda blob-filer till dem när du har skapat behållarna. Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) mer information om programmässigt manipulering blobbar.

## <a name="access-blob-containers-in-code"></a>Åtkomst till blob-behållare i koden
Om du vill komma åt blobar i ASP.NET Core projekt, måste du lägga till följande objekt om de inte redan finns.

1. Lägg till följande kod namnrymdsdeklarationer överst i en C#-fil som du vill komma åt Azure-lagring.
   
        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;
2. Hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta dina anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfigurationen.
   
         CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
            "<storage-account-name>",
            "<access-key>"), true);
   
    **Obs:** använda alla koden ovan framför koden i följande avsnitt.
3. Använd en **CloudBlobClient** objekt för att hämta en **CloudBlobContainer** referens till en befintlig behållare på ditt lagringskonto.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

## <a name="create-a-container-in-code"></a>Skapa en behållare i koden
Du kan också använda den **CloudBlobClient** att skapa en behållare på ditt lagringskonto. Allt du behöver göra är att lägga till ett anrop till **CreateIfNotExistsAsync** enligt följande:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "my-new-container."
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


**Obs:** API: er som utför anrop till Azure-lagring i ASP.NET Core är asynkron. Se [asynkron programmering med Async och Await](http://msdn.microsoft.com/library/hh191443.aspx) för mer information. Koden nedan förutsätter asynkrona programming metoder som används.

Du kan ange att behållaren ska vara offentlig med hjälp av följande kod för att göra filerna i behållaren ska vara tillgänglig för alla.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Hämta en referens för behållaren och använda den för att hämta en blobbreferens om du vill överföra en blob-fil till en behållare. När du har en blobbreferens kan du ladda upp en dataström till den genom att anropa den **UploadFromStreamAsync** metod. Den här åtgärden skapas blobben om den inte redan har gjort det, eller skrivs över om den finns. Följande exempel visar hur du laddar upp en blobb till en behållare och förutsätter att behållaren redan hade skapats.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa blobbar i en behållare börjar du med att hämta en referens för behållaren. Du kan sedan anropa behållarens **ListBlobsSegmentedAsync** metod för att hämta blobbarna och/eller katalogerna i den. För att komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **IListBlobItem**-metod måste du skicka den till ett **CloudBlockBlob**-, **CloudPageBlob**- eller **CloudBlobDirectory**-objekt. Om du inte vet blob-datatyp, kan du använda en typkontroll för att fastställa som du vill skicka den till. Följande kod visar hur du hämtar och returnerar URI: N för varje objekt i en behållare.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
            }

            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob pageBlob = (CloudPageBlob)item;

                Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
            }

            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory directory = (CloudBlobDirectory)item;

                Console.WriteLine("Directory: {0}", directory.Uri);
            }
        }
    } while (token != null);

Det finns andra sätt att visa innehållet i en blob-behållare. Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) för mer information.

## <a name="download-a-blob"></a>Ladda ned en blob
Om du vill hämta en blob först hämta en referens till blobben och sedan anropa den **DownloadToStreamAsync** metod. I följande exempel används den **DownloadToStreamAsync** metod för att överföra blobbinnehållet till ett stream-objekt som du sedan kan spara som en lokal fil.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named "myfile".
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Det finns andra sätt att spara blobbar som filer. Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) för mer information.

## <a name="delete-a-blob"></a>Ta bort en blob
Om du vill ta bort en blobb först hämta en referens till blobben och sedan anropa den **DeleteAsync** metod på den.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

