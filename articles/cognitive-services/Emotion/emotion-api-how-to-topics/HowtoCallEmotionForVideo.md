---
title: 'Exempel: Anropa Känslo-API för video'
titlesuffix: Azure Cognitive Services
description: Lär dig hur du anropar Känslo-API för video i Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2687145a89c11efb4a3bcb1494a39806e9aae551
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238615"
---
# <a name="example-call-emotion-api-for-video"></a>Exempel: Anropa Känslo-API för video

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Den här guiden visar hur du anropar Känslo-API för video. Exemplen är skrivna i C# med hjälp av Känslo-API för videoklientbiblioteket.

### <a name="Prep">Förberedelse</a>
För att kunna använda Känslo-API för video behöver du en video som innehåller personer, helst video där personernas ansikten är vända mot kameran.

### <a name="Step1">Steg 1: Auktorisera API-anropet</a>
Alla anrop till Känslo-API:et för video kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud. Om du vill skicka prenumerationsnyckeln via en frågesträng använder du begärande-URL:en nedan för Känslo-API för video som exempel:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Alternativt kan prenumerationsnyckeln specificeras i HTTP-begärans sidhuvud:

```
ocp-apim-subscription-key: <Your subscription key>
```

När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för klassen VideoServiceClient. Exempel:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Prenumerationer] (https://azure.microsoft.com/try/cognitive-services/).

### <a name="Step2">Steg 2: Ladda upp en video till tjänsten och kontrollera status</a>
Det enklaste sättet att utföra någon av anropen med Känslo-API för video är att ladda upp en video direkt. Detta sker genom att en ”POST”-begäran med innehållstypen program-/oktettflöde skickas tillsammans med de data som läses från en videofil. Videons maxstorlek är 100 MB.

Med hjälp av klientbiblioteket sker stabilisering med uppladdning genom att ett stream-objekt skickas in. Se exemplet nedan:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Observera att metoden CreateOperationAsync i VideoServiceClient är asynkron. Anropsmetoden bör också markeras som asynkron för att använda väntesatsen.
Om videon redan finns på webben och har en offentlig URL kan Känslo-API för video nås genom att URL:en anges. I det här exemplet är begärandetexten en JSON-sträng som innehåller URL:en.

Med hjälp av klientbiblioteket kan stabilisering med en URL enkelt utföras med hjälp av en annan överlagring för metoden CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Den här uppladdningsmetoden blir densamma för alla anrop med Känslo-API för video.

När du har laddat upp en video är nästa åtgärd som du bör utföra att kontrollera dess status. Eftersom videofiler vanligtvis är större och mer mångskiftande än andra filer kan användarna förvänta sig en bearbetningstid i det här steget. Tiden beror på filens storlek och längd.

Med hjälp av klientbiblioteket kan du hämta åtgärdsstatus och resultat med metoden GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Normalt bör klientsidan med jämna mellanrum hämta åtgärdsstatus tills statusen visas som ”Succeeded” (Lyckades) eller ”Failed” (Misslyckades).

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

När statusen för VideoOperationResult visas som ”Succeeded” (Lyckades) kan resultatet hämtas genom att omvandla VideoOperationResult till ett VideoOperationInfoResult<VideoAggregateRecognitionResult> och komma åt fältet ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Steg 3: Hämta och förstå känsloigenkänning och spåra JSON-utdata</a>

Utdataresultatet innehåller metadata från ansiktena inom den angivna filen i JSON-format.

Enligt beskrivningen i steg 2 är JSON-utdata tillgängliga i fältet ProcessingResult i OperationResult, när dess status visas som ”Succeeded” (Lyckades).

Ansiktsigenkännings- och spårnings-JSON innehåller följande attribut:

Attribut | Beskrivning
-------------|-------------
Version | Refererar till versionen av Känslo-API för video-JSON.
Tidsskala | ”Tick” per sekund i videon.
Offset  |Tidsförskjutningen för tidsstämplar. I version 1.0 av Känslo-API för videor är detta alltid 0. I framtida scenarier som stöds kan detta värde komma att ändras.
Bildfrekvens | Bildrutor per sekund i videon.
Fragment   | Metadata delas in i flera mindre delar som kallas fragment. Varje fragment innehåller en start, varaktighet, intervallnummer och händelser.
Start   | Starttiden för den första händelsen i tick.
Varaktighet |  Längden på fragmentet i tick.
Intervall |  Längden på varje händelse i fragmentet i tick.
Händelser  | En matris med händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten.
windowFaceDistribution |    Den procentandel ansikten som har en viss känsla under händelsen.
windowMeanScores |  Genomsnittlig poäng för varje känsla hos ansiktena på bilden.

Orsaken för att JSON formateras på det här sättet är att konfigurera API:er för framtida scenarier där det är viktigt att hämta metadata snabbt och hantera en stor ström av resultat. Den här formateringen använder både metoderna för fragmentering (så att du kan dela in metadata i tidsbaserade delar, där du kan ladda ned endast det du behöver) och segmentering (så att du kan dela upp händelserna om de blir för stora). Några enkla beräkningar kan hjälpa dig att transformera data. Exempel: om en händelse startade vid 6 300 (tick) med en tidsskala på 2 997 (tick per sekund) och en bildfrekvens på 29,97 (bildrutor per sekund) gäller följande:

*   Start/tidsskala = 2,1 sekunder
*   Sekunder x (bildfrekvens/tidsskala) = 63 bildrutor

Nedan visas ett enkelt exempel på extrahering av JSON-koden i ett format per bildram för ansiktsigenkänning och spårning:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Eftersom känslor jämnas ut med tiden ska du om du skapar en visualisering för att överlägga dina resultat ovanpå originalvideon subtrahera 250 millisekunder från de tillhandahållna tidsstämplarna.

### <a name="Summary">Sammanfattning</a>
I den här guiden lärde du dig om funktionerna i Känslo-API för video: hur du kan ladda upp en video, kontrollera dess status och hämta metadata för känsloigenkänning.

Mer information om API finns i API-referensguiden ”[Emotion API for Video Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)” (Referens till Känslo-API för video).
