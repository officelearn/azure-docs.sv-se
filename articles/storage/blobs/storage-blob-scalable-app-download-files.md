---
title: Ladda ned stora mängder slumpmässiga data från Azure Storage  | Microsoft Docs
description: Lär dig hur du använder Azure SDK för att ladda ned stora mängder slumpmässiga data från ett Azure Storage-konto
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 180e38146911b15e95828bf033cbcaefae22d094
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241397"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Ladda ned stora mängder slumpmässiga data från Azure Storage

Den här självstudiekursen är den tredje delen i en serie. Den här kursen visar hur du laddar ned stora mängder data från Azure Storage.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet
> * Köra programmet
> * Verifiera antalet anslutningar

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien måste du ha slutfört den tidigare Storage-kursen: [Överföra stora mängder slumpmässiga data parallellt till Azure Storage][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Fjärranslut till din virtuella dator

 Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med publicIPAddress för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Uppdatera programmet

I föregående kurs överförde du bara filer till lagringskontot. Öppna `D:\git\storage-dotnet-perf-scale-app\Program.cs` i en textredigerare. Ersätt metoden `Main` med följande exempel. Det här exemplet kommentarer ut överföringsaktiviteten och tar bort kommentarstecknen för nedladdningsaktiviteten och aktiviteten för att ta bort innehåll i lagringskontot vid slutförandet.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initially
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

När programmet har uppdaterats måste du bygga programmet på nytt. Öppna en `Command Prompt` och navigera till `D:\git\storage-dotnet-perf-scale-app`. Bygg om programmet genom att köra `dotnet build` som i följande exempel:

```
dotnet build
```

## <a name="run-the-application"></a>Köra programmet

Nu när programmet har byggts om är det dags att köra programmet med den uppdaterade koden. Öppna en `Command Prompt`, om den inte redan är öppen, och navigera till `D:\git\storage-dotnet-perf-scale-app`.

Skriv `dotnet run` för att köra programmet.

```
dotnet run
```

Programmet läser de containrar i lagringskontot som anges i **storageconnectionstring**. Den går igenom 10 blobar åt gången med hjälp av metoden [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) i containrarna och laddar ned dem till den lokala datorn med hjälp av metoden [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_).
I följande tabell visas de [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) som har definierats för varje blob när den hämtas.

|Egenskap|Värde|Beskrivning|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Den här egenskapen inaktiverar kontrollen av MD5-hashen för innehållet som har överförts. Överföringen går snabbare om MD5-verifieringen inaktiveras. Däremot bekräftas inte giltigheten eller integriteten för de filer som överförs. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Den här egenskapen anger om en MD5-hash beräknas och sparas.   |

Aktiviteten `DownloadFilesAsync` visas i följande exempel:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>Verifiera anslutningarna

När filerna laddas ned kan du verifiera antalet samtidiga anslutningar till lagringskontot. Öppna en `Command Prompt` och skriv `netstat -a | find /c "blob:https"`. Det här kommandot visar antalet anslutningar som för närvarande öppnas med hjälp av `netstat`. Följande exempel visar utdata som ser ut ungefär som när du kör kursen själv. Som du ser i exemplet var mer än 280 anslutningar öppna när de slumpmässiga filerna laddades ned från lagringskontot.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Nästa steg

I den tredje delen i serien lärde du dig hur du laddar ned stora mängder slumpmässiga data från ett lagringskonto. Du fick till exempel veta hur du gör för att:

> [!div class="checklist"]
> * Köra programmet
> * Verifiera antalet anslutningar

Gå vidare till den fjärde delen i serien och lär dig hur du kontrollerar mått för dataflöde och svarstid i Portal.

> [!div class="nextstepaction"]
> [Verifiera mått för dataflöde och svarstid i Portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
