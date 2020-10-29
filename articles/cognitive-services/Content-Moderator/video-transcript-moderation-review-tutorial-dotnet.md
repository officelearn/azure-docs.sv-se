---
title: 'Självstudie: Moderera videor och avskrifter i .NET – Content Moderator'
titleSuffix: Azure Cognitive Services
description: I den här självstudien visas hur du skapar en komplett modereringslösning för video och avskrift med maskinassisterad moderering och HITL-granskning (human-in-the-loop).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1648bd9a073bca696299e9ed703536db745e7edb
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912845"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Självstudie: Moderering av video och avskrift

I den här självstudien får du lära dig hur du skapar en komplett video-och avskrifts redigerings lösning med hjälp av dator redigering och granskning av mänsklig granskning.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Komprimera indatavideo(r) för snabbare bearbetning
> - Moderera videon för att få bilder och bildrutor med information
> - Använda bildrutans tidsstämplar till att skapa miniatyrer (bilder)
> - Skicka tidsstämplar och miniatyrer för att skapa videogranskningar
> - Konvertera videons tal till text (avskrift) med Media Indexer-API:n
> - Moderera avskriften med textändringstjänsten
> - Lägga till den modererade avskriften i videogranskningen

## <a name="prerequisites"></a>Förutsättningar

- Registrera dig för webbplatsen för [Content moderator gransknings verktyg](https://contentmoderator.cognitive.microsoft.com/) och skapa anpassade taggar. Se [använda Taggar](./review-tool-user-guide/configure.md#tags) om du behöver hjälp med det här steget.

    ![skärm bild av anpassade taggar för video moderator](images/video-tutorial-custom-tags.png)
- Om du vill köra exempel programmet behöver du ett Azure-konto, en Azure Media Services resurs, en Azure Content Moderator-resurs och Azure Active Directory autentiseringsuppgifter. Instruktioner för hur du hämtar dessa resurser finns i API-guiden för [Videokontrollanter](video-moderation-api.md) .
- Hämta [video gransknings konsol programmet](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) från GitHub.

## <a name="enter-credentials"></a>Ange autentiseringsuppgifter

Redigera `App.config` filen och Lägg till Active Directory klient namn, tjänst slut punkter och prenumerations nycklar som anges av `#####` . Du behöver följande information:

|Nyckel|Beskrivning|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Slutpunkt för API:n till Azure Media Services (AMS)|
|`ClientSecret`|Prenumerationsnyckel för Azure Media Services|
|`ClientId`|Klient-ID för Azure Media Services|
|`AzureAdTenantName`|Active Directory-klientorganisationens namn som representerar din organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Prenumerationsnyckel för gransknings-API:n i Content Moderator|
|`ContentModeratorApiEndpoint`|Slutpunkt för Content Moderator-API:n|
|`ContentModeratorTeamId`|Team-ID för Content Moderator|

## <a name="examine-the-main-code"></a>Granska huvud koden

Klassen `Program` i `Program.cs` är den främsta startpunkten till videoändringsprogrammet.

### <a name="methods-of-program-class"></a>Metoder för program klass

|Metod|Beskrivning|
|-|-|
|`Main`|Parsar kommandoraden, samlar indata från användaren och påbörjar bearbetningen.|
|`ProcessVideo`|Komprimerar, laddar upp, modererar och skapar videogranskningar.|
|`CreateVideoStreamingRequest`|Skapar en ström som laddar upp en video|
|`GetUserInputs`|Samlar indata från användaren, vilket används när det inte finns några kommandoradsalternativ|
|`Initialize`|Initierar de objekt som behövs för modereringsprocessen|

### <a name="the-main-method"></a>Main-metoden

`Main()` är där körningen startar, så det där du kan börja förstå videoändringsprocessen.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` hanterar följande kommandoradsargument:

- Sökvägen till en katalog med MPEG-4-videofiler som ska skickas för moderering. Alla `*.mp4`-filer i den här katalogen och dess underkataloger skickas för moderering.
- En boolesk flagga (sant/falskt) som anger om textavskrifterna ska genereras i syfte att moderera ljud kan också visas.

Om det inte finns några argument på kommandoraden anropar `Main()``GetUserInputs()`. Den här metoden uppmanar användaren att ange sökvägen till en enskild videofil och att ange om en textavskrift ska genereras.

> [!NOTE]
> Konsol programmet använder [Azure Media INDEXER API](../../media-services/previous/legacy-components.md) för att generera avskrifter från den uppladdade videons ljud spår. Resultaten anges i WebVTT-format. Mer information om det här formatet finns i [Web Video Text Tracks-format](https://developer.mozilla.org/docs/Web/API/WebVTT_API).

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

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

`ProcessVideo()`-metoden är ganska enkel. Följande åtgärder utförs i ordning:

- Komprimerar videon
- Laddar upp videon till en Azure Media Services-tillgång
- Skapar ett AMS-jobb för att moderera videon
- Skapar en videogranskning i Content Moderator

I följande avsnitt beskrivs i detalj några av de enskilda processer som anropas av `ProcessVideo()`. 

## <a name="compress-the-video"></a>Komprimera videon

För att minimera nätverkstrafiken konverterar programmet videofiler till H.264-format (MPEG-4 AVC) och skalar dem till en maximal bredd på 640 bildpunkter. En H.264-codec rekommenderas tack vare dess höga effektivitet (komprimeringsgrad). Komprimeringen görs med hjälp av det kostnadsfria `ffmpeg`-kommandoradsverktyget, som finns i mappen `Lib` i Visual Studio-lösningen. Indatafilerna kan ha de format som stöds av `ffmpeg`, inklusive vanliga videofilformat och codecs.

> [!NOTE]
> När programmet startas med kommandoradsalternativen, anger du en katalog som innehåller de videofiler som ska skickas för moderering. Alla filer i den här katalogen som har filnamnstillägget `.mp4` bearbetas. Om du vill bearbeta andra filnamnstillägg uppdaterar du `Main()`-metoden i `Program.cs` och inkluderar önskade tillägg.

Koden som komprimerar en enskild videofil finns i `AmsComponent`-klassen i `AMSComponent.cs`. Metoden som ansvarar för den här funktionen är `CompressVideo()`, vilket visas här.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

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

## <a name="upload-and-moderate-the-video"></a>Ladda upp och måttlig videon

Videon måste lagras i Azure Media Services innan den kan bearbetas av innehållsändringstjänsten. `Program`-klassen i `Program.cs` har en kort metod för `CreateVideoStreamingRequest()`. Den returnerar ett objekt som representerar den strömmande begäran som användes för att ladda upp videon.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Det resulterande objektet `UploadVideoStreamRequest` definieras i `UploadVideoStreamRequest.cs` (och dess överordnade, `UploadVideoRequest`, i `UploadVideoRequest.cs`). De här klasserna visas inte här. De är korta och fungerar bara som lagring av komprimerade videodata och dess information. En annan klass med enbart data, `UploadAssetResult` (`UploadAssetResult.cs`) används för att lagra resultatet av uppladdningen. Nu är det lättare att förstå dessa rader i `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Raderna utför följande uppgifter:

- Skapa en `UploadVideoStreamRequest` för att ladda upp den komprimerade videon
- Ange `GenerateVTT`-flaggan för begäran om användaren har begärt en textavskrift
- Anropar `CreateAzureMediaServicesJobToModerateVideo()` för att genomföra uppladdningen och ta emot resultatet

## <a name="examine-video-moderation-code"></a>Granska videokontrollantens kod

Metoden `CreateAzureMediaServicesJobToModerateVideo()` finns i `VideoModerator.cs`, som innehåller den stora mängd kod som interagerar med Azure Media Services. Källkoden för metoden visas i följande extrahering.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Koden utför följande uppgifter:

- Skapar ett AMS-jobb för den bearbetning som ska utföras
- Lägger till uppgifter för att koda videofilen, moderera den och generera en textavskrift
- Skickar jobbet, laddar upp filen och påbörjar bearbetningen
- Hämtar modereringsresultaten, textavskriften (om det har begärts) och övrig information

## <a name="sample-video-moderation-output"></a>Exempel på video moderator

Resultatet från videoändringsjobbet (se [snabbstarten för videoändringar](video-moderation-api.md)) är en JSON-datastruktur som innehåller modereringsresultaten. En detaljerad analys av fragmenten (videobilderna) ingår i resultaten, där vart och ett innehåller händelser (klipp) med nyckelbildrutor som har flaggats för granskning. Varje nyckelbildruta bedöms med sannolikheten att den innehåller vuxet eller olämpligt innehåll. I följande exempel visas ett JSON-svar:

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

En avskrift av ljudet från videon genereras också när `GenerateVTT`-flaggan har angetts.

> [!NOTE]
> Konsol programmet använder [Azure Media INDEXER API](../../media-services/previous/legacy-components.md) för att generera avskrifter från den uppladdade videons ljud spår. Resultaten anges i WebVTT-format. Mer information om det här formatet finns i [Web Video Text Tracks-format](https://developer.mozilla.org/docs/Web/API/WebVTT_API).

## <a name="create-a-human-review"></a>Skapa en mänsklig granskning

Modereringsprocessen returnerar en lista med nyckelbildrutor från videon, tillsammans med avskrifter av dess ljudspår. Nästa steg är att skapa en granskning i Content Moderator gransknings verktyg för mänskliga moderatorer. När du går tillbaka till `ProcessVideo()`-metoden i `Program.cs`, ser du anropet till `CreateVideoReviewInContentModerator()`-metoden. Den här metoden finns i `videoReviewApi`-klassen, som är i `VideoReviewAPI.cs`, och visas här.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` anropar flera andra metoder för att utföra följande uppgifter:

> [!NOTE]
> Konsolprogrammet använder [FFmpeg](https://ffmpeg.org/)-biblioteket till att generera miniatyrer. De här miniatyrerna (bilder) motsvarar bildrutans tidsstämplar i videoändringens utdata.

|Uppgift|Metoder|Fil|
|-|-|-|
|Extraherar nyckelbildrutorna från videon och skapar miniatyrbilder av dem|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Genomsöker textavskriften, om det finns någon, för att hitta vuxet eller olämpligt ljud|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Förbereder och skickar en begäran om videogranskning för manuell kontroll|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

Följande skärmbild visar resultatet av föregående steg.

![Standardvyn för videogranskning](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Bearbeta avskriften

Fram tills nu har koden som visas i den här självstudien fokuserat på visuellt innehåll. Granskning av talat innehåll är en separat och en valfri process som använder en avskrift som genereras av ljudet. Det är nu dags att ta en titt på hur textavskrifter skapas och används i granskningsprocessen. Uppgiften att generera avskriften finns i [Azure Media Indexer](../../media-services/previous/media-services-index-content.md)-tjänsten.

Programmet utför följande uppgifter:

|Uppgift|Metoder|Fil|
|-|-|-|
|Bestämmer om textavskrifter ska genereras|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|I dessa fall skickas ett avskriftsjobb som en del av modereringen|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Hämtar en lokal kopia av avskriften|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaggar bildrutor i videon som innehåller olämpligt ljud|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Lägger till resultaten i granskningen|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Uppgiftskonfiguration

Låt oss gå direkt till att skicka avskriftsjobbet. `CreateAzureMediaServicesJobToModerateVideo()` (beskrevs tidigare) anropar `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Konfigurationen för avskriftsuppgiften läses från filen `MediaIndexerConfig.json` i lösningens `Lib`-mapp. AMS-tillgångar skapas för konfigurationsfilen och för utdatan från avskriftsprocessen. När AMS-jobbet körs skapar uppgiften textavskrifter från videofilens ljudspår.

> [!NOTE]
> Exempelprogrammet kan endast identifiera tal på (amerikansk) engelska.

### <a name="transcript-generation"></a>Avskriftsgenerering

Avskriften publiceras som en AMS-tillgång. Om du vill genomsöka avskriften efter stötande innehåll, hämtar programmet tillgången från Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` anropar `GenerateTranscript()`, vilket visas här, för att hämta filen.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Efter viss nödvändig AMS-konfiguration utförs den faktiska hämtningen genom att `DownloadAssetToLocal()` anropas. Det är en generisk funktion som kopierar en AMS-tillgång till en lokal fil.

## <a name="moderate-the-transcript"></a>Måttlig avskrift

När avskriften har erhållits genomsöks den och används i granskningen. Att skapa granskningen ingår i `CreateVideoReviewInContentModerator()`, som anropar `GenerateTextScreenProfanity()` för att göra jobbet. Metoden anropar i sin tur `TextScreen()`, som innehåller de flesta funktionerna.

`TextScreen()` utför följande uppgifter:

- Parsar avskriften för tidstämplar och bildtexter
- Skickar varje bildtext för textändring
- Flaggar alla bildrutor som kan innehålla stötande talat innehåll

Låt oss nu undersöka var och en av dessa uppgifter i detalj:

### <a name="initialize-the-code"></a>Initiera koden

Initiera först alla variabler och samlingar.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Parsa avskriften för bildtexter

Därefter parsas den VTT-formaterade avskriften för bildtexter och tidsstämplar. Gransknings verktyget visar de här rubrikerna på fliken avskrift på video gransknings skärmen. Tidsstämplarna används till att synkronisera bildtexterna med motsvarande bildrutor.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderera bildtexterna med textändringstjänsten

Därefter genomsöker vi de parsade bildtexterna med text-API:n i Content Moderator.

> [!NOTE]
> Din tjänstnyckel för Content Moderator har en hastighetsgräns för begäranden per sekund (RPS). Om du överskrider gränsen utlöser SDK:n ett undantag med felkoden 429.
>
> En nyckel på den kostnadsfria nivån har en gräns på en RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Text redigerings analys

`TextScreen()` är en omfattande metod, så vi delar upp den.

1. Metoden läser först avskriftsfilen rad för rad. Den ignorerar tomma rader och rader som innehåller en `NOTE` med en förtroendepoäng. Den extraherar tidsstämplar och textobjekt från *stackikonerna* i filen. En stackikon representerar text från ljudspåret och innehåller start- och sluttider. En stackikon börjar med tidsstämpelraden med strängen `-->`. Den följs av en eller flera rader med text.

1. Instanser av `CaptionScreentextResult` (definieras i `TranscriptProfanity.cs`) används för att lagra information som har parsats från varje stackikon.  När en ny tidsstämpelrad har identifierats, eller en maximal längd på 1 024 tecken har uppnåtts, läggs en ny `CaptionScreentextResult` till i `csrList`. 

1. Metoden skickar därefter varje stackikon till textändrings-API:n. Den anropar både `ContentModeratorClient.TextModeration.DetectLanguageAsync()` och `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, som definieras i sammansättningen `Microsoft.Azure.CognitiveServices.ContentModerator`. Om du inte vill bli begränsad pausar metoden i en sekund innan varje stackikon skickas.

1. När du fått resultaten från textändringstjänsten analyserar metoden dem för att se om de uppfyller förtroendetröskeln. Dessa värden är upprättade i `App.config` som `OffensiveTextThreshold`, `RacyTextThreshold` och `AdultTextThreshold`. Slutligen lagras också de stötande termerna. Alla bildrutor inom stackikonens tidsintervall är flaggade att innehålla stötande, olämplig och/eller vuxen text.

1. `TextScreen()` returnerar en `TranscriptScreenTextResult`-instans som innehåller textändringsresultatet från videon som helhet. Det här objektet innehåller flaggor och resultat för de olika typerna av stötande innehåll, tillsammans med en lista över alla stötande termer. Anroparen, `CreateVideoReviewInContentModerator()`, anropar `UploadScreenTextResult()` för att koppla den här informationen till granskningen så att den är tillgänglig för manuell granskning.

Följande skärmbild visar resultatet från avskriftsgenereringen och modereringen.

![Avskriftsvisning för videoändring](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programmets utdata

Följande kommandorads utdata från programmet visar de olika uppgifterna när de har slutförts. Dessutom finns modereringsresultatet (i JSON-format) och talavskriften i samma katalog som de ursprungliga videofilerna.

```console
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
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du ett program som förändrar video innehåll, &mdash; inklusive avskrifts innehåll &mdash; och skapar granskningar i gransknings verktyget. Nu ska du läsa mer om hur du kan få mer information om videoinspelning.

> [!div class="nextstepaction"]
> [Videomoderering](./video-moderation-human-review.md)