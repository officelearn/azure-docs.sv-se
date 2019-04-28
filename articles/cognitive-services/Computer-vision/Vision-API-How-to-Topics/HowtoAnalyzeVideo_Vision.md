---
title: 'Exempel: Videoanalys i realtid – Visuellt innehåll'
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att utföra analyser nära realtid på bildrutor som kommer från direktuppspelad video med hjälp av API för visuellt innehåll.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: feafb983a7b9e4aea6091753842b03a65ccd3ca5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293761"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Analysera videor i realtid

Den här guiden visar hur du utför analyser nära realtid på bildrutor som kommer från direktuppspelad video. De grundläggande komponenterna i ett sådant system är:

- Hämta bilder från en videokälla
- Välja vilka bildrutor som ska analyseras
- Skicka dessa bildrutor till API:et
- Använda varje analysresultat som returneras från API-anropet

De här exemplen är skrivna i C# och koden finns på GitHub här: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Metoden

Det finns flera sätt att lösa problemet med att köra analyser nära realtid för direktuppspelad video. Vi ska börja med att beskriva tre metoder med ökande komplexitet.

### <a name="a-simple-approach"></a>En enkel metod

Den enklaste designen för ett analyssystem nära realtid är en oändlig loop där vi tar en bildruta i varje iteration, analyserar den och sedan använder resultatet:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Om analysen består av en förenklad algoritm för klientsidan, skulle den här metoden vara lämplig. Men när analysen sker i molnet innebär svarstiden att ett API-anrop kan ta flera sekunder, då vi inte samlar in bilder och vår tråd i stort sett inte gör någonting. Vår högsta bildfrekvens är begränsad av svarstiden för API-anrop.

### <a name="parallelizing-api-calls"></a>Parallellisera API-anrop

Även om en enkel entrådig loop passar för en enkel algoritm från klientsidan passar den inte bra ihop med den svarstid som är involverad i molnets API-anrop. Lösning på problemet är att tillåta att långvariga API-anrop körs parallellt med insamling av bildrutor. I C#, kan vi åstadkomma detta med uppgiftsbaserad parallellitet, till exempel:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Den här metoden startar varje analys i en separat uppgift som kan köras i bakgrunden medan vi fortsätter insamlingen av nya bildrutor. Den förhindrar blockering av huvudtråden medan systemet väntar på att ett API-anrop ska returneras, men vi har gått miste om några av de garantier som den enkla versionen tillhandahöll – flera API-anrop kan ske parallellt och resultatet kan returneras i fel ordning. Denna metod kan även leda till att flera trådar skickas till funktionen ConsumeResult() samtidigt, vilket kan vara farligt om funktionen inte är trådsäker. Slutligen håller den här enkla koden inte reda på de uppgifter som skapas, så undantag försvinner tyst. Därför är den sista komponenten som vi ska lägga till en ”konsumenttråd” som spårar analysuppgifterna, skapar undantag, avslutar långvariga uppgifter och ser till att resultaten förbrukas i rätt ordning, ett i taget.

### <a name="a-producer-consumer-design"></a>En producent-konsument-design

I vårt slutgiltiga ”producent-konsument-system” har vi en producenttråd som starkt liknar vår tidigare oändliga loop. Men i stället för att förbruka analysresultat så fort de blir tillgängliga placerar producenten helt enkelt uppgifterna i en kö för att hålla reda på dem.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

Vi har även en konsumenttråd som plockar bort uppgifterna från kön, väntar på att de ska avslutas och antingen visar resultatet eller höjer de undantag som kastades. Om du använder kön garanterar vi att resultaten används ett i taget, i rätt ordning, utan att det begränsar systemets maximala bildfrekvens.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementera lösningen

### <a name="getting-started"></a>Komma igång

För att du ska komma igång med din app så snabbt som möjligt har vi implementerat det system som beskrivs ovan, och som ska göra det tillräckligt flexibelt så att du kan implementera många scenarier, samtidigt som systemet är enkelt att använda. Om du vill ha åtkomst till koden går du till [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteket innehåller klassen FrameGrabber som implementerar det ovan beskrivna producent-konsument-systemet för att bearbeta bildrutor från en webbkamera. Användaren kan ange den exakta formen av API-anropet, och klassen använder händelser för att informera koden om när en ny bildruta tas eller ett nytt analysresultat är tillgängligt.

För att beskriva några av möjligheterna finns det två exempelappar som använder biblioteket. Den första är en enkel konsolapp, och en förenklad version av denna återges nedan. Den hämtar bildrutor från standardwebbkameran och skickar dem till Ansikts-API för ansiktsigenkänning.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Den andra exempelappen är lite mer intressant kan användas för att välja vilka API:er som ska anropas på bildrutorna. På den vänstra sidan visar appen en förhandsgranskning av den direktströmmade videon. På den högra sidan visas det senaste API-resultatet som ett överlägg på motsvarande bildruta.

I de flesta lägen finns en synlig fördröjning mellan den direktströmmade videon till vänster och den visualiserade analysen till höger. Den här fördröjningen är den tid det tar att göra API-anrop. Undantaget till detta är i läget ”EmotionsWithClientFaceDetect”, som utför ansiktsigenkänning lokalt på klientdatorn med hjälp av OpenCV innan bilder skickas till Cognitive Services. Så sätt kan vi visualisera de identifierade ansiktena omedelbart och sedan uppdatera känslorna senare när API-anropet returneras. Detta demonstrerar möjligheten för ”hybridmetod”, där viss enkel bearbetning kan utföras på klienten och API:er för Cognitive Services sedan kan användas för att utöka detta med mer avancerad analys vid behov.

![Skärmbild av LiveCameraSample app som visar bilden med taggar visas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrera i din kodbas

Kom igång med det här exemplet genom att följa dessa steg:

1. Hämta API-nycklar för API:er för visuellt innehåll från [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). För bildruteanalys är de tillämpliga API:erna:
    - [API för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Känslo-API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klona GitHub-lagringsplatsen [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Öppna exemplet i Visual Studio 2015 och skapa och kör exempelprogrammen:
    - För BasicConsoleSample är Ansikts-API-nyckeln hårdkodad direkt i  [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - För LiveCameraSample bör du ange nycklarna i appens fönster Inställningar. De bevaras mellan sessioner som användardata.

När du är redo att integrera **refererar du helt enkelt till biblioteket VideoFrameAnalyzer från dina egna projekt.**

Funktionerna för bild, röst, video och textförståelse i VideoFrameAnalyzer använder Azure Cognitive Services. Microsoft tar emot bilder, ljud, video och andra data som du laddar upp (via den här appen) och kan använda dem i syfte att förbättra tjänsten. Vi ber om din hjälp för att skydda de personer vars data din app skickar till Azure Cognitive Services.

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du utför analys i nästan realtid av livevideoströmmar med hjälp av Ansikts-API, API för visuellt innehåll och Känslo-API, och hur du kan komma igång genom att använda vår exempelkod. Du kan börja skapa din app med kostnadsfria API-nycklar på [Azure Cognitive Services-registreringssidan](https://azure.microsoft.com/try/cognitive-services/). 

Ge oss gärna feedback och förslag i [GitHub-lagringsplatsen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) eller, om det gäller mer allmängilgit API-feedback, på vår  [UserVoice-webbplats](https://cognitive.uservoice.com/).

