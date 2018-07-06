---
title: Azure Content Moderator – måttlig videor och betyg i .NET | Microsoft Docs
description: Hur du använder Content Moderator för att ändra videor och betyg i .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865479"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Självstudiekurs för moderering av video och avskrift

Content Moderator video API: er kan du ändra videor och skapa video granskningar i Verktyg för mänsklig granskning. 

Det här detaljerade kursen går vi igenom att förstå hur du skapar en komplett video och avskrift moderering lösning med datorstödd moderering och human-i-the-loop granskning skapas.

Ladda ned den [C#-konsolprogram](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) för den här självstudiekursen. Konsolprogrammet använder SDK och relaterade paket för att utföra följande uppgifter:

- Komprimera inkommande video(s) för snabbare bearbetning
- Måttlig videon för att få skärmbilder och ramar med insikter
- Använda ramens tidsstämplarna för att skapa miniatyrer (avbildningar)
- Skicka in tidsstämplar och thumbnails för att skapa video granskningar
- Omvandla video tal till text (avskrift) med Media Indexer API
- Måttlig avskrift med tjänsten för moderering av text
- Lägg till kontrollerad avskriften till video granskningen

## <a name="sample-program-outputs"></a>Exempelprogrammet matar ut

Innan du fortsätter, ska vi titta på följande exempel utdata från programmet:

- [Konsolens utdata](#program-output)
- [Video granskning](#video-review-default-view)
- [Visa avskrift](#video-review-transcript-view)

## <a name="prerequisites"></a>Förutsättningar

1. Registrera dig för den [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) webbplats och [skapa anpassade taggar](Review-Tool-User-Guide/tags.md) som C#-konsolprogram tilldelar från i koden. Följande skärmbild visar anpassade taggar.

  ![Videomoderering anpassade taggar](images/video-tutorial-custom-tags.png)

1. Om du vill köra exempelprogrammet, behöver du ett Azure-konto och ett Azure Media Services-konto. Du måste även åtkomst till den privata förhandsgranskningen för Content Moderator. Slutligen behöver Azure Active Directory-autentiseringsuppgifter. Mer information om hur du skaffar den här informationen finns i [Video-API för Bildmoderering snabbstarten](video-moderation-api.md).

1. Redigera filen `App.config` och Lägg till Active Directory-klientnamn, Tjänsteslutpunkter och prenumerationsnycklar indikeras av `#####`. Du behöver följande information:

|Nyckel|Beskrivning|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Slutpunkt för API: et för Azure Media Services (AMS)|
|`ClientSecret`|Prenumerationsnyckel för Azure Media Services|
|`ClientId`|Klient-ID för Azure Media Services|
|`AzureAdTenantName`|Active Directory-klientnamn som representerar din organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Prenumerationsnyckel för Content Moderator granskar API|
|`ContentModeratorApiEndpoint`|Slutpunkt för API för Content Moderator|
|`ContentModeratorTeamId`|Content moderator lag-ID|

## <a name="getting-started"></a>Komma igång

Klassen `Program` i `Program.cs` är den viktigaste startpunkten till programmet videomoderering.

### <a name="methods-of-class-program"></a>Metoderna i klassen Program

|Metod|Beskrivning|
|-|-|
|`Main`|Parsar kommandoraden, samlar in indata från användaren och påbörjar bearbetningen av.|
|`ProcessVideo`|Komprimerar, laddar upp, ändrar och skapar video granskningar.|
|`CreateVideoStreamingRequest`|Skapar en ström för att ladda upp en video|
|`GetUserInputs`|Samlar in indata från användaren; används när det finns inga kommandoradsalternativ|
|`Initialize`|Initierar objekt som behövs för moderering av processen|

### <a name="the-main-method"></a>Main-metoden

`Main()` är där körningen startar, så det är ställe att börja förstå videomoderering-processen.

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

- Sökvägen till en katalog som innehåller MPEG-4-filer som ska skickas för moderering. Alla `*.mp4` filer i den här katalogen och dess underkataloger skickas för moderering.
- Du kan också en boolesk (SANT/FALSKT) flagga som anger om texten avskrifter ska genereras i syfte att rätt att kontrollera ljud.

Om inga argument på kommandoraden finns `Main()` anrop `GetUserInputs()`. Den här metoden uppmanar användaren att ange sökvägen till en enda videofil och att ange om en textavskrift för ska genereras.

> [!NOTE]
> Konsolprogrammet använder den [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) generera avskrifter från överförd video ljudspår. Resultaten finns i WebVTT-format. Mer information om det här formatet finns i [Web Video spårar textformat](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Initiera och ProcessVideo metoder

Oavsett om programmets alternativ kom från kommandoraden eller interaktiva användarindata `Main()` nästa anrop `Initialize()` att skapa följande instanser:

|Klass|Beskrivning|
|-|-|
|`AMSComponent`|Komprimerar videofiler innan du skickar dem för moderering.|
|`AMSconfigurations`|Gränssnitt för programmets konfigurationsdata finns i `App.config`.|
|`VideoModerator`| Ladda upp, kodning, kryptering och bildmoderering med hjälp av AMS SDK|
|`VideoReviewApi`|Hanterar video granskningar i Content Moderator-tjänsten|

De här klasserna (aside från `AMSConfigurations`, vilket är enkelt) beskrivs i detalj i kommande avsnitt i den här självstudien.

Slutligen videofilerna är bearbetade en i taget genom att anropa `ProcessVideo()` för var och en.

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
- Laddar upp videon till en tillgång med Azure Media Services
- Skapar ett AMS-jobb för att ändra videon
- Skapar en granskning av video i Content Moderator

I följande avsnitt tänka på i detalj några av de enskilda processer som anropas av `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprimering av videon

Om du vill minimera nätverkstrafiken programmet konverterar videofiler till H.264 (MPEG-4 AVC)-format och kan skalas upp dem till en maximal bredd 640 bildpunkter. H.264-codec rekommenderas på grund av dess hög effektivitet (komprimering pris). Komprimeringen görs med hjälp av den kostnadsfria `ffmpeg` kommandoradsverktyg, som ingår i den `Lib` mappen i Visual Studio-lösningen. Indatafilerna kan vara alla format som stöds av `ffmpeg`, inklusive de vanligaste videofilformat och -codec.

> [!NOTE]
> När programmet startas med kommandoradsalternativ, anger du en katalog som innehåller videofiler som ska skickas för moderering. Alla filer i den här katalogen som har den `.mp4` filnamnstillägget bearbetas. För att bearbeta andra filnamnstillägg, uppdatera den `Main()` -metod i `Program.cs` att inkludera de önskade tillägg.

Koden som komprimerar en video-fil är den `AmsComponent` klassen i `AMSComponent.cs`. Metoden som ansvarar för den här funktionen är `CompressVideo()`, visas här.

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

Koden utför följande steg:

- Kontroller för att kontrollera konfigurationen i `App.config` innehåller alla nödvändiga data
- Kontroller för att se till att den `ffmpeg` binära finns
- Skapar utdatafilnamnet genom att lägga till `_c.mp4` till det grundläggande namnet på filen (till exempel `Example.mp4`  ->  `E>xample_c.mp4`)
- Skapar en kommandoradssträng för att utföra konverteringen
- Startar en `ffmpeg` bearbeta från kommandoraden
- Väntar på videon som ska bearbetas

> [!NOTE]
> Om du vet att dina videor som redan har komprimerats med H.264 och har lämpliga dimensioner, kan du skriva om `CompressVideo()` att hoppa över komprimeringen.

Metoden returnerar filnamnet på komprimerade utdatafilen.

## <a name="uploading-and-moderating-the-video"></a>Ladda upp och kontrollera videon

Videon måste lagras i Azure Media Services innan de kan bearbetas av tjänsten innehållsmoderering. Den `Program` klassen i `Program.cs` har en kort metod `CreateVideoStreamingRequest()` som returnerar ett objekt som representerar strömmande begäran används för att ladda upp videon.

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

Den resulterande `UploadVideoStreamRequest` objektet definieras i `UploadVideoStreamRequest.cs` (och dess överordnade `UploadVideoRequest`i `UploadVideoRequest.cs`). De här klasserna visas inte här. de har för lite och fungerar bara som förvaring komprimerade data som video och information om den. En annan endast klass `UploadAssetResult` (`UploadAssetResult.cs`) används för att lagra resultatet av uppladdningen. Nu är det möjligt att förstå dessa rader i `ProcessVideo()`:

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
- Ange begärandets `GenerateVTT` flaggan om användaren har begärt en textavskrift för
- Anrop `CreateAzureMediaServicesJobToModerateVideo()` att genomföra överföringen och ta emot resultatet

## <a name="deep-dive-into-video-moderation"></a>Djupdykning i videomoderering

Metoden `CreateAzureMediaServicesJobToModerateVideo()` i `VideoModerator.cs`, som innehåller stora mängd kod som interagerar med Azure Media Services. Källkoden för den metoden illustreras i följande extrahera.

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

- Skapar ett AMS-jobb för bearbetning som ska utföras
- Lägger till aktiviteter för att koda videofilen, kontrollera den och generera en textavskrift för
- Skickar jobbet, ladda upp fil- och början bearbetning
- Hämtar resultaten, text avskriften (om så krävs) och annan information

## <a name="sample-video-moderation-response"></a>Videomoderering exempelsvar

Resultatet av jobbet videomoderering (se [videomoderering snabbstarten](video-moderation-api.md) är en JSON-datastruktur som innehåller resultaten. En detaljerad analys av fragment (skärmbilder) ingår i videon, varje som innehåller händelser (klipp) med viktiga ramar som har flaggats för granskning i resultaten. Varje nyckel ram beräknas genom att sannolikheten att den innehåller vuxet eller olämpligt innehåll. I följande exempel visar ett JSON-svar:

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

En avskrift ljudet från videon är också genereras när den `GenerateVTT` flagga har angetts.

> [!NOTE]
> Konsolprogrammet använder den [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) generera avskrifter från överförd video ljudspår. Resultaten finns i WebVTT-format. Mer information om det här formatet finns i [Web Video spårar textformat](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Skapa human-i-the-loop-granskning

Moderering processen returnerar en lista över viktiga ramar från videon, tillsammans med avskrifter av dess ljudspår. Nästa steg är att en granskning skapas i granskningsverktyget Content Moderator för mänskliga moderatorer. Gå tillbaka till den `ProcessVideo()` -metod i `Program.cs`, du se anropet till den `CreateVideoReviewInContentModerator()` metoden. Den här metoden finns i den `videoReviewApi` klassen, som är i `VideoReviewAPI.cs`, och visas här.

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
> Konsolprogrammet använder den [FFmpeg](https://ffmpeg.org/) bibliotek för att generera miniatyrer. De här miniatyrbilderna (avbildningar) motsvarar ramens tidsstämplar i den [videomoderering utdata](#sample-video-moderation-response).

|Aktivitet|Metoder|Fil|
|-|-|-|
|Extrahera nyckeln ramar från videon och skapar miniatyrbilder av dem.|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Genomsök textavskrift om det finns att hitta vågat eller för ljud|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Förbereder och skickar en begäran för video granskning för mänskliga granskning|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Standardvyn för video granskning

Följande skärmbild visar resultatet av föregående steg.

![Standardvyn för video granskning](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Avskriften generation

Fram till nu har koden som visas i den här självstudien fokuserar på visuellt innehåll. Granskning av talat innehåll är en separat och en valfri process som som tidigare nämnts använder en avskrift som genereras av ljudet. Det är dags nu för att ta en titt på hur text avskrifter skapas och används i granskningsprocessen. Uppgiften att generera avskriften faller till den [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) service.

Programmet utför följande uppgifter:

|Aktivitet|Metoder|Fil|
|-|-|-|
|Ta reda på om avskrifter text som ska genereras|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|I så, fall skicka en avskrift jobb som en del av moderering|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Hämta en lokal kopia av avskriften|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaggan bildrutor i videon som innehåller olämplig ljud|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Lägg till resultaten granskningen|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Uppgiftskonfiguration

Låt oss sätta i utskriftsjobbet avskrift. `CreateAzureMediaServicesJobToModerateVideo()` (beskrevs tidigare) anrop `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurationen för aktiviteten avskrift läses från filen `MediaIndexerConfig.json` i lösningens `Lib` mapp. AMS-tillgångar skapas för konfigurationsfilen och utdata från processen avskrift. När AMS-jobbet körs skapar den här uppgiften avskrifter text från filen video ljudspår.

> [!NOTE]
> Exempelprogrammet kan identifiera tal i engelska (USA) endast.

### <a name="transcript-generation"></a>Avskriften generation

Avskriften publiceras som en AMS tillgång. Om du vill genomsöka avskrift för stötande innehåll, hämtar programmet tillgången från Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` anrop `GenerateTranscript()`visas här om du vill hämta filen.

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

Efter vissa nödvändiga AMS-konfigurationen måste den faktiska hämtas genom att anropa `DownloadAssetToLocal()`, en allmän funktion som kopierar en AMS tillgång till en lokal fil.

## <a name="transcript-moderation"></a>Moderering av avskrift

Det är genomsöks och används i granskningen med avskrift nära till hands. Är att skapa granskningen omfattas av `CreateVideoReviewInContentModerator()`som anrop `GenerateTextScreenProfanity()` göra jobbet. I sin tur anropar den här metoden `TextScreen()`, som innehåller de flesta funktionerna. 

`TextScreen()` Utför följande uppgifter:

- Parsa avskriften för tid tamps och bildtexter
- Skicka varje beskrivningen för textmoderering
- Flagga alla ramar som kan ha stötande talat innehåll

Låt oss nu undersöka var och en uppgift i detalj:

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
    

### <a name="parse-the-transcript-for-captions"></a>Parsa avskriften bildtexter

Därefter parsa VTT formaterade avskriften för undertexter och tidsstämplar. Granskningsverktyget visar dessa rubriker på fliken avskrift på skärmen video granskning. Tidsstämplarna används för att synkronisera undertexter med de motsvarande bildrutorna.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>Måttlig undertexter med tjänsten för moderering av text

Nu ska skanna vi Analyserad text undertexter med Content Moderator-API för textöversättning.

> [!NOTE]
> Din nyckel för Content Moderator-tjänsten har en begäranden per sekund (RPS) hastighetsbegränsning. Om du överskrider gränsen utlöser ett undantag med en 429 felkod i SDK: N. 
>
> En nyckel för kostnadsfria nivån har en hastighetsbegränsning för en RPS.

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

### <a name="breaking-down-the-text-moderation-step"></a>Bryta ned steget för moderering av text

`TextScreen()` är en omfattande metod, så vi dela upp.

1. Metoden läser först filen avskrift rad för rad. Ignorerar tomma rader och rader som innehåller en `NOTE` med ett förtroenderesultat. Det extraherar tidsstämplar och textobjekt från den *tips* i filen. En stack-ikon som representerar text från ljudspåret och innehåller start- och sluttider. En stack-ikon som börjar med tidslinjen för stämpel med strängen `-->`. Den följs av en eller flera rader med text.

1. Instanser av `CaptionScreentextResult` (definieras i `TranscriptProfanity.cs`) används för att lagra information som har parsats från varje stack.  När en ny stämpel tidslinjen har identifierats eller en största längd på 1024 tecken har uppnåtts, en ny `CaptionScreentextResult` läggs till i `csrList`. 

1. Metoden skickar bredvid varje stack-ikon för Moderering av Text-API. Den anropar båda `ContentModeratorClient.TextModeration.DetectLanguageAsync()` och `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, som definieras i den `Microsoft.Azure.CognitiveServices.ContentModerator` sammansättningen. Om du vill undvika att rate-limited, metoden pausar i en sekund innan du skickar varje stack.

1. När du får resultat från tjänsten Textmoderering analyserar metoden sedan dem för att se om de uppfyller förtroende tröskelvärden. Dessa värden är etablerade i `App.config` som `OffensiveTextThreshold`, `RacyTextThreshold`, och `AdultTextThreshold`. Slutligen lagras också stötande villkoren själva. Alla ramar inom den stack tidsintervallet är flaggade som som innehåller stötande, olämpligt och/eller vuxet text.

1. `TextScreen()` Returnerar en `TranscriptScreenTextResult` instans som innehåller text moderering resultatet från videon som helhet. Det här objektet innehåller flaggor och poängsätter för de olika typerna av stötande innehåll, tillsammans med en lista över alla stötande termer. Anroparen, `CreateVideoReviewInContentModerator()`, anrop `UploadScreenTextResult()` att koppla den här informationen till granskningen så att den är tillgänglig för mänsklig granskare.
 
## <a name="video-review-transcript-view"></a>Video granska avskrift vy

Följande skärmbild visar resultatet av avskriften generation och moderering steg.

![Videomoderering avskrift vy](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programutdata

Följande kommandorad utdata från programmet visar de olika uppgifterna som de har slutförts. Dessutom finns moderering resultatet (i JSON-format) och tal avskriften i samma katalog som de ursprungliga videofilerna.

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

[Ladda ned Visual Studio-lösningen](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) och exempel på filer och bibliotek som krävs för den här självstudiekursen och komma igång med din integrering.
