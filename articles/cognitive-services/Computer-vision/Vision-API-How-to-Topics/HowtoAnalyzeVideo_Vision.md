---
title: Realtid video analys med datorn Vision API | Microsoft Docs
description: Lär dig mer om att utföra analyser av nära realtid på ramar som tagits från direktuppspelad video med datorn Vision API i kognitiva Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: d75b1a887e5e4557d5464d8062e1bde628e7adab
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352455"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Hur du analyserar videor i realtid
Den här guiden visar hur du utför nära realtid analys på ramar som hämtats från direktuppspelad video. Komponenter i ett sådant system är:
- Hämta ramar från en video källa
- Välj vilka ramar att analysera
- Skicka dessa ramar-API: et
- Använda varje resultat som returneras från API-anrop

Exemplen är skrivna i C# och koden finns på GitHub här: [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Metoden
Det finns flera sätt att lösa problemet för analys i nära realtid som körs på videoströmmar. Vi ska börja med beskriver tre metoder i höja komplex.

### <a name="a-simple-approach"></a>En enkel metod
Den enklaste designen för ett nära realtid analyssystem är en oändlig loop där i varje iteration vi hämtar en ram, analysera och sedan använda resultatet:
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
Om vår analys består av en förenklad algoritm för klientsidan, kommer den här metoden skulle vara lämplig. Men när vår analys sker i molnet, innebär svarstiden ingår att ett API-anrop kan ta flera sekunder, under denna tid vi inte skapa avbildningar och våra tråden är i grunden gör ingenting. Vår högsta bildfrekvens begränsas av svarstiden för API-anrop.

### <a name="parallelizing-api-calls"></a>Parallelizing API-anrop
När en enkel Enkeltrådig loop passar för en förenklad algoritm för klientsidan, plats inte med svarstiden som ingår i molnet API-anrop. Lösning på problemet är att tillåta API-anrop för tidskrävande att köra parallellt med den ram-ta tag. I C#, kan vi göra detta med uppgiftsbaserade parallellitet, till exempel:
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
Detta startar varje analys i en separat åtgärd som kan köras i bakgrunden medan vi fortsätter gripa nya bildrutor. Detta förhindrar blockerar huvudtråden medan väntar på ett API-anrop att returnera, men vi har förlorat några av garantierna som den enkla version som följde--flera API-anrop kan ske parallellt och resultat kan returneras i fel ordning. Det kan göra flera trådar för att ange funktionen ConsumeResult() samtidigt, vilket kan vara farligt om funktionen inte är trådsäker. Slutligen enkla koden inte hålla reda på de uppgifter som skapas, så undantag försvinner tyst. Därför är den sista komponenten för att vi ska lägga till en ”konsumenten” tråd som ska spåra uppgifter för analys, utlöser undantag, kill tidskrävande uppgifter och kontrollera resultaten hämta förbrukas i rätt ordning, en i taget.

### <a name="a-producer-consumer-design"></a>En Design för tillverkare-Consumer
I vårt system ”producenten-konsumenten” har vi en producent tråd som liknar våra tidigare oändlig loop. Men i stället för att använda analysresultat så snart de blir tillgängliga, placerar producenten bara aktiviteterna i en kö för att hålla ordning på dem.
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
Vi har också en konsument-tråd som tar aktiviteter av den kö som väntar på att slutföras, och visa resultatet eller utfärdande av undantag som uppstod. Med hjälp av kön garanterar vi att resultat får förbrukade en i taget, i rätt ordning, utan att begränsa den maximala bildfrekvens av systemet.
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
För att få din app in och körs så fort som möjligt, har vi implementerat det system som beskrivs ovan, har för avsikt att vara tillräckligt flexibelt för att implementera många scenarier, samtidigt som det är enkelt att använda. Om du vill komma åt koden, gå till [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteket innehåller klassen FrameGrabber som implementerar producenten-consumer systemet bearbeta bildrutor från en webbkamera som beskrivs ovan. Användaren kan ange exakta form av API-anrop och klassen använder händelser så att den anropande koden veta när en ny ram förvärvas eller en ny resultat är tillgängliga.

För att illustrera några möjligheter finns det två exempelappar som används i biblioteket. Först är ett enkelt konsolprogram och en förenklad version av detta återges nedan. Den hämtar ramar från standard webbkamera och skickar dem till Ansikts-API för identifiering av står inför.
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
Andra exempelappen är lite mer intressant och kan du välja vilka API för att anropa bildrutorna. Vänster visar appen en förhandsgranskning av live videon på höger sida visas det senaste API-resultatet högst upp på motsvarande ram.

I de flesta lägen blir det en synlig fördröjning mellan direktuppspelad video till vänster och visualiserade analysen till höger. Den här fördröjningen är den tid det tar att göra API-anrop. Undantaget till detta är i läget ”EmotionsWithClientFaceDetect”, som utför ansikte identifiering lokalt på klientdatorn med hjälp av OpenCV, innan du skickar bilder till kognitiva tjänster. Genom att göra vi visualisera de identifierade står inför omedelbart och uppdatera emotikoner senare när returnerar API-anrop. Detta demonstrerar risken för en ”hybrid” metod, där vissa enkel bearbetning kan utföras på klienten och sedan kognitiva Services API: er kan användas för att utöka detta med mer avancerade analyser när det behövs.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrera i din codebase
Följ dessa steg för att komma igång med det här exemplet:

1. Hämta API-nycklar för Vision-API: er från [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). För analys av bildruta är de tillämpliga API: er
    - [API för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Känslo-API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klona den [kognitiva funktionsnedsättningar-exempel-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-repo

3. Öppna exemplet i Visual Studio 2015, skapa och köra exempelprogrammen:
    - Ansikts-API-nyckeln för BasicConsoleSample, är hårdkodat direkt i [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - För LiveCameraSample, bör nycklarna anges i rutan inställningar i appen. De kommer vara beständiga mellan sessioner som användardata.
        

När du är redo att integrera, **bara referera till biblioteket VideoFrameAnalyzer från dina projekt.** 



## <a name="developer-code-of-conduct"></a>Utvecklarens regler för uppförande
Som med alla kognitiva tjänsterna, utveckla med våra API: er och exempel utvecklare måste följa den ”[Developer uppförandekod för tjänster som Microsoft Cognitive](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)”. 


Bild, ljud, video eller text förstå funktionerna i VideoFrameAnalyzer använder kognitiva Microsoft-tjänster. Microsoft får avbildningar, ljud, video och andra data som du överför (via den här appen) och kan använda dem för tjänsten improvement ändamål. Vi ber om din hjälp för att skydda personer vars data appen skickar till Microsoft kognitiva Services. 


## <a name="summary"></a>Sammanfattning
I den här guiden du har lärt dig hur du kör nära realtid analys på live videoströmmar med framsidan, datorn Vision och Känslo-API: er och hur du kan använda vår exempelkod för att komma igång. Du kan börja skapa en app med ledigt API-nycklar på den [registreringssidan för Microsoft kognitiva Services](https://azure.microsoft.com/try/cognitive-services/). 

Gärna ge feedback och förslag i den [GitHub-lagringsplatsen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), eller för mer omfattande API-feedback på vår [UserVoice-webbplatsen](https://cognitive.uservoice.com/).

