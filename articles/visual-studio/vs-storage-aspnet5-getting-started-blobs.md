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
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: afd73bd0fd041a53fbe31aa3a5c23b3e27d7a9ec
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob storage och Visual Studio anslutna tjänster (ASP.NET kärnor)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Den här artikeln beskriver hur du kommer igång med Azure Blob storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ASP.NET Core-projekt med hjälp av Visual Studio **anslutna tjänster** funktion. Den **anslutna tjänster** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure-lagring i ditt projekt och lägger till anslutningssträngen för lagringskontot konfigurationsfilerna projektet. (Se [Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.)

Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda blob kan vara valfri storlek. Blobbar kan vara till exempel bilder, ljud- och bildfiler, rådata och dokumentfiler. Den här artikeln beskriver hur du kommer igång med blob storage när du har skapat ett Azure storage-konto med hjälp av Visual Studio **Connected Services** i ASP.NET Core-projekt.

Precis som filer live i mappar, live storage-blobbar i behållare. När du har skapat en blobb skapar du en eller flera behållare i blobben. Till exempel i en blob som kallas ”klippboken” du kan skapa behållare som kallas ”bilder” för att lagra bilder och en annan som heter ”ljud” för att lagra ljudfiler. När du har skapat behållarna som kan du överföra enskilda filer till dem. Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) mer information om programmässigt manipulering blobbar.

Vissa av Azure Storage-API: er är asynkron och koden i den här artikeln förutsätter asynkrona metoder som används. Se [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) för mer information.

## <a name="access-blob-containers-in-code"></a>Åtkomst till blob-behållare i koden

Om du vill komma åt blobar i ASP.NET Core projekt, måste du lägga till följande kod om den inte redan finns:

1. Lägg till nödvändiga `using` instruktioner:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Hämta en `CloudStorageAccount` objekt som representerar din kontoinformation för lagring. Använd följande kod för att hämta din anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Använd en `CloudBlobClient` objekt för att hämta en `CloudBlobContainer` referens till en befintlig behållare på ditt lagringskonto:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Skapa en behållare i koden

Du kan också använda den `CloudBlobClient` att skapa en behållare på ditt lagringskonto genom att anropa `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Ange att behållaren ska vara offentlig om du vill göra filerna i behållaren ska vara tillgänglig för alla:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare

Hämta en referens för behållaren och använda den för att hämta en blobbreferens om du vill överföra en blob-fil till en behållare. Sedan ladda upp en dataström som referens när du anropar den `UploadFromStreamAsync` metoden. Den här åtgärden skapas blobben om den inte redan har gjort det, och skriver över en befintlig blob. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Om du vill visa blobbar i en behållare, första hämta en referens för behållaren anropa sedan dess `ListBlobsSegmentedAsync` metod för att hämta blobbarna och/eller katalogerna i den. Att komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad `IListBlobItem`, skicka den till en `CloudBlockBlob`, `CloudPageBlob`, eller `CloudBlobDirectory` objekt. Om du inte vet blob-datatyp, kan du använda en typkontroll för att fastställa vilken den ska skickas.

```cs
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
```

Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) för andra sätt att visa innehållet i en blob-behållare.

## <a name="download-a-blob"></a>Ladda ned en blob

Om du vill hämta en blob, första hämta en referens till blob anropa den `DownloadToStreamAsync` metoden. I följande exempel används den `DownloadToStreamAsync` metod för att överföra blobbinnehållet till ett stream-objekt som du sedan kan spara som en lokal fil.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Se [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) för andra sätt att spara blobbar som filer.

## <a name="delete-a-blob"></a>Ta bort en blob

Om du vill ta bort en blob, första hämta en referens till blob anropa den `DeleteAsync` metoden:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
