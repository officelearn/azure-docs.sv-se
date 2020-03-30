---
title: Komma igång med blob-lagring med Visual Studio (ASP.NET Core)
description: Komma igång med Azure Blob-lagring i ett Visual Studio-ASP.NET Core-projekt efter att du har skapat ett lagringskonto med Hjälp av Anslutna Visual Studio-tjänster
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298830"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Komma igång med Azure Blob storage och Visual Studio-anslutna tjänster (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

I den här artikeln beskrivs hur du kommer igång med Azure Blob-lagring i Visual Studio när du har skapat eller refererat till ett Azure-lagringskonto i ett ASP.NET Core-projekt med hjälp av Funktionen Visual Studio **Connected Services.** Åtgärden **Connected Services** installerar lämpliga NuGet-paket för att komma åt Azure-lagring i projektet och lägger till anslutningssträngen för lagringskontot i projektkonfigurationsfilerna. (Se [Lagringsdokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Azure Blob storage är en tjänst för lagring av stora mängder ostrukturerade data som kan nås var som helst i världen via HTTP eller HTTPS. En enda blob kan vara valfri storlek. Blobbar kan vara saker som bilder, ljud- och videofiler, rådata och dokumentfiler. I den här artikeln beskrivs hur du kommer igång med blob-lagring när du har skapat ett Azure-lagringskonto med hjälp av Visual Studio **Connected Services** i ett ASP.NET Core-projekt.

Precis som filer finns i mappar finns lagringsblobar i behållare. När du har skapat en blob skapar du en eller flera behållare i den blobben. I en blob som heter "Scrapbook" kan du till exempel skapa behållare som kallas "bilder" för att lagra bilder och en annan som kallas "ljud" för att lagra ljudfiler. När du har skapat behållarna kan du överföra enskilda filer till dem. Se [Snabbstart: Ladda upp, hämta och lista blobbar med .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) för mer information om programmatiskt manipulera blobbar.

Vissa azure storage API:er är asynkrona och koden i den här artikeln förutsätter att asynkrometoder används. Mer information [finns i Asynkron programmering.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-blob-containers-in-code"></a>Komma åt blob-behållare i kod

Om du vill använda programmatiskt åtkomst till blobbar i ASP.NET Core-projekt måste du lägga till följande kod om det inte redan finns:

1. Lägg till `using` nödvändiga satser:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Hämta `CloudStorageAccount` ett objekt som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträngen och lagringskontot från Azure-tjänstkonfigurationen:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Använd `CloudBlobClient` ett objekt `CloudBlobContainer` för att hämta en referens till en befintlig behållare i ditt lagringskonto:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Skapa en behållare i kod

Du kan också `CloudBlobClient` använda för att skapa en `CreateIfNotExistsAsync`behållare i ditt lagringskonto genom att ringa:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Om du vill göra filerna i behållaren tillgängliga för alla ställer du in behållaren så att den är offentlig:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container

Om du vill överföra en blob-fil till en behållare hämtar du en behållarreferens och använder den för att hämta en blob-referens. Ladda sedan upp alla dataström till `UploadFromStreamAsync` den referensen genom att anropa metoden. Den här åtgärden skapar bloben om den inte redan finns där och skriver över en befintlig blob. 

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

Om du vill visa blobbar i en behållare hämtar `ListBlobsSegmentedAsync` du först en behållarreferens och anropar sedan dess metod för att hämta blobbar och/eller kataloger i den. Om du vill komma åt den omfattande `IListBlobItem`uppsättningen egenskaper `CloudBlockBlob`och `CloudPageBlob`metoder `CloudBlobDirectory` för en returnerad castar du den till ett eller ett objekt. Om du inte känner till blob-typen använder du en typkontroll för att avgöra vilken du ska casta den till.

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

Se [Snabbstart: Ladda upp, hämta och lista blobbar med .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) för andra sätt att lista innehållet i en blob-behållare.

## <a name="download-a-blob"></a>Ladda ned en blob

Om du vill hämta en blob får du först `DownloadToStreamAsync` en referens till blobben och anropar sedan metoden. I följande exempel `DownloadToStreamAsync` används metoden för att överföra blob-innehållet till ett flödesobjekt som du sedan kan spara som en lokal fil.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Se [Snabbstart: Ladda upp, hämta och lista blobbar med .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) för andra sätt att spara blobbar som filer.

## <a name="delete-a-blob"></a>Ta bort en blob

Om du vill ta bort en blob får du `DeleteAsync` först en referens till blobben och anropar sedan metoden:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
