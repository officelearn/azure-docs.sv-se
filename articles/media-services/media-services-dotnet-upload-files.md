---
title: "Ladda upp filer till ett Media Services-konto med hjälp av .NET | Microsoft Docs"
description: "Lär dig mer om att få medieinnehåll i Media Services genom att skapa och ladda upp tillgångar."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: juliako
ms.openlocfilehash: ec8c1da633374ba684f6a0a895c542ee76ef73b8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Ladda upp filer till ett Media Services-konto med hjälp av .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

I Media Services överför du (eller för in) dina digitala filer till en tillgång. Den **tillgången** entitet kan innehålla video, ljud, bilder, miniatyrsamlingar, text spår och textning filer (och metadata om dessa filer.)  När filerna har överförts lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning.

Filerna i tillgången kallas **Tillgångsfiler**. Den **AssetFile** instansen och den faktiska mediefilen är två distinkta objekt. AssetFile-instans innehåller metadata om filen media när mediefilen innehåller faktiskt medieinnehåll.

> [!NOTE]
> Följande gäller:
> 
> * Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför tillåts procent-encoding inte. Värdet för den **namn** egenskapen får inte ha något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det kan bara finnas ett '.' för filnamnstillägget.
> * Längden på namnet får inte vara större än 260 tecken.
> * Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [det här](media-services-quotas-and-limitations.md) avsnittet.
> * Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.
> 

När du skapar tillgångar, anger du följande krypteringsalternativ. 

* **Ingen** – Ingen kryptering används. Detta är standardvärdet. Observera att när du använder det här alternativet om ditt innehåll inte skyddas under överföringen eller i vila i lagring.
  Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet. 
* **CommonEncryption** – Använd det här alternativet om du överför innehåll som redan har krypterats och skyddats med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
* **EnvelopeEncrypted** – Använd det här alternativet om du överför HLS som krypterats med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.
* **StorageEncrypted** - krypterar innehållet lokalt med hjälp av AES 256 bitarskryptering och överför den till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Det primära användningsfallet för Lagringskryptering är när du vill skydda dina hög kvalitet inkommande mediefiler med stark kryptering i vila på disk.
  
    Media Services tillhandahåller på disklagring kryptering för dina tillgångar, inte över överföring som Digital Rights Manager (DRM).
  
    Om tillgången är lagringskrypterad, måste du konfigurera principen för tillgångsleverans. Mer information finns i [konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

Om du anger för din tillgång som ska krypteras med en **CommonEncrypted** alternativ, eller en **EnvelopeEncypted** alternativet måste du associera din tillgång med en **ContentKey**. Mer information finns i [hur du skapar en ContentKey](media-services-dotnet-create-contentkey.md). 

Om du anger för din tillgång som ska krypteras med en **StorageEncrypted** alternativ, Media Services SDK för .NET skapar en **StorateEncrypted** **ContentKey** för din tillgången.

Det här avsnittet visar hur du använder Media Services .NET SDK samt Media Services .NET SDK-tillägg för att överföra filer till en tillgång med Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Ladda upp en enstaka fil med Media Services .NET SDK
Exempelkoden nedan använder .NET SDK för att överföra en fil. AccessPolicy och lokaliserare skapas och förstörs av funktionen överföringen. 


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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Överför flera filer med Media Services .NET SDK
Följande kod visar hur du skapar en tillgång och överför flera filer.

Koden gör följande:

* Skapar en tom tillgång med metoden CreateEmptyAsset som definierats i föregående steg.
* Skapar en **AccessPolicy** -instans som definierar behörigheter och varaktighet för åtkomst till tillgången.
* Skapar en **lokaliserare** instans som ger åtkomst till tillgången.
* Skapar en **BlobTransferClient** instans. Den här typen representerar en klient som körs på Azure-blobbar. I det här exemplet använder vi klienten övervakar överför. 
* Räknar upp via filer i den angivna katalogen och skapar en **AssetFile** -instans för varje fil.
* Överför filer till Media Services med hjälp av den **UploadAsync** metod. 

> [!NOTE]
> Använd metoden UploadAsync så att anrop inte blockerar och filerna har överförts parallellt.
> 
> 

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

                // It is recommended to validate AccestFiles before upload. 
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



Tänk på följande när du hämtar ett stort antal tillgångar.

* Skapa en ny **CloudMediaContext** objekt per tråd. Den **CloudMediaContext** klass är inte trådsäker.
* Öka NumberOfConcurrentTransfers från standardvärdet 2 till ett högre värde som 5. Den här egenskapen påverkar alla förekomster av **CloudMediaContext**. 
* Hålla ParallelTransferThreadCount standardvärdet 10.

## <a id="ingest_in_bulk"></a>Fört in tillgångar i gång med hjälp av Media Services .NET SDK
Överföringen av stora tillgångsfiler kan utgöra en flaskhals under skapande av tillgångsinformation. Fört in tillgångar i massutskick eller ”samtidigt vill föra in” innebär att Frikoppling tillgången skapas överföringen. Skapa ett manifest (IngestManifest) som beskriver tillgången och dess associerade filer om du vill använda en grupp som vill föra in metod. Sedan använda metoden överför du väljer att ladda upp filerna i manifestet blob-behållaren. Microsoft Azure Media Services bevakar blob-behållare som är associerade med manifestet. När en fil har överförts till blob-behållare, Slutför Microsoft Azure Media Services tillgång skapas baserat på konfigurationen av tillgången i manifestet (IngestManifestAsset).

Om du vill skapa en ny IngestManifest anropa metoden skapa som exponeras av IngestManifests samlingen på CloudMediaContext. Den här metoden skapar en ny IngestManifest med manifestnamnet som du anger.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Skapa tillgångar som ska associeras med flesta IngestManifest. Konfigurera önskade krypteringsalternativ för tillgångsinformation för att föra in samtidigt.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

En IngestManifestAsset associerar en tillgång med bulk IngestManifest för att föra in samtidigt. Här associerar även AssetFiles som utgör varje tillgång. Använd Create-metoden för att skapa en IngestManifestAsset på serverkontext.

Exemplet nedan visar att lägga till två nya IngestManifestAssets som kopplar två tillgångar skapat för flesta mata in manifestet. Varje IngestManifestAsset associerar även en uppsättning filer som överförs för varje tillgång vid massinläsning vill föra in.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });

Du kan använda en hög hastighet klientprogrammet kan överföra tillgångsfiler till blob storage-behållare URI som tillhandahålls av den **IIngestManifest.BlobStorageUriForUpload** -egenskapen för IngestManifest. En tjänst för överföringen av viktiga hög hastighet är [Aspera på begäran för Azure-programmet](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Du kan också skriva kod för att överföra tillgångar filerna som visas i följande kodexempel.

    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);

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

Koden för uppladdning av tillgångsfiler för används i det här avsnittet visas i följande kodexempel.

    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);


Du kan se förloppet för den grupp vill föra in för alla resurser som är associerade med en **IngestManifest** genom att avsöka egenskapen statistik för den **IngestManifest**. För att kunna uppdatera statusinformation, måste du använda en ny **CloudMediaContext** varje gång du avsöka egenskapen statistik.

Exemplet nedan visar avsöker en IngestManifest av dess **Id**.

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



## <a name="upload-files-using-net-sdk-extensions"></a>Överföra filer med hjälp av .NET SDK-tillägg
Exemplet nedan visar hur du laddar upp en enstaka fil med .NET SDK-tillägg. I det här fallet den **CreateFromFile** metod används, men den asynkrona versionen är också tillgänglig (**CreateFromFileAsync**). Den **CreateFromFile** metoden kan du ange filnamnet, kryptering och ett återanrop för att rapportera filen Överföringsförlopp.

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

I följande exempel anropar UploadFile funktion och anger lagringskryptering som alternativ för skapande av tillgångsinformation.  

    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);

## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade behållaren. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Nu när du har överfört en tillgång till Media Services, gå till den [hur du hämtar en Medieprocessor] [ How to Get a Media Processor] avsnittet.

[How to Get a Media Processor]: media-services-get-media-processor.md

