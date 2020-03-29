---
title: Så här anger du en identifieringsmodell - Face
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du väljer vilken ansiktsidentifieringsmodell som ska användas med ditt Azure Face-program.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934580"
---
# <a name="specify-a-face-detection-model"></a>Ange en ansiktsavkänningsmodell

Den här guiden visar hur du anger en ansiktsidentifieringsmodell för Azure Face-tjänsten.

Face-tjänsten använder maskininlärningsmodeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra noggrannheten i våra modeller baserat på feedback från kunder och framsteg inom forskning, och vi levererar dessa förbättringar som modelluppdateringar. Utvecklare har möjlighet att ange vilken version av ansiktsidentifieringsmodellen de vill använda. de kan välja den modell som bäst passar deras användningsfall.

Läs vidare om du vill lära dig hur du anger ansiktsigenkänningsmodellen i vissa ansiktsoperationer. Face-tjänsten använder ansiktsigenkänning när den konverterar en bild av ett ansikte till någon annan form av data.

Om du är osäker på om du ska använda den senaste modellen går du till avsnittet [Utvärdera olika modeller](#evaluate-different-models) för att utvärdera den nya modellen och jämföra resultat med hjälp av den aktuella datauppsättningen.

## <a name="prerequisites"></a>Krav

Du bör känna till begreppet AI ansiktsigenkänning. Om du inte är det läser du konceptguiden för ansiktsigenkänning eller vägledningen:

* [Begrepp för ansiktsigenkänning](../concepts/face-detection.md)
* [Så här identifierar du ansikten i en bild](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansiktsigenkänning hittar markeringsramens platser för mänskliga ansikten och identifierar deras visuella landmärken. Den extraherar ansiktets funktioner och lagrar dem för senare användning i [erkännande](../concepts/face-recognition.md) operationer.

När du använder [API:et Face - Detect] `detectionModel` kan du tilldela modellversionen till parametern. De tillgängliga värdena är:

* `detection_01`
* `detection_02`

En url för begäran för [Face - Detect] REST API kommer att se ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Om du använder klientbiblioteket kan du `detectionModel` tilldela värdet för genom att skicka in en lämplig sträng. Om du lämnar det otilldelat kommer API:et`detection_01`att använda standardmodellversionen ( ). Se följande kodexempel för .NET-klientbiblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Lägga till ansikte till person med angiven modell

Face-tjänsten kan extrahera ansiktsdata från en bild och associera den med ett **personobjekt** via [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. I det här API-anropet kan du ange identifieringsmodellen på samma sätt som i [Face - Detect].

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar en `mypersongroupid` **persongrupp** med ID och lägger till en **person** i den. Sedan lägger ett **Person** ansikte till `detection_02` denna person med hjälp av modellen. Om du inte anger parametern *detectionModel* används standardmodellen `detection_01`.

> [!NOTE]
> Du behöver inte använda samma identifieringsmodell för alla ansikten i ett **personobjekt,** och du behöver inte använda samma identifieringsmodell när du upptäcker nya ansikten för att jämföra med ett **personobjekt** (till exempel i [ansikts- identifiera] API).

## <a name="add-face-to-facelist-with-specified-model"></a>Lägga till ansikte i FaceList med angiven modell

Du kan också ange en identifieringsmodell när du lägger till ett ansikte i ett befintligt **FaceList-objekt.** Se följande kodexempel för .NET-klientbiblioteket.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar `My face collection` en **FaceList** som anropas och lägger till ett ansikte i den `detection_02` med modellen. Om du inte anger parametern *detectionModel* används standardmodellen `detection_01`.

> [!NOTE]
> Du behöver inte använda samma identifieringsmodell för alla ansikten i ett **FaceList-objekt,** och du behöver inte använda samma identifieringsmodell när du upptäcker nya ansikten för att jämföra med ett **FaceList-objekt.**

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

De olika ansiktsidentifieringsmodellerna är optimerade för olika uppgifter. Se följande tabell för en översikt över skillnaderna.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Standardval för alla ansiktsidentifieringsåtgärder. | Släpptes i maj 2019 och finns som tillval i alla ansiktsigenkänningsåtgärder.
|Inte optimerad för små, sidovyer eller suddiga ytor.  | Förbättrad noggrannhet på små, sidovyer och suddiga ytor. |
|Returnerar ansiktsattribut (huvud pose, ålder, känslor och så vidare) om de anges i upptäcka samtalet. |  Returnerar inte ansiktsattribut.     |
|Returnerar face landmärken om de anges i identifieringsanropet.   | Returnerar inte ansikte landmärken.  |

Det bästa sättet att jämföra `detection_01` `detection_02` prestanda och modeller är att använda dem på ett urval datauppsättning. Vi rekommenderar att du anropar [Face - Detect] API på en mängd olika bilder, särskilt bilder av många ansikten eller ansikten som är svåra att se, med hjälp av varje identifieringsmodell. Var uppmärksam på antalet ansikten som varje modell returnerar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du anger identifieringsmodellen som ska användas med olika Face API:er. Följ sedan en snabbstart för att komma igång med ansiktsigenkänning.

* [Vänd mot .NET SDK](../Quickstarts/csharp-sdk.md)
* [Ansikte Python SDK](../Quickstarts/python-sdk.md)
* [Ansikte Gå SDK](../Quickstarts/go-sdk.md)

[Ansiktsigenkänning – Känna igen]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
