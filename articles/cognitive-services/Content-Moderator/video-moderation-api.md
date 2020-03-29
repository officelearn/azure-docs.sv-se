---
title: Analysera videoinnehåll för stötande material i C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Så här analyserar du videoinnehåll för olika stötande material med hjälp av Content Moderator SDK för .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755244"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analysera videoinnehåll för stötande material i C #

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med [Content Moderator SDK för .NET för](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) att söka igenom videoinnehåll efter barn eller racy-innehåll.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="prerequisites"></a>Krav
- Alla utgåvor av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

Innehållsmoderatorns videomoderationsfunktion är tillgänglig som en kostnadsfri offentlig **förhandsgranskningsmedieprocessor** i Azure Media Services (AMS). Azure Media Services är en specialiserad Azure-tjänst för lagring och direktuppspelning av videoinnehåll. 

### <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Följ instruktionerna i [Skapa ett Azure Media Services-konto](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) för att prenumerera på AMS och skapa ett associerat Azure-lagringskonto. Skapa en ny Blob-lagringsbehållare i det lagringskontot.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Navigera till din nya AMS-prenumeration i Azure-portalen och välj **API-åtkomst** från sidomenyn. Välj **Anslut till Azure Media Services med tjänstens huvudnamn**. Observera värdet i fältet **REST API-slutpunkt.** du kommer att behöva detta senare.

I avsnittet **Azure AD-app** väljer du **Skapa ny** och namnger din nya Azure AD-programregistrering (till exempel "VideoModADApp"). Klicka på **Spara** och vänta några minuter medan programmet är konfigurerat. Sedan bör du se din nya appregistrering under avsnittet **Azure AD-app** på sidan.

Välj din appregistrering och klicka på knappen **Hantera program** under den. Observera värdet i fältet **Program-ID.** du kommer att behöva detta senare. Välj **Inställningstangenter** > **Keys**och ange en beskrivning av en ny nyckel (till exempel "VideoModKey"). Klicka på **Spara**och lägg sedan märke till det nya nyckelvärdet. Kopiera den här strängen och spara den på ett säkert ställe.

En mer grundlig genomgång av ovanstående process finns i [Komma igång med Azure AD-autentisering](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

När du har gjort detta kan du använda videomoderingsmedieprocessorn på två olika sätt.

## <a name="use-azure-media-services-explorer"></a>Använda Utforskaren för Azure Media Services

Utforskaren för Azure Media Services är en användarvänlig klientdel för AMS. Använd den för att bläddra i ditt AMS-konto, ladda upp videor och skanna innehåll med content moderatorns medieprocessor. Hämta och installera det från [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)eller se [blogginlägget](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) i Azure Media Services Explorer för mer information.

![Utforskaren för Azure Media Services med Innehållsmoderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Skapa ett nytt **konsolappprojekt (.NET Framework) i** Visual Studio och namnge det **VideoModeration**. 
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - windowsazure.mediaservices windowsazure.mediaservices windowsazure.mediaservices windowsaz
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Lägga till videomoderationskod

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

### <a name="set-up-resource-references"></a>Ställ in resursreferenser

Lägg till följande statiska fält i klassen **Program** i _Program.cs_. Dessa fält innehåller den information som krävs för att ansluta till din AMS-prenumeration. Fyll dem med de värden du har i stegen ovan. Observera `CLIENT_ID` att det är **värdet för program-ID** för din Azure AD-app och `CLIENT_SECRET` är värdet för "VideoModKey" som du skapade för den appen.

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

Om du vill använda en lokal videofil (enklaste fallet) lägger du `INPUT_FILE` till den i projektet och anger sökvägen som värde (relativa sökvägar är relativa till körningskatalogen).

Du måste också skapa _filen preset.json_ i den aktuella katalogen och använda den för att ange ett versionsnummer. Ett exempel:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Ladda indatavideon/indatavideon

**Huvudmetoden** för **klassen Program** skapar en Azure Media-kontext och sedan en Azure Storage-kontext (om dina videor är i blob-lagring). Den återstående koden söker igenom en video från en lokal mapp, blob eller flera blobbar i en Azure-lagringsbehållare. Du kan prova alla alternativ genom att kommentera ut de andra kodraderna.

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

### <a name="create-an-azure-media-context"></a>Skapa en Azure Media-kontext

Lägg till följande metod i klassen **Program**. Detta använder dina AMS-autentiseringsuppgifter för att tillåta kommunikation med AMS.

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Lägga till koden för att skapa en Azure Storage-kontext

Lägg till följande metod i klassen **Program**. Du använder lagringskontexten, som skapats från dina lagringsuppgifter, för att komma åt din blob-lagring.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Lägga till koden för att skapa Azure Media-resurser från lokala filer och blob

Medieprocessorn för Innehållsmoderator kör jobb på **resurser** inom Azure Media Services-plattformen.
Dessa metoder skapar tillgångarna från en lokal fil eller en associerad blob.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Lägga till koden för att skanna en samling videor (som blobbar) i en behållare

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Lägga till metoden för att köra jobbet innehållsmoderererator

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

### <a name="add-helper-functions"></a>Lägga till hjälpfunktioner

Dessa metoder hämtar utdatafilen för Content Moderator (JSON) från Azure Media Services-tillgången och hjälper dig att spåra status för modereringsjobbet så att programmet kan logga en status som körs till konsolen.

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

När jobbet Innehållsmoderering har slutförts analyserar du JSON-svaret. Den består av dessa delar:

- Sammanfattning av videoinformation
- **Skott** som "**fragment**"
- **Nyckelbildrutor** som "**händelser**" med en **reviewRecommended" (= sant eller falskt)"** flagga baserad på **vuxen-** och **racypoäng**
- **start**, **varaktighet,** **totalDuration**och **tidsstämpel** är i "fästingar". Dividera med **tidsskala** för att få numret i sekunder.
 
> [!NOTE]
> - `adultScore`representerar den potentiella närvaro- och förutsägelsepoängen för innehåll som kan betraktas som sexuellt explicit eller vuxen i vissa situationer.
> - `racyScore`representerar den potentiella närvaro- och förutsägelsepoängen för innehåll som kan anses vara sexuellt suggestivt eller moget i vissa situationer.
> - `adultScore`och `racyScore` är mellan 0 och 1. Ju högre poäng, desto högre modell förutspår att kategorin kan vara tillämplig. Den här förhandsversionen bygger på en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig till dina krav.
> - `reviewRecommended`är antingen sant eller falskt beroende på de interna poängtrösklarna. Kunder bör bedöma om de ska använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehållsprinciper.

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

Läs om hur du skapar [videorecensioner](video-reviews-quickstart-dotnet.md) från modereringsutdata.

Lägg till [avskriftsmodering](video-transcript-moderation-review-tutorial-dotnet.md) i dina videorecensioner.

Kolla in den detaljerade handledning om hur man bygger en [komplett video och avskrift moderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

[Ladda ned Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för den här och andra snabbstarter för Innehållsmoderator för .NET.
