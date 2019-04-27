---
title: Analysera video innehåll för stötande material i C# -Content Moderator
titlesuffix: Azure Cognitive Services
description: Hur du analyserar videoinnehåll för olika stötande material med innehåll Moderator SDK för .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e987c1249360b14fddf8af57c61fdd1a46ee6c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605329"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analysera video innehåll för stötande material iC#

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med den [innehåll Moderator SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) söker igenom videoinnehåll för vuxet eller olämpligt innehåll.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

Content Moderator videomoderering funktionen är tillgänglig som en kostnadsfri offentlig förhandsversion **medieprocessor** i Azure Media Services (AMS). Azure Media Services är en särskild Azure-tjänst för att lagra och strömma videofiler. 

### <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Följ instruktionerna i [skapa ett Azure Media Services-konto](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) att prenumerera på AMS och skapa en associerad Azure storage-konto. Skapa en ny Blob storage-behållare i det lagringskontot.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Gå till din nya AMS-prenumeration i Azure-portalen och välj **API-åtkomst** på menyn på klientsidan. Välj **Anslut till Azure Media Services med tjänstens huvudnamn**. Notera värdet i den **REST API-slutpunkt** fältet; du behöver det senare.

I den **Azure AD-app** väljer **Skapa ny** och namnge din nya Azure AD-programregistrering (till exempel ”VideoModADApp”). Klicka på **spara** och Vänta några minuter medan programmet är konfigurerat. Då, bör du se din nya appregistreringen under den **Azure AD-app** på sidan.

Välj din appregistrering och klicka på den **Hanteringsappen** knappen under den. Notera värdet i den **program-ID** fältet; du behöver det senare. Välj **inställningar** > **nycklar**, och ange en beskrivning för en ny nyckel (till exempel ”VideoModKey”). Klicka på **spara**, och notera värdet för nya nyckeln. Kopiera följande sträng och spara den någonstans säkert.

En mer omfattande genomgång av processen ovan finns i [Kom igång med Azure AD-autentisering](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

När du har gjort det, kan du använda mediebearbetare videomoderering på två olika sätt.

## <a name="use-azure-media-services-explorer"></a>Använd Azure Media Services Explorer

Azure Media Services Explorer är ett användarvänligt frontend för AMS. Du kan använda den för att bläddra ditt AMS-konto, ladda upp videor och söker igenom innehållet i medieprocessor Content Moderator. Hämta och installera det från [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases), eller se den [Azure Media Services Explorer blogginlägget](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) för mer information.

![Azure Media Services explorer med Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. I Visual Studio skapar du en ny **konsolapp (.NET Framework)** projektet och ge den namnet **VideoModeration**. 
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Lägg till kod för videomoderering

Nu ska du kopiera och klistra in koden från den här guiden i ditt projekt för att implementera ett grundläggande innehållsmodereringsscenario.

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Lägg till följande `using`-instruktioner överst i _Program.cs_-filen.

```csharp
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
```

### <a name="set-up-resource-references"></a>Konfigurera resurs-referenser

Lägg till följande statiska fält till klassen **Program** i _Program.cs_. De här fälten hålla informationen behövs för att ansluta till AMS-prenumeration. Fyll dem med de värden som du fick i stegen ovan. Observera att `CLIENT_ID` är den **program-ID** värdet för din Azure AD-app och `CLIENT_SECRET` är värdet för den ”VideoModKey” som du skapade för den appen.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Om du vill använda en lokal videofil (enklaste fallet) lägga till den i projektet och ange dess sökväg som den `INPUT_FILE` värde (relativa sökvägar är relativa till körningskatalogen).

Du måste också skapa den _preset.json_ filen i den aktuella katalogen och använda den för att ange ett versionsnummer. Exempel:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Läsa in indata video(s)

Den **Main** -metoden för den **programmet** klass skapar ett Azure Media-kontexten och sedan ett Azure Storage-kontext (om dina videor finns i blob storage). Återstående koden söker igenom en video från en lokal mapp, blob eller flera blobbar i en Azure storage-behållare. Du kan prova alla alternativ genom att kommentera ut andra rader med kod.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Skapa ett Azure Media-kontext

Lägg till följande metod i klassen **Program**. Här används AMS-autentiseringsuppgifter för att tillåta kommunikation med AMS.

```csharp
// Creates a media context from azure credentials
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
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Lägg till kod för att skapa ett Azure Storage-kontext

Lägg till följande metod i klassen **Program**. Du kan använda Storage-kontext som skapats från storage-autentiseringsuppgifter för att få åtkomst till ditt blob storage.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Lägg till kod för att skapa Azure Medietillgångar från lokal fil och blob

Mediebearbetare för Content Moderator kör jobb **tillgångar** i Azure Media Services-plattformen.
Dessa metoder skapa tillgångar från en lokal fil eller en associerad blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Lägg till kod för att skanna en samling med videor (som blobar) i en behållare

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
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

// Get all blobs in your container
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
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Lägg till metod för att köra jobbet för Content Moderator

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
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
```

### <a name="add-helper-functions"></a>Lägg till hjälpfunktioner

Dessa metoder ladda ned utdatafilen Content Moderator (JSON) från Azure Media Services-tillgången och hjälpa dig spåra statusen för moderering-jobbet så att programmet kan logga en Körstatus till konsolen.

```csharp
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
```

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

```json
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
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar [videon går igenom](video-reviews-quickstart-dotnet.md) från moderering-utdata.

Lägg till [avskrift moderering](video-transcript-moderation-review-tutorial-dotnet.md) på din video granskningar.

Kolla in detaljerad genomgång om hur du skapar en [väljer video- och avskrift moderering](video-transcript-moderation-review-tutorial-dotnet.md).

[Ladda ned Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra Content Moderator-Snabbstart för .NET.
