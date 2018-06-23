---
title: Känslo-API-anropet efter Video | Microsoft Docs
description: 'Lär dig hur du anropar Känslo-API: T för videon i kognitiva Services.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352446"
---
# <a name="how-to-call-emotion-api-for-video"></a>Hur du anropar Känslo-API: T för videon

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här guiden visar hur du anropar Känslo-API: T för videon. Exemplen är skrivna i C# med hjälp av Känslo-API för klienten videobibliotek.

### <a name="Prep">Förberedelse</a> 
För att kunna använda Känslo-API: T för videon, behöver du en video som innehåller personer, helst video där personerna som är riktade mot kameran.

### <a name="Step1">Steg 1: Ge API-anrop</a> 
Varje anrop till Känslo-API: T för videon kräver en nyckel för prenumerationen. Den här nyckeln måste antingen skickas via en frågesträngsparameter eller anges i huvudet i begäran. Om du vill överföra nyckeln prenumeration via en frågesträng, referera till URL: en nedan Känslo-API: t video som exempel:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Som ett alternativ kan nyckeln prenumeration anges i HTTP-huvudet för begäran:

```
ocp-apim-subscription-key: <Your subscription key>
```

När du använder ett klientbibliotek skickas nyckeln prenumeration via konstruktorn för klassen VideoServiceClient. Exempel:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
En prenumeration nyckel finns [prenumerationer] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">Steg 2: Ladda upp en video på tjänsten och kontrollera status</a>
Det enklaste sättet att utför någon av Känslo-API: T för videon anrop är genom att ladda upp en video direkt. Detta görs genom att skicka en begäran om ”POST” med program/oktett-ström innehållstypen tillsammans med de data som läses från en videofil. Den maximala storleken för videon är 100MB.

Använder klientbiblioteket görs stabilisering med hjälp av överföring genom att passera i en stream-objektet. Se exemplet nedan:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Observera att CreateOperationAsync VideoServiceClient är asynkron. Anropa metoden måste markeras som asynkron samt för att kunna använda await-satsen.
Om videon finns redan på webben och har en offentlig URL, kan Känslo-API: T för videon nås genom att ange URL: en. I det här exemplet är begärandetexten en JSON-sträng som innehåller URL: en.

Använder klientbiblioteket kan stabilisering med hjälp av en URL enkelt utföras med hjälp av en annan överlagring för metoden CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Den här metoden Överför kommer vara samma för alla Känslo-API för Video-anrop. 

När du har överfört en video, är nästa igen ska du utföra att kontrollera dess status. Eftersom videofiler är ofta större och mer skilda än andra filer, kan användarna förväntar sig att lång tid i det här steget. Tiden beror på storleken och längden på filen.

Använder klientbiblioteket kan hämta du Åtgärdsstatus och resultatet med metoden GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Klientsidan bör normalt regelbundet hämtar Åtgärdsstatus förrän status visas som ”lyckades” eller ”misslyckades”.

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

När statusen för VideoOperationResult visas som ”lyckades” resultatet kan hämtas av kastar VideoOperationResult till en VideoOperationInfoResult<VideoAggregateRecognitionResult> och komma åt fältet ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Steg 3: Hämta och förstå känslo recognition och spåra JSON-utdata</a>

Det utgående resultatet innehåller metadata från ytor inom den angivna filen i JSON-format.

Enligt beskrivningen i steg 2 är JSON-utdata tillgänglig i fältet ProcessingResult i OperationResult, när dess status visas som ”lyckades”.

Ansikts-identifiering och spåra JSON innehåller följande attribut:

Attribut | Beskrivning
-------------|-------------
Version | Refererar till versionen av Känslo-API: T för videon JSON.
Tidsrymd | ”Tick” per sekund i videon.
Offset  |Tidsförskjutningen för tidsstämplar. I version 1.0 av Känslo-API för videor att detta alltid vara 0. Det här värdet kan ändras i framtida stöds scenarier.
Ramhastighet | Bildrutor per sekund av videon.
Fragment   | Metadata klipps i olika mindre delar som kallas fragment. Varje avsnitt innehåller en start, varaktighet, antalet och händelse(r).
Start   | Starttiden för den första händelsen i skalstreck.
Varaktighet |  Längden på ett fragment, i skalstreck.
Intervall |  Längden på varje händelse inom ett fragment, i skalstreck.
Händelser  | En matris av händelser. Yttre matrisen representerar ett tidsintervall. Den interna matrisen består av 0 eller fler händelser som skedde vid den punkten i tid.
windowFaceDistribution |    Procent står för att ha en viss känslo under händelsen.
windowMeanScores |  Medelvärdet av poängen för varje känsla av ytor i avbildningen.

Orsaken till formatering JSON till det här sättet är att ställa in API: er för framtida scenarier, så att det är viktigt att hämta metadata snabbt och hantera en stor ström av resultaten. Den här formateringen använder teknikerna som av fragmentering (så att du kan dela upp metadata i tidsbaserade delar, där du kan hämta vad du behöver bara) och segmentering (så att du kan dela upp händelser om de blir för stor). Med hjälp av några enkla beräkningar kan du omvandla data. Om exempelvis en händelse startade 6300 (ticks) med en tidsrymd för 2997 (ticks per sekund) och en bildhastighet sedan 29,97 (bildrutor per sekund):

*   Start/tidsrymd = 2.1 sekunder
*   Sekunder x (ramhastighet/tidsrymd) = 63 ramar

Nedan visas ett enkelt exempel på extraherar JSON till en per Ramformat för framsidan identifiering och spårning:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Eftersom emotikoner utjämnas över tiden, om du skapar en visualisering om du vill överlagra resultaten överst ursprungliga videon någonsin, subtrahera 250 millisekunder från den angivna tidsstämplar.

### <a name="Summary">Sammanfattning</a>
I den här guiden du lärt dig om funktionerna i Känslo-API för Video: hur du kan ladda upp en video, kontrollera status, hämta känslo recognition metadata.

Mer information om API-information finns i guiden API-referens ”[Känslo-API för Video referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)”.
