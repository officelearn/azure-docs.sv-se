---
title: 'Exempel: Videoanalys i realtid – Visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att utföra analyser nära realtid på bildrutor som kommer från direktuppspelad video med hjälp av API för visuellt innehåll.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859084"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Analysera videor i real tid

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
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
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

![Skärmbild av appen LiveCameraSample visar en bild där taggar visas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrera i din kodbas

Kom igång med det här exemplet genom att följa dessa steg:

1. Hämta API-nycklar för API:er för visuellt innehåll från [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). För bildruteanalys är de tillämpliga API:erna:
    - [API för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klona GitHub-lagringsplatsen [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Öppna exemplet i Visual Studio 2015 eller senare, skapa och kör exempel programmen:
    - För BasicConsoleSample är Ansikts-API-nyckeln hårdkodad direkt i [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - För LiveCameraSample bör du ange nycklarna i appens fönster Inställningar. De bevaras mellan sessioner som användardata.

När du är redo att integrera **refererar du helt enkelt till biblioteket VideoFrameAnalyzer från dina egna projekt.**

Funktionerna för bild, röst, video och textförståelse i VideoFrameAnalyzer använder Azure Cognitive Services. Microsoft tar emot bilder, ljud, video och andra data som du laddar upp (via den här appen) och kan använda dem i syfte att förbättra tjänsten. Vi ber om din hjälp för att skydda de personer vars data din app skickar till Azure Cognitive Services.

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du kör real tids analyser i real tid med real tids video strömmar med ansikts-och Visuellt innehåll-API: er och hur du kan använda vår exempel kod för att komma igång. Du kan börja skapa din app med kostnadsfria API-nycklar på [Azure Cognitive Services-registreringssidan](https://azure.microsoft.com/try/cognitive-services/).

Ge feedback och förslag på [GitHub-lagringsplatsen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) eller mer allmän API-feedback på vår [UserVoice-webbplats](https://cognitive.uservoice.com/).

