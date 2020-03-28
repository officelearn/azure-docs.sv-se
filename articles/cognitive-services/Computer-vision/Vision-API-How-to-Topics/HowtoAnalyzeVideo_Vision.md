---
title: Analysera videor i nära realtid - Datorseende
titleSuffix: Azure Cognitive Services
description: Lär dig hur du utför analyser i nära realtid på ramar som hämtas från en livevideoström med hjälp av API:et för visuellt innehåll.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166834"
---
# <a name="analyze-videos-in-near-real-time"></a>Analysera videor i nära realtid

Den här artikeln visar hur du utför analys i nära realtid på ramar som tas från en livevideoström med hjälp av API:et för visuellt innehåll. De grundläggande inslagen i en sådan analys är följande:

- Hämtar bildrutor från en videokälla.
- Välja vilka ramar som ska analyseras.
- Skicka in dessa ramar till API:et.
- Konsumerar varje analysresultat som returneras från API-anropet.

Exemplen i den här artikeln är skrivna i C#. Om du vill komma åt koden går du till [exempelsidan för videoruteanalys](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) på GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Metoder för att köra nära realtidsanalys

Du kan lösa problemet med att köra nära realtidsanalys på videoströmmar med hjälp av en mängd olika metoder. Denna artikel beskriver tre av dem, i ökande nivåer av förfining.

### <a name="design-an-infinite-loop"></a>Designa en oändlig loop

Den enklaste designen för nästan realtidsanalys är en oändlig loop. I varje iteration av den här loopen tar du en ram, analyserar den och förbrukar sedan resultatet:

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

Om din analys skulle bestå av en lätt, klient-side algoritm, skulle detta tillvägagångssätt vara lämpligt. Men när analysen sker i molnet innebär den resulterande svarstiden att ett API-anrop kan ta flera sekunder. Under denna tid, du inte fånga bilder, och din tråd är i huvudsak gör ingenting. Din maximala bildhastighet begränsas av svarstiden för API-anropen.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Tillåt att API-anrop körs parallellt

Även om en enkel, enkeltrådad loop är meningsfull för en lätt algoritm på klientsidan passar den inte bra med svarstiden för ett moln-API-anrop. Lösningen på det här problemet är att låta det långvariga API-anropet köras parallellt med ramgripandet. I C# kan du göra detta genom att använda uppgiftsbaserad parallellism. Du kan till exempel köra följande kod:

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

Med den här metoden startar du varje analys i en separat uppgift. Aktiviteten kan köras i bakgrunden medan du fortsätter att ta tag i nya bildrutor. Metoden undviker att blockera huvudtråden när du väntar på att ett API-anrop ska returneras. Tillvägagångssättet kan dock innebära vissa nackdelar:
* Det kostar dig några av de garantier som den enkla versionen tillhandahålls. Det innebär att flera API-anrop kan uppstå parallellt och resultaten kan returneras i fel ordning. 
* Det kan också orsaka flera trådar att komma in i Funktionen ConsumeResult() samtidigt, vilket kan vara farligt om funktionen inte är trådsäker. 
* Slutligen håller den här enkla koden inte reda på de uppgifter som skapas, så undantag försvinner tyst. Därför måste du lägga till en "konsument" tråd som spårar analysuppgifter, höjer undantag, dödar långvariga uppgifter och ser till att resultaten förbrukas i rätt ordning, en i taget.

### <a name="design-a-producer-consumer-system"></a>Utforma ett producent-konsumentsystem

För din slutliga strategi, utforma en "producent-konsument" system, du bygga en producent tråd som liknar din tidigare nämnda oändlig loop. Men istället för att konsumera analysresultaten så snart de är tillgängliga, placerar producenten helt enkelt uppgifterna i en kö för att hålla reda på dem.

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

Du skapar också en konsumenttråd, som tar bort uppgifter från kön, väntar på att de ska slutföras och antingen visar resultatet eller höjer undantaget som uppstod. Genom att använda kön kan du garantera att resultaten förbrukas en i taget, i rätt ordning, utan att begränsa systemets maximala bildhastighet.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>Implementera lösningen

### <a name="get-started-quickly"></a>Kom igång snabbt

För att hjälpa till att få igång din app så snabbt som möjligt har vi implementerat systemet som beskrivs i föregående avsnitt. Den är avsedd att vara tillräckligt flexibel för att rymma många scenarier, samtidigt som den är lätt att använda. Om du vill komma åt koden går du till [exempelsidan för videoruteanalys](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) på GitHub.

Biblioteket innehåller `FrameGrabber` klassen, som implementerar det tidigare diskuterade producent-konsumentsystemet för att bearbeta videoramar från en webbkamera. Användare kan ange den exakta formen för API-anropet och klassen använder händelser för att låta den anropande koden veta när en ny ram hämtas eller när ett nytt analysresultat är tillgängligt.

För att illustrera några av möjligheterna har vi tillhandahållit två exempelappar som använder biblioteket. 

Den första exempelappen är en enkel konsolapp som tar bildrutor från standardwebbkameran och skickar dem sedan till Ansiktstjänsten för ansiktsidentifiering. En förenklad version av appen återges i följande kod:

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

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
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

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

Det andra exemplet app är lite mer intressant. Det låter dig välja vilket API som ska anropas på videobildrutorna. Till vänster visar appen en förhandsgranskning av livevideon. Till höger överlagrar den det senaste API-resultatet på motsvarande ram.

I de flesta lägen finns det en synlig fördröjning mellan livevideon till vänster och den visualiserade analysen till höger. Den här fördröjningen är den tid det tar att ringa API-anropet. Ett undantag är i läget "EmotionsWithClientFaceDetect", som utför ansiktsidentifiering lokalt på klientdatorn med hjälp av OpenCV innan de skickar några avbildningar till Azure Cognitive Services. 

Genom att använda den här metoden kan du visualisera det identifierade ansiktet omedelbart. Du kan sedan uppdatera känslorna senare, när API-anropet returnerar. Detta visar möjligheten till en "hybrid" strategi. Det vill än, vissa enkla bearbetning kan utföras på klienten, och sedan Cognitive Services API: er kan användas för att utöka denna bearbetning med mer avancerad analys vid behov.

![LiveCameraSample-appen som visar en bild med taggar](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrera exemplen i kodbasen

Gör följande för att komma igång med det här exemplet:

1. Hämta API-nycklar för API:er för visuellt innehåll från [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). För videobildanalys är tillämpliga tjänster:
    - [Datorseende](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Ansikte](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klona [cognitive-samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub repo.

3. Öppna exemplet i Visual Studio 2015 eller senare och skapa och kör sedan exempelprogrammen:
    - För BasicConsoleSample är face-tangenten hårdkodad direkt i [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - För LiveCameraSample anger du tangenterna i fönstret **Inställningar** i appen. Nycklarna sparas över sessioner som användardata.

När du är redo att integrera exemplen refererar du till VideoFrameAnalyzer-biblioteket från dina egna projekt.

Funktionerna för bild-, röst-, video- och textförståelse i VideoFrameAnalyzer använder Azure Cognitive Services. Microsoft tar emot bilder, ljud, video och andra data som du laddar upp (via den här appen) och kan använda dem för att förbättra tjänsterna. Vi ber om din hjälp för att skydda de personer vars data din app skickar till Azure Cognitive Services.

## <a name="summary"></a>Sammanfattning

I den här artikeln lärde du dig att köra nära realtidsanalys på livevideoströmmar med hjälp av tjänsterna Ansikts- och datorseende. Du har också lärt dig hur du kan använda vår exempelkod för att komma igång. Om du vill komma igång med att skapa din app med hjälp av kostnadsfria API-nycklar går du till [registreringssidan för Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Ge gärna feedback och förslag i [GitHub-databasen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). För att ge bredare API feedback, gå till vår [UserVoice webbplats](https://cognitive.uservoice.com/).

