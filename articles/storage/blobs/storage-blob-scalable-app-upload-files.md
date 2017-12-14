---
title: "Överför stora datamängder slumpmässiga parallellt till Azure Storage | Microsoft Docs"
description: "Lär dig hur du använder Azure SDK för att överföra stora mängder slumpmässiga data parallellt till ett Azure Storage-konto"
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
ms.openlocfilehash: c587a5fb23ad72b4a20475a83ad6ac4764717c42
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Överför stora datamängder slumpmässiga parallellt till Azure-lagring

Den här kursen ingår två i en serie. Den här kursen visar du distribuerar ett program som överför stora mängder slumpmässiga data till ett Azure storage-konto.

I del två av serien får du lära dig hur du:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Köra programmet
> * Validera antalet anslutningar

Azure blob storage är en skalbar tjänst för att lagra dina data. Att säkerställa ditt program är performant som möjligt, en förståelse av hur fungerar för blob storage rekommenderas. Kunskap om gränser för Azure BLOB är viktigt, att lära dig mer om dessa begränsningar finns: [blob storage skalbarhetsmål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Partitionen naming](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) är en annan viktig faktor när du skapar en hög prestanda tillämpning med BLOB. Azure storage använder ett intervall-baserade partitioneringsschema skalning och inläsning balans. Den här konfigurationen innebär att filer med liknande namnkonventioner eller prefix går till samma partition. Den här logiken innehåller namnet på behållaren som filerna som överförs till. I den här kursen använder du filer GUID för namn som bra som slumpmässigt genererat innehåll. De överförs sedan till fem olika behållare med slumpmässiga namn.

## <a name="prerequisites"></a>Krav

Den här kursen du måste ha slutfört föregående lagring kursen: [skapa en virtuell dator och storage-konto för ett skalbart program][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Fjärråtkomst till den virtuella datorn

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbords-session med den virtuella datorn. Ersätt IP-adress med publicIPAddress för den virtuella datorn. När du uppmanas ange de autentiseringsuppgifter som du använde när du skapar den virtuella datorn.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurera anslutningssträngen

Navigera till ditt lagringskonto i Azure-portalen. Välj **åtkomstnycklar** under **inställningar** i ditt lagringskonto. Kopiera den **anslutningssträngen** från de primära och sekundära nycklarna. Logga in på den virtuella datorn som du skapade i föregående kursen. Öppna en **kommandotolk** som administratör och kör den `setx` kommandot med de `/m` växel, sparar det här kommandot en miljövariabel för inställning av datorn. Miljövariabeln är inte tillgängligt förrän du ladda den **kommandotolk**. Ersätt  **\<storageConnectionString\>**  i följande exempel:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

När du är klar öppnar du en annan **kommandotolk**, gå till `D:\git\storage-dotnet-perf-scale-app` och skriv `dotnet build` att skapa programmet.

## <a name="run-the-application"></a>Köra programmet

Navigera till `D:\git\storage-dotnet-perf-scale-app`.

Typen `dotnet run` att köra programmet. Första gången du kör `dotnet` fylls i din lokala paketcachen för att återställa snabbare och aktivera offline-åtkomst. Det här kommandot tar upp till en minut att slutföra och inträffar endast en gång.

```
dotnet run
```

Programmet skapar fem slumpmässigt namn behållare och börjar överföringen av filer i den tillfälliga katalogen till lagringskontot. Programmet anger minsta trådar och 100 och [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) och 100 för att säkerställa att ett stort antal samtidiga anslutningar tillåts när du kör programmet.

Förutom att konfigurera inställningarna threading och anslutningen gränsen på [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) för den [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metoden är konfigurerade att använda parallellitet och inaktivera MD5 hash-verifieringen. Filerna har överförts i block med 100 mb, den här konfigurationen ger bättre prestanda, men kan vara kostsamma om du använder ett dåligt utför nätverk som om det uppstår ett fel 100 mb hela blocket försöks.

|Egenskap|Värde|Beskrivning|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| Inställningen delar blob i block när du laddar upp. Det här värdet ska vara 8 gånger antalet kärnor för högsta prestanda. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Den här egenskapen inaktiverar kontrollerar MD5-hash för det innehåll som överförs. Inaktiverar MD5 validering producerar en snabbare överföring. Men inte bekräftar giltigheten eller integriteten för filer som överförs.   |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Den här egenskapen anger om en MD5-hash beräknas och sparas med filen.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 2 sekunder backoff med 10 högsta antal försök |Anger återförsöksprincip begäranden. I det här exemplet är anslutningsfel igen en [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) principen är konfigurerad med en 2-sekund backoff och en högsta antal försök 10. Den här inställningen är viktigt när programmet hämtar nära träffa den [blob storage skalbarhetsmål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

Den `UploadFilesAsync` aktiviteten visas i följande exempel:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directiory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestionOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

I följande exempel visas ett trunkerat programmet körs på ett Windows-system.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directiory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Validera anslutningar

När filer överförs kan du kontrollera antalet samtidiga anslutningar till ditt lagringskonto. Öppna en **kommandotolk** och skriv `netstat -a | find /c "blob:https"`. Det här kommandot visar antalet anslutningar som för närvarande öppnas med hjälp av `netstat`. I följande exempel visas en liknande utdata till vad som visas när du kör guiden dig själv. Som du ser i exemplet var 800 anslutningar öppna när slumpmässiga filer skickas till lagringskontot. Det här värdet ändras i hela kör överföringen. Genom att överföra i parallellt block segment minskar avsevärt mängden tid som krävs för att överföra innehållet.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Nästa steg

Del två seriens du lärt dig om hur du överför stora mängder slumpmässiga data till ett lagringskonto parallellt, till exempel att:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Köra programmet
> * Validera antalet anslutningar

Gå vidare till avsnitt tre seriens att hämta stora mängder data från ett lagringskonto.

> [!div class="nextstepaction"]
> [Överför stora mängder av stora filer parallellt till ett lagringskonto](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md