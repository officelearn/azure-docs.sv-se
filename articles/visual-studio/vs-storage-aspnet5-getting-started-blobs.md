---
title: Kom igång med blob storage och Visual Studio-anslutna tjänster (ASP.NET Core) | Microsoft Docs
description: Hur du kommer igång med Azure Blob-lagring i ett Visual Studio ASP.NET Core-projekt när du har skapat ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 388c4d5f28e87f5cfe26336771d30fa44c6f9ef0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123016"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob storage och Visual Studio-anslutna tjänster (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Den här artikeln beskrivs hur du kommer igång med Azure Blob-lagring i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ett ASP.NET Core-projekt med hjälp av Visual Studio **Connected Services** funktionen. Den **Connected Services** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure storage i ditt projekt och lägger till anslutningssträngen för lagringskontot i konfigurationsfilerna projekt. (Se [dokumentation om Storage](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.)

Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda blob kan vara valfri storlek. Blobar kan vara saker som bilder, ljud- och bildfiler, rådata och dokumentfiler. Den här artikeln beskrivs hur du kommer igång med blob-lagring när du har skapat ett Azure storage-konto med hjälp av Visual Studio **Connected Services** i ett ASP.NET Core-projekt.

Precis som filer live i mappar, lagringsblobar för Direktmigrering i behållare. När du har skapat en blob, skapar du en eller flera behållare i blobben. Till exempel i en blob som kallas ”klippboken”, kan du skapa behållare som kallas ”avbildningar” för att lagra bilder och en annan kallas ”ljud” för att lagra ljudfiler. Du kan ladda upp enskilda filer till dem när du har skapat behållarna. Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) för mer information om hur du programmässigt hantera BLOB-objekt.

Några av de API: erna för Azure Storage är asynkrona och koden i den här artikeln förutsätter att asynkrona metoder som används. Se [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) för mer information.

## <a name="access-blob-containers-in-code"></a>Åtkomst till blob-behållare i kod

För att programmässigt få åtkomst till blobar i ASP.NET Core-projekt, måste du lägga till följande kod om det inte redan finns:

1. Lägg till nödvändiga `using` instruktioner:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Hämta en `CloudStorageAccount` objekt som representerar information på lagringskontot. Använd följande kod för att hämta din anslutningssträng för lagring och lagringskontoinformation från konfigurationen i Azure service:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Använd en `CloudBlobClient` objekt för att hämta en `CloudBlobContainer` referens till en befintlig behållare i ditt storage-konto:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Skapa en behållare i kod

Du kan också använda den `CloudBlobClient` att skapa en behållare i ditt storage-konto genom att anropa `CreateIfNotExistsAsync`:

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

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container

Hämta en referens för behållaren och använda den för att hämta en blobbreferens för att ladda upp en blob-fil till en behållare. Sedan ladda upp en dataström som refererar genom att anropa den `UploadFromStreamAsync` metoden. Den här åtgärden skapar bloben om den inte och skriver över en befintlig blob. 

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

Om du vill visa blobbar i en behållare, första hämta en referens för behållaren sedan anropa dess `ListBlobsSegmentedAsync` metod för att hämta blobbarna och/eller katalogerna i den. Komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad `IListBlobItem`, skicka den till en `CloudBlockBlob`, `CloudPageBlob`, eller `CloudBlobDirectory` objekt. Om du inte vet vilken blob, kan du använda en typkontroll för att fastställa som du vill skicka den till.

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

Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) för andra sätt att visa innehållet i en blob-behållare.

## <a name="download-a-blob"></a>Ladda ned en blob

Om du vill ladda ned en blob, första hämta en referens till bloben, sedan anropa den `DownloadToStreamAsync` metoden. I följande exempel används den `DownloadToStreamAsync` metod för att överföra blobbinnehållet till ett dataströmsobjekt som du kan spara som en lokal fil.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Gå till [Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) andra sätt att spara blobbar som filer.

## <a name="delete-a-blob"></a>Ta bort en blob

Om du vill ta bort en blob, första hämta en referens till bloben, sedan anropa den `DeleteAsync` metoden:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
