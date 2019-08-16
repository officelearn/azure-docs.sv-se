---
title: Kom igång med Blob Storage och anslutna Visual Studio-tjänster (ASP.NET Core) | Microsoft Docs
description: Komma igång med Azure Blob Storage i ett Visual Studio-ASP.NET Core projekt när du har skapat ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: c0f4124ffdb03a7a193791515444c4c7001378d7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511564"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob Storage och anslutna Visual Studio-tjänster (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Den här artikeln beskriver hur du kommer igång med Azure Blob Storage i Visual Studio när du har skapat eller refererat till ett Azure Storage-konto i ett ASP.NET Core projekt med hjälp av funktionen Visual Studio **Connected Services** . Åtgärden **anslutna tjänster** installerar rätt NuGet-paket för att komma åt Azure Storage i projektet och lägger till anslutnings strängen för lagrings kontot i dina projekt konfigurationsfiler. (Se [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda BLOB kan vara vilken storlek som helst. Blobbar kan vara saker som bilder, ljud-och videofiler, rå data och dokument-filer. Den här artikeln beskriver hur du kommer igång med Blob Storage när du har skapat ett Azure Storage-konto med hjälp av Visual Studio **Connected Services** i ett ASP.net Core-projekt.

Precis som filer Live i mappar, Storage blobbar i behållare. När du har skapat en BLOB skapar du en eller flera behållare i denna blob. I en blob med namnet "klipp bok" kan du till exempel skapa behållare som kallas "bilder" för att lagra bilder och en annan som kallas "ljud" för att lagra ljudfiler. När du har skapat behållarna kan du överföra enskilda filer till dem. Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med](../storage/blobs/storage-quickstart-blobs-dotnet.md) hjälp av .net om du vill ha mer information om program mässigt manipulera blobbar.

Några av de Azure Storage API: erna är asynkrona och koden i den här artikeln förutsätter att asynkrona metoder används. Mer information finns i [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-blob-containers-in-code"></a>Komma åt BLOB-behållare i kod

För att program mässigt få åtkomst till blobbar i ASP.NET Core-projekt måste du lägga till följande kod om den inte redan finns:

1. Lägg till de `using` nödvändiga instruktionerna:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Hämta ett `CloudStorageAccount` objekt som representerar lagrings konto informationen. Använd följande kod för att hämta information om lagrings anslutnings strängen och lagrings kontot från Azure-tjänst konfigurationen:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Använd ett `CloudBlobClient` objekt för att hämta `CloudBlobContainer` en referens till en befintlig behållare i ditt lagrings konto:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Skapa en behållare i kod

Du kan också använda `CloudBlobClient` för att skapa en behållare i ditt lagrings konto genom att anropa: `CreateIfNotExistsAsync`

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Om du vill göra filerna i behållaren tillgängliga för alla, anger du att behållaren ska vara offentlig:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container

Om du vill överföra en BLOB-fil till en behållare hämtar du en behållar referens och använder den för att hämta en BLOB-referens. Ladda sedan upp data strömmar till referensen genom att anropa `UploadFromStreamAsync` -metoden. Den här åtgärden skapar blobben om den inte redan finns där, och skriver över en befintlig blob. 

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

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Om du vill visa en lista över blobarna i en behållare, hämtar du först en behållar referens och anropar sedan dess `ListBlobsSegmentedAsync` metod för att hämta blobbar och/eller kataloger i den. För att få åtkomst till den omfattande uppsättningen med egenskaper och metoder `IListBlobItem`för en returnerad, `CloudBlockBlob`omvandla den till `CloudBlobDirectory` ett-, `CloudPageBlob`-eller-objekt. Om du inte känner till BLOB-typen använder du en typ kontroll för att avgöra vilken du vill omvandla den till.

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

Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) .net för andra sätt att visa innehållet i en BLOB-behållare.

## <a name="download-a-blob"></a>Ladda ned en blob

Hämta en BLOB genom att först hämta en referens till bloben och anropa `DownloadToStreamAsync` sedan metoden. I följande exempel används `DownloadToStreamAsync` metoden för att överföra BLOB-innehållet till ett Stream-objekt som du sedan kan spara som en lokal fil.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) .net för andra sätt att spara blobbar som filer.

## <a name="delete-a-blob"></a>Ta bort en blob

Om du vill ta bort en BLOB måste du först hämta en referens till bloben `DeleteAsync` och sedan anropa metoden:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
