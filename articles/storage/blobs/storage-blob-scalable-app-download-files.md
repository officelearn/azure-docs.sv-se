---
title: "Hämta stora mängder slumpmässiga data från Azure Storage | Microsoft Docs"
description: "Lär dig hur du använder Azure SDK för att hämta stora mängder slumpmässiga data från ett Azure Storage-konto"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 46b0cf3666088175372b6a2e73b3dd421a4bff8b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Hämta stora mängder slumpmässiga data från Azure storage

Den här kursen ingår tre i en serie. Den här kursen visar hur du hämtar stora mängder data från Azure storage.

I del tre av serien får du lära dig hur du:

> [!div class="checklist"]
> * Uppdatera programmet
> * Köra programmet
> * Validera antalet anslutningar

## <a name="prerequisites"></a>Krav

Den här kursen du måste ha slutfört föregående lagring kursen: [överför stora datamängder slumpmässiga parallellt med Azure storage][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Fjärråtkomst till den virtuella datorn

 Om du vill skapa en fjärrskrivbords-session med den virtuella datorn använder du följande kommando på den lokala datorn. Ersätt IP-adress med publicIPAddress för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Uppdatera programmet

I föregående självstudierna överföra du bara filer till lagringskontot. Öppna `D:\git\storage-dotnet-perf-scale-app\Program.cs` i en textredigerare. Ersätt den `Main` metoden med följande exempel. Det här exemplet kommentarer ut aktiviteten överföringen och uncomments på download och aktiviteten Ta bort innehållet i lagringskontot när du är klar.

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
        // initially to support the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

När programmet har uppdaterats, måste du skapa programmet igen. Öppna en `Command Prompt` och navigera till `D:\git\storage-dotnet-perf-scale-app`. Återskapa programmet genom att köra `dotnet build` som visas i följande exempel:

```
dotnet build
```

## <a name="run-the-application"></a>Köra programmet

Nu när programmet har återskapats är det dags att köra programmet med den uppdaterade koden. Om det inte är redan öppen, öppnar du en `Command Prompt` och navigera till `D:\git\storage-dotnet-perf-scale-app`.

Typen `dotnet run` att köra programmet.

```
dotnet run
```

Programmet läser de behållare som finns i lagringskontot som anges i den **storageconnectionstring**. Den går igenom blobbar 10 på en gång med hjälp av den [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metod i behållare och hämtningsbara filer dem till lokalt datorn med hjälp av den [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metod.
I följande tabell visas de [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) som är definierade för varje blob som hämtas.

|Egenskap|Värde|Beskrivning|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Den här egenskapen inaktiverar kontrollerar MD5-hash för det innehåll som överförs. Inaktiverar MD5 validering producerar en snabbare överföring. Men inte bekräftar giltigheten eller integriteten för filer som överförs. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Den här egenskapen anger om en MD5-hash är beräknad och lagras.   |

Den `DownloadFilesAsync` aktiviteten visas i följande exempel:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
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

### <a name="validate-the-connections"></a>Validera anslutningar

När filerna laddas ned, kan du kontrollera antalet samtidiga anslutningar till ditt lagringskonto. Öppna en `Command Prompt` och skriv `netstat -a | find /c "blob:https"`. Det här kommandot visar antalet anslutningar som för närvarande öppnas med hjälp av `netstat`. I följande exempel visas en liknande utdata till vad som visas när du kör guiden dig själv. Som du ser i exemplet var över 280 anslutningar öppna när du hämtar slumpmässigt filer från lagringskontot.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Nästa steg

Del tre seriens du lärt dig om att hämta stora mängder slumpmässiga data från ett lagringskonto, till exempel att:

> [!div class="checklist"]
> * Köra programmet
> * Validera antalet anslutningar

Gå vidare till steg fyra i serien att verifiera genomflöde och svarstid mått i portalen.

> [!div class="nextstepaction"]
> [Kontrollera genomflöde och svarstid mått i portalen](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md