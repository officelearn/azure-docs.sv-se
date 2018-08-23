---
title: Överföra filer till ett Media Services-konto med hjälp av .NET | Microsoft Docs
description: Lär dig hur du hämtar medieinnehåll till Media Services genom att skapa och ladda upp tillgångar.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: juliako
ms.openlocfilehash: 9edfa8ea0c9e469d09cef7ddbd1c7edda4484b47
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444637"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Överföra filer till ett Media Services-konto med hjälp av .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

I Media Services överför du (eller för in) dina digitala filer till en tillgång. Den **tillgången** entiteten kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och dold textning filer (och metadata om dessa filer.)  När filerna har överförts lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning.

Filerna i tillgången kallas **Tillgångsfiler**. Den **AssetFile** instans och den faktiska mediefilen finns två olika objekt. AssetFile-instans innehåller metadata om filen media medan mediefilen innehåller faktiskt medieinnehåll.

> [!NOTE]
> Följande gäller:
> 
> * Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför stöds procent-kodning inte. Värdet för den **namn** egenskapen får inte innehålla något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det får bara finnas ett '.' för filnamnstillägget.
> * Längden på namnet får inte vara större än 260 tecken.
> * Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.
> * Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).
> 

När du skapar resurser kan ange du följande alternativ för kryptering:

* **Ingen** – Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagring.
  Om du planerar att leverera en MP4 med progressivt nedladdning ska använda det här alternativet: 
* **CommonEncryption** – Använd det här alternativet om du överför innehåll som redan har krypterats och skyddats med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
* **EnvelopeEncrypted** – Använd det här alternativet om du överför HLS som krypterats med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.
* **StorageEncrypted** – krypterar innehållet lokalt med hjälp av AES-256-bitarskryptering och överför dem till Azure Storage var den lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
  
    Media Services tillhandahåller på disklagring kryptering för dina tillgångar, inte over-under överföring som Manager DRM (Digital Rights).
  
    Om tillgången är krypterad, måste du konfigurera tillgångsleveransprincip. Mer information finns i [konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

Om du anger för din tillgång krypteras med en **CommonEncrypted** alternativet, eller en **EnvelopeEncypted** alternativet, måste du koppla tillgången med en **ContentKey**. Mer information finns i [så här skapar du en ContentKey](media-services-dotnet-create-contentkey.md). 

Om du anger för din tillgång krypteras med en **StorageEncrypted** alternativ, Media Services SDK för .NET skapar en **StorateEncrypted** **ContentKey** för tillgången.

Den här artikeln visar hur du använder Media Services .NET SDK samt Media Services .NET SDK-tillägg för att överföra filer till en tillgång med Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Ladda upp en enstaka fil med Media Services .NET SDK
Följande kod använder .NET för att ladda upp en fil. AccessPolicy och positionerare skapas och förstörs av funktionen ladda upp. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Ladda upp flera filer med Media Services .NET SDK
Följande kod visar hur du skapar en tillgång och överföra flera filer.

Koden gör följande:

* Skapar en tom tillgång med metoden CreateEmptyAsset som definierats i föregående steg.
* Skapar en **AccessPolicy** -instans som definierar behörigheter och varaktighet för åtkomst till tillgången.
* Skapar en **positionerare** -instans som ger åtkomst till tillgången.
* Skapar en **BlobTransferClient** instans. Den här typen representerar en klient som körs på Azure-blobar. I det här exemplet övervakar klienten Överföringsförlopp. 
* Räknar upp igenom filerna i den angivna katalogen och skapar en **AssetFile** instans för varje fil.
* Överför filer till Media Services med hjälp av den **UploadAsync** metod. 

> [!NOTE]
> Använd metoden UploadAsync så att anrop inte blockerar och filerna överförs parallellt.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Tänk på följande när du överför ett stort antal tillgångar:

* Skapa en ny **CloudMediaContext** objekt per tråd. Den **CloudMediaContext** klassen är trådsäkra.
* Öka NumberOfConcurrentTransfers från standardvärdet 2 till ett högre värde som 5. Den här egenskapen påverkar alla förekomster av **CloudMediaContext**. 
* Behåll ParallelTransferThreadCount standardvärdet 10 stå.

## <a id="ingest_in_bulk"></a>Fört in tillgångar gruppvis med hjälp av Media Services .NET SDK
Ladda upp stora tillgångsfiler kan utgöra en flaskhals när tillgången skapas. Mata in tillgångar i massutskick eller ”Bulk inmatning” innebär att Frikoppling tillgången skapas från uppladdningen. Skapa ett manifest (IngestManifest) som beskriver tillgången och dess associerade filer om du vill använda en grupp som matar in metod. Sedan använda metoden upload föredrar för att överföra filerna till den manifest blob-behållare. Microsoft Azure Media Services bevakar blob-behållare som är associerade med manifestet. När en fil har överförts till blob-behållaren, har Microsoft Azure Media Services tillgången skapat baserat på konfigurationen av tillgången i manifestet (IngestManifestAsset).

Anropa metoden skapa som exponeras av IngestManifests samlingen på CloudMediaContext för att skapa en ny IngestManifest. Den här metoden skapar en ny IngestManifest med manifestnamnet som du anger.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Skapa de resurser som är associerade med stora IngestManifest. Konfigurera önskade krypteringsalternativen för tillgång för att föra in samtidigt.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

En IngestManifestAsset associerar en tillgång med en massinläsning IngestManifest för bulk mata in. Den associerar även AssetFiles som utgör varje tillgång. För att skapa en IngestManifestAsset, använder du metoden skapa på serverkontext.

I följande exempel visar att lägga till två nya IngestManifestAssets som associerar två tillgångar skapade tidigare till stora mata in manifestet. Varje IngestManifestAsset associerar även en uppsättning filer som har laddats upp för varje tillgång vid massinläsning mata in.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Du kan använda valfritt höghastighetsnätverk program kan ladda upp resursfiler till blob storage-behållare URI som tillhandahålls av den **IIngestManifest.BlobStorageUriForUpload** egenskapen för IngestManifest. 

Följande kod visar hur du använder .NET SDK för att ladda upp filer för tillgångar.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

I följande kodexempel visas koden för att ladda upp resursfiler för exemplet som används i den här artikeln:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Du kan se förloppet för bulk-inmatning för alla tillgångar som är associerade med en **IngestManifest** genom att avsöka egenskapen statistik för den **IngestManifest**. Om du vill uppdatera statusinformation, måste du använda en ny **CloudMediaContext** varje gång du avsöka egenskapen statistik.

Följande exempel visar att avsöka en IngestManifest av dess **Id**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Överföra filer med .NET SDK-tillägg
I följande exempel visas hur du överför en fil med hjälp av .NET SDK-tilläggen. I det här fallet den **CreateFromFile** metoden används, men den asynkrona versionen är också tillgängliga (**CreateFromFileAsync**). Den **CreateFromFile** metoden kan du ange namnet på filen, krypteringsalternativet och ett återanrop för att rapportera filens Överföringsförlopp.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

I följande exempel anropar UploadFile funktion och anger lagringskryptering som alternativet Skapa för tillgången.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade containern. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Nu när du har överfört en tillgång till Media Services, går du till den [så här hämtar du en Medieprocessor] [ How to Get a Media Processor] artikeln.

[How to Get a Media Processor]: media-services-get-media-processor.md

