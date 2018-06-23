---
title: Azure innehåll kontrollant - måttlig videoklipp och betyg i .NET | Microsoft Docs
description: Hur du använder innehåll kontrollant för att måttlig videoklipp och betyg i .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352524"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Video och betyg avbrottsmoderering självstudiekursen

Innehåll kontrollant video API: er kan du måttlig videor och skapa video granskningar i verktyget mänsklig granska. 

Detta detaljerad kursen hjälper dig att förstå hur du skapar en komplett video och betyg avbrottsmoderering lösning med dator-stödd avbrottsmoderering och skapande av hr-i-the-loop granska.

Hämta den [C#-konsolprogram](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) för den här självstudiekursen. Konsolprogrammet använder SDK och relaterade paket för att utföra följande uppgifter:

- Komprimera inkommande video(s) för snabbare bearbetning
- Måttlig video för att få bilderna och ramar med insights
- Använd ram tidsstämplarna för att skapa miniatyrbilder (bilder)
- Skicka tidsstämplar och miniatyrer för att skapa video granskningar
- Konvertera video tal till text (betyg) med Media indexeraren API
- Måttlig utskrifter med tjänsten text avbrottsmoderering
- Lägg till kontrollerad betyg i video granskning

## <a name="sample-program-outputs"></a>Exempelprogrammet matar ut

Innan du går vidare, ska vi titta på följande exempel utdata från programmet:

- [Konsolens utdata](#program-output)
- [Video granskning](#video-review-default-view)
- [Visa betyg](#video-review-transcript-view)

## <a name="prerequisites"></a>Förutsättningar

1. Registrera dig för den [innehåll kontrollant granska verktyget](https://contentmoderator.cognitive.microsoft.com/) webbplats och [skapa anpassade taggar](Review-Tool-User-Guide/tags.md) som C#-konsolprogram tilldelar från i koden. Följande skärmbild visar anpassade taggar.

  ![Video avbrottsmoderering anpassade taggar](images/video-tutorial-custom-tags.png)

1. Om du vill köra exempelprogrammet behöver du ett Azure-konto och ett Azure Media Services-konto. Dessutom behöver du åtkomst till innehåll kontrollant privat förhandsversion. Slutligen måste Azure Active Directory-autentiseringsuppgifter. Mer information om hur du får den här informationen finns [Video Avbrottsmoderering API-Snabbstart](video-moderation-api.md).

1. Redigera filen `App.config` och lägga till Active Directory-klientnamn, slutpunkter och prenumeration nycklarna som anges av `#####`. Behöver du följande information:

|Nyckel|Beskrivning|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Slutpunkten för API: et för Azure Media Services (AMS)|
|`ClientSecret`|Prenumerationen nyckel för Azure Media Services|
|`ClientId`|Klient-ID för Azure Media Services|
|`AzureAdTenantName`|Active Directory-klientnamn som representerar organisationen|
|`ContentModeratorReviewApiSubscriptionKey`|Prenumerationen nyckel för innehåll kontrollanten granska API|
|`ContentModeratorApiEndpoint`|Slutpunkt för innehåll kontrollanten API|
|`ContentModeratorTeamId`|Innehåll kontrollant team-ID|

## <a name="getting-started"></a>Komma igång

Klassen `Program` i `Program.cs` är Huvudstartadressen till video avbrottsmoderering programmet.

### <a name="methods-of-class-program"></a>Metoderna i klassen Program

|Metod|Beskrivning|
|-|-|
|`Main`|Parsar kommandoraden, samlar in indata från användaren och påbörjar bearbetningen.|
|`ProcessVideo`|Komprimerar, överför, ändrar och skapar video granskningar.|
|`CreateVideoStreamingRequest`|Skapar en dataström för att ladda upp en video|
|`GetUserInputs`|Samlar in indata från användaren; används när det finns inga kommandoradsalternativ|
|`Initialize`|Initierar objekt som behövs för avbrottsmoderering-processen|

### <a name="the-main-method"></a>Main-metoden

`Main()` är där börjar köras, så det är börjar förstå video avbrottsmoderering processen.

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

`Main()` hanterar kommandoradsargument som följande:

- Sökvägen till en katalog som innehåller MPEG-4-filer som ska skickas för gruppering. Alla `*.mp4` filer i katalogen och dess underkataloger skickas för gruppering.
- Du kan också en boolesk (SANT/FALSKT) flagga som anger om texten betyg ska genereras för rätt att kontrollera ljud.

Om det inte finns några kommandoradsargument finns `Main()` anrop `GetUserInputs()`. Den här metoden uppmanas användaren att ange sökvägen till en enda videofil och ange om ett betyg text ska genereras.

> [!NOTE]
> Konsolprogrammet använder den [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) generera betyg från den överförda video, ljud spår. Resultatet anges i WebVTT format. Mer information om det här formatet finns [Web Video spårar textformat](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Initiera och ProcessVideo metoder

Oavsett om programmets alternativ kom från kommandoraden eller interaktiv användarindata `Main()` nästa anrop `Initialize()` att skapa följande instanser:

|Klass|Beskrivning|
|-|-|
|`AMSComponent`|Komprimerar videofiler innan du skickar dem för gruppering.|
|`AMSconfigurations`|Gränssnitt för programmets konfigurationsdata finns i `App.config`.|
|`VideoModerator`| Ladda upp, kodning, kryptering och avbrottsmoderering med AMS SDK|
|`VideoReviewApi`|Hanterar video granskningar i tjänsten innehåll kontrollant|

Dessa klasser (aside från `AMSConfigurations`, vilket är enkla) beskrivs i detalj i kommande avsnitt i den här kursen.

Slutligen-filer har bearbetat en i taget genom att anropa `ProcessVideo()` för varje.

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


Den `ProcessVideo()` metoden är ganska enkelt. Följande åtgärder utförs i ordning:

- Komprimerar videon
- Överför videon till en Azure Media Services-tillgång
- Skapar ett AMS-jobb för att måttlig videon
- Skapar en video granska innehåll kontrollant

I följande avsnitt bör du tänka i detalj för enskilda processer som anropas av `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprimera videon

För att minimera nätverkstrafiken programmet konverterar videofiler till H.264 (MPEG-4 AVC)-format och skalas dem till en maximala bredd 640 bildpunkter. Angiven codec H.264 rekommenderas på grund av dess högeffektiv (komprimering kurs). Komprimeringen görs med hjälp av den kostnadsfria `ffmpeg` kommandoradsverktyg som ingår i den `Lib` för Visual Studio-lösning. Inkommande filer kan vara i ett format som stöds av `ffmpeg`, inklusive de vanligaste filformat och codec.

> [!NOTE]
> När du startar programmet med hjälp av kommandoradsalternativen kan ange du en katalog som innehåller videofiler skickas för gruppering. Alla filer i den här katalogen med det `.mp4` filnamnstillägget bearbetas. För att behandla andra filnamnstillägg, uppdatera det `Main()` metod i `Program.cs` att inkludera de önskade tillägg.

Koden som komprimerar en enda videofil är den `AmsComponent` klassen i `AMSComponent.cs`. Metoden som ansvarar för den här funktionen är `CompressVideo()`, visas här.

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

Kod som utför följande steg:

- Kontroller för att kontrollera konfigurationen i `App.config` innehåller alla nödvändiga data
- Kontroll av att den `ffmpeg` binära finns
- Skapar resulterande filnamn genom att lägga till `_c.mp4` till det grundläggande namnet på filen (som `Example.mp4`  ->  `E>xample_c.mp4`)
- Skapar en kommandoradssträng om du vill utföra konverteringen
- Startar en `ffmpeg` bearbeta med hjälp av kommandoraden
- Väntar på att videon som ska bearbetas

> [!NOTE]
> Om du vet att dina videor redan har komprimerats med H.264 och har rätt dimensioner, du kan skriva om `CompressVideo()` att hoppa över komprimeringen.

Metoden returnerar filnamnet för den komprimerade utdatafilen.

## <a name="uploading-and-moderating-the-video"></a>Ladda upp och kontrollera videon

Videon måste lagras i Azure Media Services innan den kan bearbetas av tjänsten innehåll måtta. Den `Program` klassen i `Program.cs` har en kort metod `CreateVideoStreamingRequest()` som returnerar ett objekt som representerar strömmande begäran används för att ladda upp videon.

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

Det resulterande `UploadVideoStreamRequest` objekt definieras i `UploadVideoStreamRequest.cs` (och dess överordnade `UploadVideoRequest`i `UploadVideoRequest.cs`). De här klasserna visas inte här. de korta och fungerar bara som förvaring komprimerade video data och information om den. En annan data endast klass `UploadAssetResult` (`UploadAssetResult.cs`) används för att lagra resultaten av överföringen. Nu är det möjligt att förstå dessa rader i `ProcessVideo()`:

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

Dessa rader utföra följande uppgifter:

- Skapa en `UploadVideoStreamRequest` att ladda upp den komprimerade videon
- Ange en begäran `GenerateVTT` flaggan om användaren har begärt en text betyg
- Anrop `CreateAzureMediaServicesJobToModerateVideo()` att utföra överföringen och ta emot resultatet

## <a name="deep-dive-into-video-moderation"></a>Ingående i video avbrottsmoderering

Metoden `CreateAzureMediaServicesJobToModerateVideo()` i `VideoModerator.cs`, som innehåller den största delen av koden som interagerar med Azure Media Services. Metodens källkoden visas i följande utdrag.

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

Den här koden utför följande uppgifter:

- Skapar ett AMS-jobb för bearbetning ska utföras
- Lägger till aktiviteter för kodning av videofil, kontrollera det och genererar en text betyg
- Skickar jobbet överföring av filen och början bearbetning
- Hämtar avbrottsmoderering resultat, text betyg (om så krävs) och annan information

## <a name="sample-video-moderation-response"></a>Video avbrottsmoderering exempelsvar

Resultatet av jobbet video avbrottsmoderering (se [video avbrottsmoderering quickstart](video-moderation-api.md) är en JSON-datastruktur som innehåller resultatet avbrottsmoderering. En detaljerad analys av fragment (bilderna) ingår i videon varje som innehåller händelser (klipp) med viktiga ramar som har flaggats för granskning i resultaten. Varje nyckelbilden beräknas genom att sannolikheten att den innehåller innehåll för vuxna eller dyr. I följande exempel visas en JSON-svar:

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

Ett skrivfel i ljuduppspelningen från videon är också genereras när den `GenerateVTT` flaggan anges.

> [!NOTE]
> Konsolprogrammet använder den [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) att generera betyg från den överförda video, ljud spåra. Resultatet anges i WebVTT format. Mer information om det här formatet finns [Web Video spårar textformat](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Skapa hr-i-the-loop-granskning

Processen avbrottsmoderering returnerar en lista över viktiga ramar från video, tillsammans med ett betyg av ljud-spår. Nästa steg är att skapa ett omdöme i verktyget innehåll kontrollant granska för mänsklig kontrollanter. Gå tillbaka till den `ProcessVideo()` metod i `Program.cs`, du se anropet till den `CreateVideoReviewInContentModerator()` metoden. Den här metoden finns i den `videoReviewApi` klassen, som är i `VideoReviewAPI.cs`, och visas här.

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
> Konsolprogrammet använder den [FFmpeg](https://ffmpeg.org/) biblioteket för att generera miniatyrbilder. Dessa miniatyrer (avbildningar) motsvarar ram tidsstämplar i den [video avbrottsmoderering utdata](#sample-video-moderation-response).

|Aktivitet|Metoder|Fil|
|-|-|-|
|Extrahera nyckeln ramar från videon och skapar miniatyrbilder av dem.|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Skanna text-utskrifter om det går att hitta vuxna eller dyr ljud|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Förbereda och skickar en begäran om video granska för mänsklig kontroll|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Video granska standardvyn

Följande skärmbild visar resultatet av föregående steg.

![Video granska standardvyn](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Betyg generation

Fram till nu har koden som visas i den här självstudiekursen fokuserar på visuellt innehåll. Granskning av tal innehåll är en separat och valfria process som använder ett betyg som genereras från ljuduppspelningen som tidigare nämnts kan. Det är dags nu för att ta en titt på hur texten betyg skapas och används i granskningsprocessen. Uppgiften genererar betyg hamnar den [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) service.

Programmet utför följande uppgifter:

|Aktivitet|Metoder|Fil|
|-|-|-|
|Bestämmer om betyg text ska genereras|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|I så fall, skicka ett skrivfel jobb som en del av avbrottsmoderering|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Hämta en lokal kopia av betyg|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaggan ramar av videon som innehåller olämpliga ljud|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Lägg till resultaten till granskningen|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfigurera aktivitet

Vi hoppar rätten till utskriftsjobbet skrivfel. `CreateAzureMediaServicesJobToModerateVideo()` (redan beskrivs) samtal `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurationen för aktiviteten betyg läsa från filen `MediaIndexerConfig.json` i lösningens `Lib` mapp. AMS tillgångar skapas för konfigurationsfilen och utdata från processen skrivfel. Denna uppgift skapar en betyg text från filen video, ljud spåra när AMS-jobbet körs.

> [!NOTE]
> Exempelprogrammet identifieras bara tal i amerikansk engelska.

### <a name="transcript-generation"></a>Betyg generation

Betyg publiceras som en AMS tillgång. Om du vill skanna betyg för olämpligt hämtar programmet tillgången från Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` anrop `GenerateTranscript()`, visas här om du vill hämta filen.

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
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Efter vissa nödvändiga AMS-installationen, hämta utförs genom att anropa `DownloadAssetToLocal()`, en allmän funktion som kopierar en AMS tillgång till en lokal fil.

## <a name="transcript-moderation"></a>Betyg avbrottsmoderering

Det är genomsöks och används i granskningen med betyg nära till hands. Skapa granskningen är faller `CreateVideoReviewInContentModerator()`, som anropar `GenerateTextScreenProfanity()` arbetet. Den här metoden anropar i sin tur `TextScreen()`, som innehåller de flesta funktionerna. 

`TextScreen()` Utför följande uppgifter:

- Parsa betyg gången tamps och bildtexter
- Skicka varje etikett för text avbrottsmoderering
- Flaggan inga ramar som kan vara stötande tal innehåll

Låt oss nu undersöka var dessa uppgifter i detalj:

### <a name="initialize-the-code"></a>Initiera koden

Först initiera alla variabler och samlingar.

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
    

### <a name="parse-the-transcript-for-captions"></a>Parsa betyg för beskrivningar

Därefter parsa VTT formaterad betyg för etiketter och tidsstämplar. Granska visas dessa beskrivningar på fliken betyg på skärmen video granska. Tidsstämplar används för att synkronisera beskrivningar med motsvarande bildrutor.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>Måttlig etiketter med tjänsten text avbrottsmoderering

Vi söker sedan parsade textmeddelanden med innehåll kontrollant text API.

> [!NOTE]
> Nyckeln för tjänsten för ditt innehåll kontrollant har en begäranden per hastighetsbegränsning för andra (RPS). Om du överskrider gränsen som utlöser ett undantag med 429 felkoden SDK. 
>
> En kostnadsfri nivå-nyckel har en gräns för överföringshastigheten en RPS.

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

### <a name="breaking-down-the-text-moderation-step"></a>Dela upp steget text avbrottsmoderering

`TextScreen()` är en betydande metod, så vi dela upp.

1. Metoden läser först filen betyg rad för rad. Ignorerar tomma rader och rader som innehåller en `NOTE` med en förtroende poäng. Extraheras tidsstämplar och textobjekt från den *ledtrådar* i filen. En stack representerar text från ljud spåra och innehåller start- och sluttider. En stack som börjar med i tidslinjen för stämpeln med strängen `-->`. Den följs av en eller flera rader med text.

1. Instanser av `CaptionScreentextResult` (definieras i `TranscriptProfanity.cs`) används för att lagra information som har parsats från varje stack.  När en ny tidsstämpel rad har identifierats eller en största längd på 1024 tecken har nåtts, en ny `CaptionScreentextResult` läggs till i `csrList`. 

1. Metoden skickar bredvid varje stack Text Avbrottsmoderering-API: et. Anropar båda `ContentModeratorClient.TextModeration.DetectLanguageAsync()` och `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, som definieras i den `Microsoft.Azure.CognitiveServices.ContentModerator` sammansättning. Om du vill undvika att hastighet begränsad metoden pausar för en andra innan du skickar varje stack.

1. När du har fått resultat från tjänsten Text Avbrottsmoderering analyserar metoden sedan dem för att se om de uppfyller förtroende tröskelvärden. Dessa värden är etablerade i `App.config` som `OffensiveTextThreshold`, `RacyTextThreshold`, och `AdultTextThreshold`. Slutligen lagras också stötande villkoren sig själva. Alla ramar inom tidsintervallet för det stack flaggas som innehåller stötande, dyr och/eller vuxna text.

1. `TextScreen()` Returnerar en `TranscriptScreenTextResult` -instans som innehåller text avbrottsmoderering resultatet från videon som helhet. Det här objektet innehåller flaggor och poäng för de olika typerna av olämpligt tillsammans med en lista över alla stötande termer. Anroparen, `CreateVideoReviewInContentModerator()`, anrop `UploadScreenTextResult()` bifoga den här informationen till granskningen så att den är tillgänglig för mänsklig granskare.
 
## <a name="video-review-transcript-view"></a>Video granska betyg vy

Följande skärmbild visar resultatet av betyg generation och avbrottsmoderering åtgärder.

![Video avbrottsmoderering betyg vy](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programmets utdata.

Följande visas kommandorad från programmet de olika aktiviteterna eftersom de slutförs. Dessutom finns avbrottsmoderering resultatet (i JSON-format) och tal betyg i samma katalog som de ursprungliga videofilerna.

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

[Hämta Visual Studio-lösningen](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) och exempel på filer och bibliotek som krävs för den här kursen och kom igång på din integrering.
