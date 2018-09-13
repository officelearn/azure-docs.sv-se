---
title: Azure Content Moderator - videomoderering | Microsoft Docs
description: Använd videomoderering för att söka efter möjliga vuxet eller olämpligt innehåll.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: 27e189d93573dea139c2b67c237c376a28100c2b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714317"
---
# <a name="video-moderation"></a>Videomoderering

Idag, generera online visningsprogram miljarder video vyer över webbplatser för populära och regionala sociala medier och ökar. Genom att använda maskininlärning tjänster för att förutsäga potentiellt vuxet eller olämpligt innehåll kan sänka du kostnaden för ditt arbete med moderering.

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>Registrera dig för Content Moderator-mediebearbetare (förhandsversion)

### <a name="create-a-free-azure-account"></a>Skapa ett kostnadsfritt Azure-konto

[Börja här](https://azure.microsoft.com/free/) att skapa ett kostnadsfritt Azure-konto om du inte redan har en.

### <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Content Moderator video funktionen är tillgänglig som en offentlig förhandsversion **medieprocessor** i Azure Media Services (AMS) utan kostnad.

[Skapa ett Azure Media Services-konto](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) i Azure-prenumerationen.

### <a name="get-azure-active-directory-credentials"></a>Hämta autentiseringsuppgifter för Azure Active Directory

   1. Läs den [Azure Media Services portal artikeln](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) att lära dig hur du använder Azure-portalen för att hämta dina autentiseringsuppgifter för Azure AD-autentisering.
   1. Läs den [Azure Media Services .NET artikeln](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) att lära dig hur du använder Azure Active Directory-autentiseringsuppgifter med .NET SDK.

   > [!NOTE]
   > Exempelkoden i den här snabbstarten använder den **tjänstobjektautentisering** metod som beskrivs i båda artiklar.

När du har hämtat dina AMS-autentiseringsuppgifter, finns det två sätt att testa mediebearbetare Content Moderator.

## <a name="use-azure-media-services-explorer"></a>Använd Azure Media Services Explorer

Använd det interaktiva [Azure Media Services (AMS) explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) för att bläddra bland ditt AMS-konto, ladda upp videor och Genomsök med Content Moderator mediebearbetare. [Hämta och installera det](https://github.com/Azure/Azure-Media-Services-Explorer/releases) från GitHub, och [Bläddra källkoden](http://github.com/Azure/Azure-Media-Services-Explorer) att ta itu med hjälp av AMS SDK.

![Azure Media Services explorer med Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Snabbstart för .NET med Visual Studio och C#

1. Lägga till en ny **konsolapp (.NET Framework)** projekt i lösningen.

   Namnge projektet i exempelkoden **VideoModeration**.

1. Välj det här projektet som enda Startprojekt för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paketen tillgängliga på [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder uttryck

Ändra programmet är med hjälp av uttryck.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Initiera programspecifika inställningar

Lägg till följande statiska fält i den **programmet** klassen i Program.cs.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Skapa en förinställd fil (json)

Skapa en JSON-fil i den aktuella katalogen med versionsnumret.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Lägg till följande kod i main-metoden

Main-metoden skapar först en kontext för Azure Media och sedan på ett Azure Storage-kontext om dina videor som finns i blob storage.
Återstående koden söker igenom en video från en lokal mapp, blob eller flera blobbar i en Azure storage-behållare.
Du kan prova alla alternativ genom att kommentera ut andra rader med kod.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Lägg till kod för att skapa en Azure Media-kontext

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Lägg till kod för att skapa ett Azure Storage-kontext
Du kan använda Storage-kontext som skapats från Storage-autentiseringsuppgifter för att få åtkomst till ditt blob storage.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Lägg till kod för att skapa Azure Medietillgångar från lokal fil och blob
Mediebearbetare för Content Moderator kör jobb **tillgångar** i Azure Media Services-plattformen.
Dessa metoder skapa tillgångar från en lokal fil eller en associerad blob.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Lägg till kod för att skanna en samling med videor (som blobar) i en behållare

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Lägg till metod för att köra jobbet för Content Moderator

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Lägg till ett par hjälpfunktioner

Dessa metoder ladda ned utdatafilen Content Moderator (JSON) från Azure Media Services-tillgången och hjälpa dig spåra statusen för moderering-jobbet så att programmet kan logga en Körstatus till konsolen.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

När innehållsmoderering jobbet har slutförts kan du analysera JSON-svar. Det består av följande element:

- Videoinformation sammanfattning
- **Skärmbilder** som ”**fragment**”
- **Nyckeln bildrutor** som ”**händelser**” med en **reviewRecommended ”(= SANT eller FALSKT)”** flaggan utifrån **vuxet** och **Racy** poäng
- **Starta**, **varaktighet**, **totalDuration**, och **tidsstämpel** finns i ”ticken”. Dela med **tidsskalan** att få en på några sekunder.
 
> [!NOTE]

> - `adultScore` representerar den potentiella närvaro och förutsägelse poängen för innehåll som kan uppfattas som sexuellt explicit eller är olämpligt för barn i vissa situationer.
> - `racyScore` representerar den potentiella närvaro och förutsägelse poängen för innehåll som kan uppfattas som sexuellt något eller mogen i vissa situationer.
> - `adultScore` och `racyScore` är mellan 0 och 1. Ju högre poäng desto högre modellen är att förutsäga att kategorin kan användas. Den här förhandsversionen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll att avgöra hur varje kategori stämmer överens med dina behov.
> - `reviewRecommended` är true eller false beroende på den interna poängen tröskelvärden. Kunder bör utvärdera om du vill använda det här värdet eller välja anpassade tröskelvärden baserat på deras innehåll principer.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar [videon går igenom](video-reviews-quickstart-dotnet.md) från moderering-utdata.

Lägg till [avskrift moderering](video-transcript-moderation-review-tutorial-dotnet.md) på din video granskningar.

Kolla in detaljerad genomgång om hur du skapar en [väljer video- och avskrift moderering](video-transcript-moderation-review-tutorial-dotnet.md).

[Ladda ned Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra Content Moderator-Snabbstart för .NET.
