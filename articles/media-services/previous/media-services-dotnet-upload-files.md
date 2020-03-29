---
title: Ladda upp filer till ett Media Services-konto med .NET | Microsoft-dokument
description: Läs om hur du får in medieinnehåll i Media Services genom att skapa och ladda upp resurser.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 03b9995eab503ac1fcd4615882419dde31d4f8bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64869471"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Ladda upp filer till ett Media Services-konto med .NET 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

I Media Services överför du (eller för in) dina digitala filer till en tillgång. **Asset-entiteten** kan innehålla video- ljud, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text (och metadata om dessa filer.)  När filerna har laddats upp lagras ditt innehåll säkert i molnet för vidare bearbetning och streaming.

Filerna i tillgången kallas **Tillgångsfiler**. **AssetFile-förekomsten** och den faktiska mediefilen är två olika objekt. AssetFile-instansen innehåller metadata om mediefilen, medan mediefilen innehåller det faktiska medieinnehållet.

## <a name="considerations"></a>Överväganden

Följande gäller:
 
 * Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL:er för direktuppspelningsinnehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför är procentkodning inte tillåtet. Värdet för egenskapen **Name** kan inte ha något av följande [procent-kodningsreservat tecken:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Dessutom kan det bara finnas en "." för filnamnstillägget.
* Namnets längd får inte vara större än 260 tecken.
* Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.
* Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

När du skapar resurser kan du ange följande krypteringsalternativ:

* **Ingen** – Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet är ditt innehåll inte skyddat under överföring eller i vila i lagring.
  Om du planerar att leverera en MP4 med progressiv nedladdning använder du det här alternativet: 
* **CommonEncryption** - Använd det här alternativet om du laddar upp innehåll som redan har krypterats och skyddats med gemensam kryptering eller PlayReady DRM (till exempel Smooth Streaming skyddas med PlayReady DRM).
* **KuvertEnkrypterad** – Använd det här alternativet om du laddar upp HLS krypterad med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.
* **StorageEncrypted** - Krypterar ditt rensa innehåll lokalt med AES-256-bitars kryptering och överför det sedan till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
  
    Media Services tillhandahåller lagringskryptering på disk för dina tillgångar, inte over-the-wire som Digital Rights Manager (DRM).
  
    Om tillgången är lagringskrypterad måste du konfigurera principen för tillgångsleverans. Mer information finns i [Konfigurera leveransprincip för tillgångar](media-services-dotnet-configure-asset-delivery-policy.md).

Om du anger att tillgången ska krypteras med ett **CommonEncrypted-alternativ,** eller ett **kuvertkrypterat** alternativ, måste du associera din tillgång med en **ContentKey**. Mer information finns i [Så här skapar du en ContentKey](media-services-dotnet-create-contentkey.md). 

Om du anger att tillgången ska krypteras med alternativet **StorageEncrypted** skapas en **StorageEncrypted** **ContentKey** för din tillgång.

Den här artikeln visar hur du använder Media Services .NET SDK samt Media Services .NET SDK-tillägg för att ladda upp filer till en Media Services-tillgång.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Ladda upp en enda fil med Media Services .NET SDK

Följande kod använder .NET för att ladda upp en enda fil. AccessPolicy och Locator skapas och förstörs av upload-funktionen. 

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
Följande kod visar hur du skapar en tillgång och laddar upp flera filer.

Koden gör följande:

* Skapar en tom tillgång med metoden CreateEmptyAsset som definierats i föregående steg.
* Skapar en **AccessPolicy-instans** som definierar behörigheter och varaktighet för åtkomst till tillgången.
* Skapar en **positionerarinstans** som ger åtkomst till tillgången.
* Skapar en **BlobTransferClient-instans.** Den här typen representerar en klient som fungerar på Azure-blobbar. I det här exemplet övervakar klienten överföringsstatusen. 
* Räknar upp filer i den angivna katalogen och skapar en **AssetFile-instans** för varje fil.
* Överför filerna till Media Services med metoden **UploadAsync.** 

> [!NOTE]
> Använd metoden UploadAsync för att se till att samtalen inte blockerar och att filerna överförs parallellt.
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


När du laddar upp ett stort antal tillgångar bör du tänka på följande:

* Skapa ett nytt **CloudMediaContext-objekt** per tråd. Klassen **CloudMediaContext** är inte trådsäker.
* Öka AntalConcurrentTransfers från standardvärdet 2 till ett högre värde som 5. Om du anger den här egenskapen påverkas alla instanser av **CloudMediaContext**. 
* Behåll ParallelTransferThreadCount till standardvärdet 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Intag av tillgångar i bulk med Media Services .NET SDK
Att ladda upp stora tillgångsfiler kan vara en flaskhals när tillgången skapas. Intag av tillgångar i bulk eller "Bulk intag", innebär frikoppling tillgång skapas från uppladdningsprocessen. Om du vill använda en massinmattningsmetod skapar du ett manifest (IngestManifest) som beskriver tillgången och dess associerade filer. Använd sedan den överförmetod du väljer för att överföra associerade filer till manifestets blob-behållare. Microsoft Azure Media Services bevakar blob-behållaren som är associerad med manifestet. När en fil har överförts till blob-behållaren slutför Microsoft Azure Media Services skapandet av tillgångar baserat på konfigurationen av tillgången i manifestet (IngestManifestAsset).

Om du vill skapa en ny IngestManifest anropar du metoden Skapa som exponeras av samlingen IngestManifests på CloudMediaContext. Den här metoden skapar en ny IngestManifest med manifestnamnet som du anger.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Skapa de tillgångar som är associerade med bulk IngestManifest. Konfigurera önskade krypteringsalternativ på tillgången för massintag.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

En IngestManifestAsset associerar en tillgång med en bulk IngestManifest för bulk intag. Den associerar också AssetFiles som utgör varje tillgång. Om du vill skapa en IngestManifestAsset använder du metoden Skapa på serverkontexten.

I följande exempel visas att du lägger till två nya IngestManifestAssets som associerar de två tillgångar som tidigare skapats till bulkintationmaniten. Varje IngestManifestAsset associerar också en uppsättning filer som överförs för varje tillgång under massintag.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Du kan använda alla höghastighetsklientprogram som kan ladda upp tillgångsfilerna till uri-behållaren blob storage som tillhandahålls av **egenskapen IIngestManifest.BlobStorageUriForUpload för** IngestManifest. 

Följande kod visar hur du använder .NET SDK för att ladda upp resursfilerna.

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

Koden för att ladda upp tillgångsfilerna för exemplet som används i den här artikeln visas i följande kodexempel:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Du kan bestämma hur massintag för alla tillgångar som är associerade med en **IngestManifest** fortskrider genom att avsöka egenskapen Statistik för **IngestManifest**. För att uppdatera statusinformation måste du använda en ny **CloudMediaContext** varje gång du avsöker egenskapen Statistik.

Följande exempel visar röstning en IngestManifest av dess **ID**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Ladda upp filer med .NET SDK-tillägg
I följande exempel visas hur du laddar upp en enda fil med .NET SDK-tillägg. I det här fallet används **metoden CreateFromFile,** men den asynkrona versionen är också tillgänglig (**CreateFromFileAsync**). Med metoden **CreateFromFile** kan du ange filnamn, krypteringsalternativ och en motringning för att rapportera filens överföringsförlopp.

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

I följande exempel anropas UploadFile-funktionen och lagringskrypteringen anges som alternativet att skapa tillgångar.  

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
Nu när du har laddat upp en tillgång till Media Services går du till artikeln [Så här hämtar du en medieprocessor.][How to Get a Media Processor]

[How to Get a Media Processor]: media-services-get-media-processor.md

