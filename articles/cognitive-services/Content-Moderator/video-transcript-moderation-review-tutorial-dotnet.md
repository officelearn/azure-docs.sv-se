---
title: 'Självstudier: Moderera videor och avskrifter i .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: I den här självstudien visas hur du skapar en komplett modereringslösning för video och avskrift med maskinassisterad moderering och HITL-granskning (human-in-the-loop).
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e3578c08b78894c2f9a858e97c7cbe2d260731c5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878756"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Självstudier: Moderering av videor och avskrifter

Med Content Moderators video-API:er kan du moderera videor och skapa videogranskningar i det manuella granskningsverktyget. 

I den här självstudien visas hur du skapar en komplett modereringslösning för video och avskrift med maskinassisterad moderering och HITL-granskning (human-in-the-loop).

Ladda ned [C#-konsolprogrammet](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) för den här självstudien. Konsolprogrammet använder SDK:n och relaterade paket till att utföra följande uppgifter:

- Komprimera indatavideo(r) för snabbare bearbetning
- Moderera videon för att få bilder och bildrutor med information
- Använda bildrutans tidsstämplar till att skapa miniatyrer (bilder)
- Skicka tidsstämplar och miniatyrer för att skapa videogranskningar
- Konvertera videons tal till text (avskrift) med Media Indexer-API:n
- Moderera avskriften med textändringstjänsten
- Lägga till den modererade avskriften i videogranskningen

## <a name="sample-program-outputs"></a>Exempel på programutdata

Innan vi fortsätter ska vi titta på följande exempelutdata från programmet:

- [Konsolutdata](#program-output)
- [Videogranskning](#video-review-default-view)
- [Avskriftsvisning](#video-review-transcript-view)

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Registrera dig på webbplatsen [Content Moderator-granskningsverktyg](https://contentmoderator.cognitive.microsoft.com/) och [skapa anpassade taggar](Review-Tool-User-Guide/tags.md) som C#-konsolprogrammet tilldelar inifrån koden. Följande skärmbild visar de anpassade taggarna.

  ![Anpassade taggar för videoändring](images/video-tutorial-custom-tags.png)

1. Om du vill köra exempelprogrammet måste du ha ett Azure-konto och ett Azure Media Services-konto. Du måste även ha åtkomst till den privata förhandsgranskningen av Content Moderator. Slutligen behöver du Azure Active Directory-autentiseringsuppgifter. Mer information om hur du skaffar den här informationen finns i [snabbstarten för videoändrings-API:n](video-moderation-api.md).

1. Redigera filen `App.config` och lägg till Active Directory-klientorganisationens namn, tjänstslutpunkter och prenumerationsnycklar som anges av `#####`. Du behöver följande information:

|Nyckel|Beskrivning|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Slutpunkt för API:n till Azure Media Services (AMS)|
|`ClientSecret`|Prenumerationsnyckel för Azure Media Services|
|`ClientId`|Klient-ID för Azure Media Services|
|`AzureAdTenantName`|Active Directory-klientorganisationens namn som representerar din organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Prenumerationsnyckel för gransknings-API:n i Content Moderator|
|`ContentModeratorApiEndpoint`|Slutpunkt för Content Moderator-API:n|
|`ContentModeratorTeamId`|Team-ID för Content Moderator|

## <a name="getting-started"></a>Komma igång

Klassen `Program` i `Program.cs` är den främsta startpunkten till videoändringsprogrammet.

### <a name="methods-of-class-program"></a>Klassmetoder för Program

|Metod|Beskrivning|
|-|-|
|`Main`|Parsar kommandoraden, samlar indata från användaren och påbörjar bearbetningen.|
|`ProcessVideo`|Komprimerar, laddar upp, modererar och skapar videogranskningar.|
|`CreateVideoStreamingRequest`|Skapar en ström som laddar upp en video|
|`GetUserInputs`|Samlar indata från användaren, vilket används när det inte finns några kommandoradsalternativ|
|`Initialize`|Initierar de objekt som behövs för modereringsprocessen|

### <a name="the-main-method"></a>Main-metoden

`Main()` är där körningen startar, så det där du kan börja förstå videoändringsprocessen.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` hanterar följande kommandoradsargument:

- Sökvägen till en katalog med MPEG-4-videofiler som ska skickas för moderering. Alla `*.mp4`-filer i den här katalogen och dess underkataloger skickas för moderering.
- En boolesk flagga (sant/falskt) som anger om textavskrifterna ska genereras i syfte att moderera ljud kan också visas.

Om det inte finns några argument på kommandoraden anropar `Main()` `GetUserInputs()`. Den här metoden uppmanar användaren att ange sökvägen till en enskild videofil och att ange om en textavskrift ska genereras.

> [!NOTE]
> Konsolprogrammet använder [Azure Media Indexer-API:n](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) till att generera avskrifter från det uppladdade videoljudspåret. Resultaten visas i WebVTT-format. Mer information om det här formatet finns i [Web Video Text Tracks-format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Metoderna Initialize och ProcessVideo

Oavsett om programmets alternativ kom från kommandoraden eller från interaktiva användarindata, anropar `Main()` därefter `Initialize()` för att skapa följande instanser:

|Klass|Beskrivning|
|-|-|
|`AMSComponent`|Komprimerar videofiler innan de skickas för moderering.|
|`AMSconfigurations`|Gränssnitt för programmets konfigurationsdata finns i `App.config`.|
|`VideoModerator`| Uppladdning, kodning, kryptering och moderering med hjälp av AMS SDK|
|`VideoReviewApi`|Hanterar videogranskningar i Content Moderator-tjänsten|

De här klasserna (förutom `AMSConfigurations` som är okomplicerad) beskrivs i detalj i kommande avsnitt av den här självstudien.

Slutligen bearbetas en videofil i taget genom att `ProcessVideo()` anropas för var och en.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()`-metoden är ganska enkel. Följande åtgärder utförs i ordning:

- Komprimerar videon
- Laddar upp videon till en Azure Media Services-tillgång
- Skapar ett AMS-jobb för att moderera videon
- Skapar en videogranskning i Content Moderator

I följande avsnitt beskrivs i detalj några av de enskilda processer som anropas av `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprimering av videon

För att minimera nätverkstrafiken konverterar programmet videofiler till H.264-format (MPEG-4 AVC) och skalar dem till en maximal bredd på 640 bildpunkter. En H.264-codec rekommenderas tack vare dess höga effektivitet (komprimeringsgrad). Komprimeringen görs med hjälp av det kostnadsfria `ffmpeg`-kommandoradsverktyget, som finns i mappen `Lib` i Visual Studio-lösningen. Indatafilerna kan ha de format som stöds av `ffmpeg`, inklusive vanliga videofilformat och codecs.

> [!NOTE]
> När programmet startas med kommandoradsalternativen, anger du en katalog som innehåller de videofiler som ska skickas för moderering. Alla filer i den här katalogen som har filnamnstillägget `.mp4` bearbetas. Om du vill bearbeta andra filnamnstillägg uppdaterar du `Main()`-metoden i `Program.cs` och inkluderar önskade tillägg.

Koden som komprimerar en enskild videofil finns i `AmsComponent`-klassen i `AMSComponent.cs`. Metoden som ansvarar för den här funktionen är `CompressVideo()`, vilket visas här.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Den här funktionen utför följande steg:

- Kontrollerar att konfigurationen i `App.config` innehåller alla nödvändiga data
- Kontrollerar att den binära `ffmpeg`-filen finns
- Skapar utdatafilnamnet genom att lägga till `_c.mp4` i basnamnet på filen (till exempel `Example.mp4` -> `Example_c.mp4`)
- Skapar en kommandoradssträng som utför konverteringen
- Startar en `ffmpeg`-process från kommandoraden
- Väntar på att videon ska bearbetas

> [!NOTE]
> Om du vet att dina videor redan har komprimerats med H.264 och har rätt storlek, kan du skriva om `CompressVideo()` för att hoppa över komprimeringen.

Metoden returnerar filnamnet på den komprimerade utdatafilen.

## <a name="uploading-and-moderating-the-video"></a>Ladda upp och moderera videon

Videon måste lagras i Azure Media Services innan den kan bearbetas av innehållsändringstjänsten. `Program`-klassen i `Program.cs` har en kort metod för `CreateVideoStreamingRequest()`. Den returnerar ett objekt som representerar den strömmande begäran som användes för att ladda upp videon.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

Det resulterande objektet `UploadVideoStreamRequest` definieras i `UploadVideoStreamRequest.cs` (och dess överordnade, `UploadVideoRequest`, i `UploadVideoRequest.cs`). De här klasserna visas inte här. De är korta och fungerar bara som lagring av komprimerade videodata och dess information. En annan klass med enbart data, `UploadAssetResult` (`UploadAssetResult.cs`) används för att lagra resultatet av uppladdningen. Nu är det lättare att förstå dessa rader i `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Raderna utför följande uppgifter:

- Skapa en `UploadVideoStreamRequest` för att ladda upp den komprimerade videon
- Ange `GenerateVTT`-flaggan för begäran om användaren har begärt en textavskrift
- Anropar `CreateAzureMediaServicesJobToModerateVideo()` för att genomföra uppladdningen och ta emot resultatet

## <a name="deep-dive-into-video-moderation"></a>Djupdykning i videoändringar

Metoden `CreateAzureMediaServicesJobToModerateVideo()` finns i `VideoModerator.cs`, som innehåller den stora mängd kod som interagerar med Azure Media Services. Källkoden för metoden visas i följande extrahering.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Koden utför följande uppgifter:

- Skapar ett AMS-jobb för den bearbetning som ska utföras
- Lägger till uppgifter för att koda videofilen, moderera den och generera en textavskrift
- Skickar jobbet, laddar upp filen och påbörjar bearbetningen
- Hämtar modereringsresultaten, textavskriften (om det har begärts) och övrig information

## <a name="sample-video-moderation-response"></a>Exempel på svar från videoändring

Resultatet från videoändringsjobbet (se [snabbstarten för videoändringar](video-moderation-api.md)) är en JSON-datastruktur som innehåller modereringsresultaten. En detaljerad analys av fragmenten (videobilderna) ingår i resultaten, där vart och ett innehåller händelser (klipp) med nyckelbildrutor som har flaggats för granskning. Varje nyckelbildruta bedöms med sannolikheten att den innehåller vuxet eller olämpligt innehåll. I följande exempel visas ett JSON-svar:

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

En avskrift av ljudet från videon genereras också när `GenerateVTT`-flaggan har angetts.

> [!NOTE]
> Konsolprogrammet använder [Azure Media Indexer-API:n](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) till att generera avskrifter från det uppladdade videoljudspåret. Resultaten visas i WebVTT-format. Mer information om det här formatet finns i [Web Video Text Tracks-format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Skapa HITL-granskningen (human-in-the-loop)

Modereringsprocessen returnerar en lista med nyckelbildrutor från videon, tillsammans med avskrifter av dess ljudspår. Nästa steg är att en granskning skapas i granskningsverktyget Content Moderator för manuella ändringar. När du går tillbaka till `ProcessVideo()`-metoden i `Program.cs`, ser du anropet till `CreateVideoReviewInContentModerator()`-metoden. Den här metoden finns i `videoReviewApi`-klassen, som är i `VideoReviewAPI.cs`, och visas här.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` anropar flera andra metoder för att utföra följande uppgifter:

> [!NOTE]
> Konsolprogrammet använder [FFmpeg](https://ffmpeg.org/)-biblioteket till att generera miniatyrer. De här miniatyrerna (bilder) motsvarar bildrutans tidsstämplar i [videoändringens utdata](#sample-video-moderation-response).

|Aktivitet|Metoder|Fil|
|-|-|-|
|Extraherar nyckelbildrutorna från videon och skapar miniatyrbilder av dem|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Genomsöker textavskriften, om det finns någon, för att hitta vuxet eller olämpligt ljud|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Förbereder och skickar en begäran om videogranskning för manuell kontroll|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Standardvyn för videogranskning

Följande skärmbild visar resultatet av föregående steg.

![Standardvyn för videogranskning](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Avskriftsgenerering

Fram tills nu har koden som visas i den här självstudien fokuserat på visuellt innehåll. Granskning av talat innehåll är en separat och en valfri process som använder en avskrift som genereras av ljudet. Det är nu dags att ta en titt på hur textavskrifter skapas och används i granskningsprocessen. Uppgiften att generera avskriften finns i [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content)-tjänsten.

Programmet utför följande uppgifter:

|Aktivitet|Metoder|Fil|
|-|-|-|
|Bestämmer om textavskrifter ska genereras|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|I dessa fall skickas ett avskriftsjobb som en del av modereringen|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Hämtar en lokal kopia av avskriften|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaggar bildrutor i videon som innehåller olämpligt ljud|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Lägger till resultaten i granskningen|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Uppgiftskonfiguration

Låt oss gå direkt till att skicka avskriftsjobbet. `CreateAzureMediaServicesJobToModerateVideo()` (beskrevs tidigare) anropar `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurationen för avskriftsuppgiften läses från filen `MediaIndexerConfig.json` i lösningens `Lib`-mapp. AMS-tillgångar skapas för konfigurationsfilen och för utdatan från avskriftsprocessen. När AMS-jobbet körs skapar uppgiften textavskrifter från videofilens ljudspår.

> [!NOTE]
> Exempelprogrammet kan endast identifiera tal på (amerikansk) engelska.

### <a name="transcript-generation"></a>Avskriftsgenerering

Avskriften publiceras som en AMS-tillgång. Om du vill genomsöka avskriften efter stötande innehåll, hämtar programmet tillgången från Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` anropar `GenerateTranscript()`, vilket visas här, för att hämta filen.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occurred while generating index for video.");
            throw;
        }
    }

Efter viss nödvändig AMS-konfiguration utförs den faktiska hämtningen genom att `DownloadAssetToLocal()` anropas. Det är en generisk funktion som kopierar en AMS-tillgång till en lokal fil.

## <a name="transcript-moderation"></a>Avskriftsmoderering

När avskriften har erhållits genomsöks den och används i granskningen. Att skapa granskningen ingår i `CreateVideoReviewInContentModerator()`, som anropar `GenerateTextScreenProfanity()` för att göra jobbet. Metoden anropar i sin tur `TextScreen()`, som innehåller de flesta funktionerna. 

`TextScreen()` utför följande uppgifter:

- Parsar avskriften för tidstämplar och bildtexter
- Skickar varje bildtext för textändring
- Flaggar alla bildrutor som kan innehålla stötande talat innehåll

Låt oss nu undersöka var och en av dessa uppgifter i detalj:

### <a name="initialize-the-code"></a>Initiera koden

Initiera först alla variabler och samlingar.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Parsa avskriften för bildtexter

Därefter parsas den VTT-formaterade avskriften för bildtexter och tidsstämplar. Granskningsverktyget visar dessa bildtexter på fliken Avskrift på videogranskningsskärmen. Tidsstämplarna används till att synkronisera bildtexterna med motsvarande bildrutor.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderera bildtexterna med textändringstjänsten

Därefter genomsöker vi de parsade bildtexterna med text-API:n i Content Moderator.

> [!NOTE]
> Din tjänstnyckel för Content Moderator har en hastighetsgräns för begäranden per sekund (RPS). Om du överskrider gränsen utlöser SDK:n ett undantag med felkoden 429. 
>
> En nyckel på den kostnadsfria nivån har en hastighetsgräns på en RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Uppdelning av textändringssteget

`TextScreen()` är en omfattande metod, så vi delar upp den.

1. Metoden läser först avskriftsfilen rad för rad. Den ignorerar tomma rader och rader som innehåller en `NOTE` med en förtroendepoäng. Den extraherar tidsstämplar och textobjekt från *stackikonerna* i filen. En stackikon representerar text från ljudspåret och innehåller start- och sluttider. En stackikon börjar med tidsstämpelraden med strängen `-->`. Den följs av en eller flera rader med text.

1. Instanser av `CaptionScreentextResult` (definieras i `TranscriptProfanity.cs`) används för att lagra information som har parsats från varje stackikon.  När en ny tidsstämpelrad har identifierats, eller en maximal längd på 1 024 tecken har uppnåtts, läggs en ny `CaptionScreentextResult` till i `csrList`. 

1. Metoden skickar därefter varje stackikon till textändrings-API:n. Den anropar både `ContentModeratorClient.TextModeration.DetectLanguageAsync()` och `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, som definieras i sammansättningen `Microsoft.Azure.CognitiveServices.ContentModerator`. Om du inte vill bli begränsad pausar metoden i en sekund innan varje stackikon skickas.

1. När du fått resultaten från textändringstjänsten analyserar metoden dem för att se om de uppfyller förtroendetröskeln. Dessa värden är upprättade i `App.config` som `OffensiveTextThreshold`, `RacyTextThreshold` och `AdultTextThreshold`. Slutligen lagras också de stötande termerna. Alla bildrutor inom stackikonens tidsintervall är flaggade att innehålla stötande, olämplig och/eller vuxen text.

1. `TextScreen()` returnerar en `TranscriptScreenTextResult`-instans som innehåller textändringsresultatet från videon som helhet. Det här objektet innehåller flaggor och resultat för de olika typerna av stötande innehåll, tillsammans med en lista över alla stötande termer. Anroparen, `CreateVideoReviewInContentModerator()`, anropar `UploadScreenTextResult()` för att koppla den här informationen till granskningen så att den är tillgänglig för manuell granskning.
 
## <a name="video-review-transcript-view"></a>Avskriftsvisning vid videogranskning

Följande skärmbild visar resultatet från avskriftsgenereringen och modereringen.

![Avskriftsvisning för videoändring](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programmets utdata

Följande kommandorads utdata från programmet visar de olika uppgifterna när de har slutförts. Dessutom finns modereringsresultatet (i JSON-format) och talavskriften i samma katalog som de ursprungliga videofilerna.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Nästa steg

[Ladda ned Visual Studio-lösningen](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) och exempel på de filer och bibliotek som krävs för självstudien och sätt igång med din integrering.
