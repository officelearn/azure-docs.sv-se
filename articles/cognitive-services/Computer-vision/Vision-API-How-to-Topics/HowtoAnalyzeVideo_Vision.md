---
title: Analysera videor i nära real tid – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du utför en analys i nära real tid i bild rutor som hämtas från en live video ström med hjälp av API för visuellt innehåll.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933179"
---
# <a name="analyze-videos-in-near-real-time"></a>Analysera videor i nära real tid

Den här artikeln visar hur du kan analysera i nära real tid i bild rutor som hämtas från en real tids video ström med hjälp av API för visuellt innehåll. De grundläggande elementen i en sådan analys är:

- Hämtar ramar från en video källa.
- Välja vilka ramar som ska analyseras.
- Skickar dessa ramar till API: et.
- Förbrukar varje analys resultat som returneras från API-anropet.

Exemplen i den här artikeln är skrivna i C#. För att komma åt koden går du till [exempel sidan analys av video RAM](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) på GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Metoder för att köra nära real tids analys

Du kan lösa problemet med att köra nära real tids analys på video strömmar genom att använda olika metoder. Den här artikeln beskriver tre av dem, i ökande nivåer av riktigt ambitiös.

### <a name="design-an-infinite-loop"></a>Utforma en oändlig slinga

Den enklaste designen för nära real tids analys är en oändlig loop. I varje iteration av den här slingan hämtar du en ram, analyserar den och använder sedan resultatet:

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

Om din analys skulle bestå av en enkel algoritm på klient sidan är den här metoden lämplig. Men när analysen sker i molnet innebär den resulterande fördröjningen att ett API-anrop kan ta flera sekunder. Under den här tiden fångar du inte avbildningar och din tråd gör ingenting i princip. Din maximala bild frekvens begränsas av API-anropens svars tid.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Tillåt att API-anrop körs parallellt

Även om en enkel, enkel tråds slinga fungerar bra för en lätt, algoritm på klient sidan, passar den inte med svars tiden för ett Cloud API-anrop. Lösningen på det här problemet är att tillåta att tids krävande API-anrop körs parallellt med den ram som fångas. I C# kan du göra detta med hjälp av uppgifts-baserad parallellitet. Du kan till exempel köra följande kod:

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

Med den här metoden startar du varje analys i en separat uppgift. Uppgiften kan köras i bakgrunden medan du fortsätter att ta nya ramar. Metoden förhindrar att huvud tråden blockeras när du väntar på ett API-anrop att returnera. Metoden kan dock ge vissa nack delar:
* Det kostar några av de garantier som den enkla versionen tillhandahåller. Det vill säga att flera API-anrop kan inträffa parallellt och resultaten kan komma att returneras i fel ordning. 
* Det kan också orsaka att flera trådar anger funktionen ConsumeResult () samtidigt, som kan vara farlig om funktionen inte är tråd säker. 
* Slutligen kan den här enkla koden inte spåra de uppgifter som skapas, så undantagen försvinner i bakgrunden. Därför måste du lägga till en "konsument"-tråd som spårar analys aktiviteter, genererar undantag, omsorg långvariga uppgifter och säkerställer att resultaten förbrukas i rätt ordning, en i taget.

### <a name="design-a-producer-consumer-system"></a>Utforma ett producent-konsument system

För den slutliga metoden, som utformar ett "produce-konsument"-system, skapar du en producent tråd som liknar din tidigare nämnda oändliga loop. Men i stället för att använda analys resultaten så fort de är tillgängliga placerar producenten bara uppgifterna i en kö för att hålla koll på dem.

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

Du kan också skapa en konsument tråd, som tar uppgifter ur kön, väntar på att de ska slutföras och visar resultatet eller aktiverar det undantag som har utlösts. Med hjälp av kön kan du garantera att resultaten kan förbrukas en i taget, i rätt ordning, utan att begränsa systemets maximala RAM hastighet.

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

För att hjälpa dig att få igång din app så snabbt som möjligt har vi implementerat systemet som beskrivs i föregående avsnitt. Den är avsedd att vara tillräckligt flexibel för att kunna hantera många scenarier, samtidigt som de är lätta att använda. För att komma åt koden går du till [exempel sidan analys av video RAM](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) på GitHub.

Biblioteket innehåller `FrameGrabber` klassen, som implementerar det tidigare diskuterade producent-Consumer-systemet för att bearbeta video bild rutor från en webb kamera. Användarna kan ange exakt form för API-anropet och klassen använder händelser för att meddela anrops koden när en ny ram förvärvas, eller när ett nytt analys resultat är tillgängligt.

För att illustrera några av möjligheterna har vi tillhandahållit två exempel på appar som använder biblioteket. 

Den första exempel appen är en enkel konsol app som hämtar ramar från standard webb kameran och skickar dem sedan till ansikts tjänsten för ansikts igenkänning. En förenklad version av appen reproduceras i följande kod:

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

Den andra exempel appen är lite mer intressant. Du kan välja vilken API som ska anropas för video bild rutorna. På den vänstra sidan visar appen en förhands visning av Live-videon. Till höger överlappar det senaste API-resultatet i motsvarande ram.

I de flesta lägen finns det en synlig fördröjning mellan Live-videon till vänster och den visualiserade analysen till höger. Den här fördröjningen är den tid det tar att göra API-anropet. Ett undantag är i läget "EmotionsWithClientFaceDetect", som utför ansikts identifiering lokalt på klient datorn med hjälp av OpenCV innan alla avbildningar skickas till Azure Cognitive Services. 

Genom att använda den här metoden kan du visualisera den identifierade ytan direkt. Sedan kan du uppdatera känslor senare när API-anropet returnerar. Detta visar risken för en hybrid metod. Det vill säga att en enkel bearbetning kan utföras på klienten och att API:er för Cognitive Services kan användas för att utöka den här bearbetningen med mer avancerad analys när det behövs.

![LiveCameraSample-appen visar en bild med Taggar](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrera exemplen i kodbasen

Kom igång med det här exemplet genom att göra följande:

1. Skapa ett [Azure-konto](https://azure.microsoft.com/free/cognitive-services/). Om du redan har en kan du gå vidare till nästa steg.
2. Skapa resurser för Visuellt innehåll och ansikte i Azure Portal för att hämta din nyckel och slut punkt. Se till att välja den kostnads fria nivån (F0) under installationen.
   - [Visuellt innehåll](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Ansikte](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) När resurserna har distribuerats klickar du på **gå till resurs** för att samla in din nyckel och slut punkt för varje resurs. 
3. Klona [kognitiv-samples-VideoFrameAnalysis GitHub-](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) lagrings platsen.
4. Öppna exemplet i Visual Studio 2015 eller senare och skapa sedan och kör exempel programmen:
    - För BasicConsoleSample är ansikts nyckeln hårdkodad direkt i [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - För LiveCameraSample anger du nycklarna i appens **inställnings** ruta. Nycklarna behålls mellan sessioner som användar data.

När du är redo att integrera exemplen refererar du till VideoFrameAnalyzer-biblioteket från dina egna projekt.

Funktionerna image-, röst-, video-och text hantering i VideoFrameAnalyzer använder Azure Cognitive Services. Microsoft tar emot bilder, ljud, video och andra data som du överför (via den här appen) och kan använda dem för tjänst förbättrings syfte. Vi ber om din hjälp för att skydda de personer vars data din app skickar till Azure Cognitive Services.

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig hur du kör nära real tids analys på direktsända video strömmar med hjälp av ansikts-och Visuellt innehålls tjänsterna. Du har också lärt dig hur du kan använda vår exempel kod för att komma igång.

Lämna gärna feedback och förslag i [GitHub-lagringsplatsen](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Om du vill ge bredare API-feedback går du till vår [UserVoice-webbplats](https://cognitive.uservoice.com/).

