---
title: Så här anger du en modell för identifiering – Ansikts-API
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du väljer vilka ansikte identifiering modell ska använda med ditt Azure Ansikts-API-program.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576703"
---
# <a name="specify-a-face-detection-model"></a>Ange en modell för identifiering av ansikte

Den här guiden visar hur du anger en modell för identifiering av ansikte för Ansikts-API i Azure.

Ansikts-API använder machine learning-modeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra våra modeller baserat på kundfeedback och utvecklingen av forskning och vi leverera dessa förbättringar som modelluppdateringar. Utvecklare har du möjlighet att ange vilken version av ansikte identifiering av modellen som de vill ha ska användas. de kan välja den modell som bäst passar deras användningsfall.

Läs vidare för att Lär dig hur du anger ansikte identifiering av modellen i vissa ansikts-åtgärder. Ansikts-API använder ansiktsigenkänning när den konverterar en avbildning av ett ansikte till någon annan form av data.

Om du är osäker på om du ska använda den senaste modellen, gå vidare till den [utvärdera olika modeller](#evaluate-different-models) avsnitt för att utvärdera den nya modellen och jämföra resultat med hjälp av dina aktuella data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du bör känna till begreppet AI ansiktsigenkänning. Om du inte se ansikte identifiering av konceptuell guiden eller här guiden:

* [Ansikte identifiering begrepp](../concepts/face-detection.md)
* [Hur du identifierar ansikten i en bild](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angivna modellen

Ansiktsigenkänning söker efter avgränsar box platserna för mänskliga ansikten och identifierar sina visual landmärken. Den extraherar de står inför funktioner och lagrar dem för senare användning i [erkännande](../concepts/face-recognition.md) åtgärder.

När du använder den [Ansiktsigenkänning – identifiera] API, som du kan tilldela versionen av modellen med den `detectionModel` parametern. Tillgängliga värden är:

* `detection_01`
* `detection_02`

En fråge-URL för den [Ansiktsigenkänning – identifiera] REST API: et ser ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

Om du använder klientbiblioteket, kan du tilldela värdet för `detectionModel` genom att ange en lämplig sträng. Om du lämnar otilldelade, API: et använder modellen standardversionen (`detection_01`). Se följande kodexempel för .NET-klientbiblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Lägg till ansiktsigenkänning i Person med angivna modellen

Ansikts-API kan extrahera ansikte data från en avbildning och associera det med en **Person** objekt via den [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. I den här API-anrop, kan du ange identifiering av modellen på samma sätt som i [Ansiktsigenkänning – identifiera].

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar en **PersonGroup** med ID `mypersongroupid` och lägger till en **Person** till den. Därefter ett ansikte läggs till den här **Person** med hjälp av den `detection_02` modellen. Om du inte anger den *detectionModel* parameter, API: et kommer att använda standardmodellen `detection_01`.

> [!NOTE]
> Du behöver inte använda samma identifiering modell för alla ansikten i en **Person** objekt, och du behöver inte använda samma identifiering av modellen när du söker efter nya ansikten ska jämföras med en **Person** objekt (i [Ansiktsigenkänning – identifiera] API, till exempel).

## <a name="add-face-to-facelist-with-specified-model"></a>Lägg till ansiktsigenkänning i FaceList med angivna modellen

Du kan också ange en modell för identifiering av när du lägger till ett ansikte i en befintlig **FaceList** objekt. Se följande kodexempel för .NET-klientbiblioteket.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar en **FaceList** kallas `My face collection` och lägger till ett ansikte i den med den `detection_02` modellen. Om du inte anger den *detectionModel* parameter, API: et kommer att använda standardmodellen `detection_01`.

> [!NOTE]
> Du behöver inte använda samma identifiering modell för alla ansikten i en **FaceList** objekt, och du behöver inte använda samma identifiering av modellen när du söker efter nya ansikten ska jämföras med en **FaceList** objekt.

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

De olika ansikte identifiering modellerna är optimerade för olika uppgifter. Se tabellen nedan för en översikt över skillnaderna.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Standard för alla åtgärder för identifiering av ansikte. | Publicerat i maj 2019 och finns också i alla åtgärder för identifiering av ansikte.
|Inte optimerade för små, sidan eller ansikten suddiga.  | Förbättrar precisionen på små, sidan och ansikten suddiga. |
|Returnerar ansiktsattribut (head attityd, ålder, känslor och så vidare) om de har angetts i anropet identifiera. |  Returnerar inte ansiktsattribut.     |
|Returnerar inför landmärken om de har angetts i anropet identifiera.   | Returnerar inte ansiktslandmärken.  |

Det bästa sättet att jämföra prestanda för den `detection_01` och `detection_02` modeller är att använda dem en samplingsdatauppsättning. Vi rekommenderar att du anropar den [Ansiktsigenkänning – identifiera] API på ett stort antal bilder, särskilt avbildningar för många ansikten eller ytor som är svåra att se, med hjälp av varje identifiering av modellen. Ta hänsyn till antalet ansikten som returnerar för varje modell.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att ange modellen identifiering ska användas med olika Ansikts-API: er. Följ sedan en Snabbstart för att komma igång med ansiktsigenkänning.

* [Identifiera ansikten i en bild (.NET SDK)](../quickstarts/csharp-detect-sdk.md)

[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc