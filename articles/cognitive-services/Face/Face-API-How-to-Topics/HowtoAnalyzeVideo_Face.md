---
title: 'Exempel: Videoanalys i realtid – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använd Ansikts-API för att utföra analyser nästan i realtid på bildrutor som kommer från direktströmmad video.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 936c516385c88191428a46d22c14b3991885340b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878177"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Exempel: Analyser av videor i realtid

Den här guiden visar hur du utför analyser nära realtid på bildrutor som kommer från direktuppspelad video. De grundläggande komponenterna i ett sådant system är:

- Hämta bilder från en videokälla
- Välja vilka bildrutor som ska analyseras
- Skicka dessa bildrutor till API:et
- Använda varje analysresultat som returneras från API-anropet

De här exemplen är skrivna i C# och koden finns på GitHub här: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Metoden

Det finns flera sätt att lösa problemet med att köra analyser nära realtid för direktuppspelad video. Vi ska börja med att beskriva tre metoder med ökande komplexitet.

### <a name="a-simple-approach"></a>En enkel metod

Den enklaste designen för ett analyssystem nära realtid är en oändlig loop där varje iteration tar en bildruta, analyserar den och sedan använder resultatet:

```CSharp
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

Om analysen består av en förenklad algoritm för klientsidan, skulle den här metoden vara lämplig. När analys sker i molnet medför dock svarstiden ett API-anrop kan ta flera sekunder. Under den tiden registrerar vi inte bilder, och tråden gör i stort sett ingenting. Vår högsta bildfrekvens är begränsad av svarstiden för API-anrop.

### <a name="parallelizing-api-calls"></a>Parallellisera API-anrop

Även om en enkel entrådig loop passar för en enkel algoritm från klientsidan passar den inte bra ihop med den svarstid som är involverad i molnets API-anrop. Lösning på problemet är att tillåta att långvariga API-anrop körs parallellt med insamling av bildrutor. I C# kan vi åstadkomma detta med uppgiftsbaserad parallellitet, till exempel:

```CSharp
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

Den här koden startar varje analys i en separat uppgift som kan köras i bakgrunden medan vi fortsätter insamlingen av nya bildrutor. Med den här metoden undviker vi att blockera huvudtråden under väntan på att ett API-anrop ska returnera, men vi tappar vissa av de garantier som den enkla versionen erbjöd. Flera API-anrop kan ske parallellt, och resultatet kan returneras i fel ordning. Det kan också leda till att flera trådar går in i funktionen ConsumeResult() samtidigt, vilket kan vara farligt om funktionen inte är trådsäker. Slutligen håller den här enkla koden inte reda på de uppgifter som skapas, så undantag försvinner tyst. Därför är det sista steget att lägga till en ”konsumenttråd” som spårar analysuppgifterna, skapar undantag, avslutar långvariga uppgifter och ser till att resultaten förbrukas i rätt ordning.

### <a name="a-producer-consumer-design"></a>En producent-konsument-design

I vårt slutgiltiga ”producent-konsument-system” har vi en producenttråd som starkt liknar vår tidigare oändliga loop. Men i stället för att använda analysresultaten så snart de blir tillgängliga, så placerar producenten helt enkelt uppgifterna i kö för att hålla ordning på dem.

```CSharp
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

```CSharp
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

För att få igång din app så snabbt som möjligt använder du en flexibel implementering av det system som beskrivs ovan. Om du vill ha åtkomst till koden går du till [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteket innehåller klassen FrameGrabber som implementerar det ovan beskrivna producent-konsument-systemet för att bearbeta bildrutor från en webbkamera. Användaren kan ange den exakta formen av API-anropet, och klassen använder händelser för att informera koden om när en ny bildruta tas eller ett nytt analysresultat är tillgängligt.

För att beskriva några av möjligheterna finns det två exempelappar som använder biblioteket. Den första är en enkel konsolapp, och en förenklad version av den återges nedan. Den hämtar bildrutor från standardwebbkameran och skickar dem till Ansikts-API för ansiktsigenkänning.

```CSharp
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
            FaceServiceClient faceClient = new FaceServiceClient("<Subscription Key>");

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

I de flesta lägen finns en synlig fördröjning mellan den direktströmmade videon till vänster och den visualiserade analysen till höger. Den här fördröjningen är den tid det tar att göra API-anrop. Ett undantag är i läget ”EmotionsWithClientFaceDetect”, som utför ansiktsigenkänning lokalt på klientdatorn med hjälp av OpenCV innan bilder skickas till Cognitive Services. På så sätt kan vi visualisera de identifierade ansiktena omedelbart och sedan uppdatera känslorna när API-anropet returneras. Det här är ett exempel på en ”hybridmetod” där klienten kan utföra viss enkel bearbetning, och API:er för Cognitive Services kan utöka detta med mer avancerad analys vid behov.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

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
        

När du är redo att integrera **refererar du till biblioteket VideoFrameAnalyzer från dina egna projekt.** 

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du utför analys av livevideoströmmar i nästan realtid med hjälp av Ansikts-API, API för visuellt innehåll och Känslo-API samt hur du kommer igång genom att använda vår exempelkod. Du kan börja skapa din app med kostnadsfria API-nycklar på [registreringssidan för Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Ge oss gärna feedback och förslag på [GitHub-lagringsplatsen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) eller, om det gäller mer allmängiltig API-feedback, på vår  [UserVoice-webbplats](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Relaterade ämnen
- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
- [Känna igen ansikten i en bild](HowtoDetectFacesinImage.md)
