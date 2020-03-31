---
title: 'Snabbstart: Azure Blob storage library v12 - .NET'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage client library version 12 för .NET för att skapa en behållare och en blob i Blob (objekt) lagring. Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240508"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Snabbstart: Azure Blob storage client library v12 för .NET

Kom igång med Azure Blob storage-klientbiblioteket v12 för .NET. Azure Blob Storage är Microsofts objektlagringslösning för molnet. Följ stegen för att installera paketet och prova exempelkod för grundläggande uppgifter. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data.

> [!NOTE]
> Information om hur du kommer igång med den tidigare SDK-versionen finns i [Snabbstart: Azure Blob storage client library for .NET](storage-quickstart-blobs-dotnet-legacy.md).

Använd Azure Blob storage client library v12 for .NET för att:

* Skapa en container
* Ladda upp en blob till Azure Storage
* Lista alla blobbar i en behållare
* Ladda ned blobben till din lokala dator
* Ta bort en container

[API-referensdokumentation](/dotnet/api/azure.storage.blobs) | [Biblioteksnamnpaket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure storage-konto - [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuell [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Var noga med att få SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

I det här avsnittet beskrivs förbereder du ett projekt för att arbeta med Azure Blob storage-klientbiblioteket v12 för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett .NET Core-program med namnet *BlobQuickstartV12*.

1. I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du kommandot för att skapa en ny konsolapp med namnet *BlobQuickstartV12*. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Växla till den nyskapade *BlobQuickstartV12-katalogen.*

   ```console
   cd BlobQuickstartV12
   ```

1. Skapa en annan katalog som kallas *data*på sidan *BlobQuickstartV12.* Det är här blob-datafilerna kommer att skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Blob-lagringsklientbiblioteket för .NET-paketet `dotnet add package` medan du fortfarande är i programkatalogen.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna *Program.cs-filen* i redigeraren
1. Ta `Console.WriteLine("Hello World!");` bort utdraget
1. Lägga `using` till direktiv
1. Uppdatera `Main` metoddeklarationen för att stödja asynkron kod

Här är koden:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` Med klassen kan du manipulera Azure Storage-resurser och blob-behållare.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` Med klassen kan du manipulera Azure Storage-behållare och deras blobbar.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` Klassen låter dig manipulera Azure Storage blobbar.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` Klassen representerar de egenskaper och innehåll som returneras från nedladdning av en blob.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitten hur du utför följande med Azure Blob storage-klientbiblioteket för .NET:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en behållare](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa en lista över blobarna i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `Main` här koden i metoden:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya behållaren. Koden nedan lägger till ett GUID-värde i behållarnamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Anropa sedan metoden [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) för att skapa behållaren i ditt lagringskonto.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodavsnitt:

1. Skapar en textfil i *data* den lokala datakatalogen.
1. Hämtar en referens till ett [BlobClient-objekt](/dotnet/api/azure.storage.blobs.blobclient) genom att anropa [metoden GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) på behållaren från avsnittet [Skapa en behållare.](#create-a-container)
1. Överför den lokala textfilen till blobben genom att anropa [metoden UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobbar i behållaren genom att anropa [metoden GetBlobsAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) I det här fallet har bara en blob lagts till i behållaren, så listningsåtgärden returnerar bara den bloben.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta den tidigare skapade blobben genom att anropa [metoden DownloadAsync.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) Exempelkoden lägger till ett suffix med "NEDLADDAT" i filnamnet så att du kan se båda filerna i det lokala filsystemet.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen har skapat genom att ta bort hela behållaren med Hjälp av [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). De lokala filer som skapas av appen tas också bort.

Appen pausar för användarindata genom att anropa `Console.ReadLine` innan blobben, behållaren och de lokala filerna tas bort. Detta är en bra chans att kontrollera att resurserna faktiskt skapades korrekt, innan de tas bort.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i din lokala *datamapp* och överför den till Blob-lagring. I exemplet visas sedan blobbar i behållaren och hämtar filen med ett nytt namn så att du kan jämföra gamla och nya filer.

Navigera till programkatalogen och skapa och kör programmet.

```console
dotnet build
```

```console
dotnet run
```

Utdata för appen liknar följande exempel:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensningsprocessen *data* kontrollerar du datamappen efter de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **Retur för** att ta bort testfilerna och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Om du vill visa exempelappar för Blob-lagring fortsätter du att:

> [!div class="nextstepaction"]
> [SDK för Azure Blob-lagring v12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* För självstudier, exempel, snabbstarter och annan dokumentation, besök [Azure för .NET- och .NET Core-utvecklare](/dotnet/azure/).
* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
