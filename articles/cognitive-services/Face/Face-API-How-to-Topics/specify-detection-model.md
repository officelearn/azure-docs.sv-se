---
title: Så här anger du en identifierings modell – FACET
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du kan välja vilken ansikts identifierings modell som ska användas med ditt Azure-ansikts program.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a70b10f7d22c9cc04427bdfbb44243fad457ba0
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913491"
---
# <a name="specify-a-face-detection-model"></a>Ange en ansiktsavkänningsmodell

Den här guiden visar hur du anger en ansikts identifierings modell för Azure Face service.

Ansikts tjänsten använder maskin inlärnings modeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra noggrannheten i våra modeller baserat på kundfeedback och framsteg i forskningen, och vi levererar dessa förbättringar som modell uppdateringar. Utvecklare har möjlighet att ange vilken version av ansikts identifierings modellen som de vill använda. de kan välja den modell som passar bäst för deras användnings fall.

Läs vidare om du vill lära dig att ange ansikts igenkännings modellen i vissa ansikts åtgärder. Ansikts tjänsten använder ansikts igenkänning när den konverterar en bild av en ansikte till en annan typ av data.

Om du inte är säker på om du ska använda den senaste modellen kan du gå till avsnittet [utvärdera olika modeller](#evaluate-different-models) för att utvärdera den nya modellen och jämföra resultat med din aktuella data uppsättning.

## <a name="prerequisites"></a>Förutsättningar

Du bör känna till begreppet AI ansikts igenkänning. Om du inte gör det, se rikt linjer för ansikts igenkänning eller instruktions guide:

* [Koncept för ansikts igenkänning](../concepts/face-detection.md)
* [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansikts igenkänning hittar de platser som är riktade mot de mänskliga ansikten och identifierar deras visuella landmärken. Den extraherar ansiktenas funktioner och lagrar dem för senare användning i [igenkännings](../concepts/face-recognition.md) åtgärder.

När du använder API: t för [ansikts igenkänning] kan du tilldela modell versionen till- `detectionModel` parametern. De tillgängliga värdena är:

* `detection_01`
* `detection_02`

En fråge-URL för [ansikts igenkännings] REST API ser ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Om du använder klient biblioteket kan du tilldela värdet för `detectionModel` genom att skicka i en lämplig sträng. Om du lämnar den otilldelad, används standard modell versionen () för API: et `detection_01` . Se följande kod exempel för .NET-klient biblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_03", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Lägg till ansikte till person med angiven modell

Ansikts tjänsten kan extrahera ansikts data från en avbildning och koppla den till ett **person** objekt via [PersonGroup-personen – Lägg till ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -API. I det här API-anropet kan du ange identifierings modellen på samma sätt som i [ansikts igenkänning].

Se följande kod exempel för .NET-klient biblioteket.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_03");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar en **PersonGroup** med ID `mypersongroupid` och lägger till en **person** till den. Sedan lägger den till en ansikte till den här **personen** med hjälp av `detection_02` modellen. Om du inte anger parametern *detectionModel* använder API: n standard modellen `detection_01` .

> [!NOTE]
> Du behöver inte använda samma identifierings modell för alla ansikten i ett **person** objekt, och du behöver inte använda samma identifierings modell när du identifierar nya ytor som ska jämföras med ett **person** objekt (till exempel i API: t för [ansikts] igenkänning).

## <a name="add-face-to-facelist-with-specified-model"></a>Lägg till ansikte i FaceList med angiven modell

Du kan också ange en identifierings modell när du lägger till ett ansikte i ett befintligt **FaceList** -objekt. Se följande kod exempel för .NET-klient biblioteket.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Den här koden skapar en **FaceList** som anropas `My face collection` och lägger till en ansikte till den med `detection_02` modellen. Om du inte anger parametern *detectionModel* använder API: n standard modellen `detection_01` .

> [!NOTE]
> Du behöver inte använda samma identifierings modell för alla ansikten i ett **FaceList** -objekt, och du behöver inte använda samma identifierings modell när du identifierar nya ytor som ska jämföras med ett **FaceList** -objekt.

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

Olika ansikts identifierings modeller är optimerade för olika uppgifter. I följande tabell visas en översikt över skillnaderna.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Standard val för alla ansikts identifierings åtgärder. | Släpps i maj 2019 och tillgängligt eventuellt i alla ansikts identifierings åtgärder.
|Inte optimerat för små, sido visning eller suddiga ansikten.  | Förbättrad noggrannhet för små, sido visning och suddiga ansikten. |
|Returnerar ansikts attribut (Head-attityd, ålder, känslo och så vidare) om de anges i identifiera anropet. |  Returnerar inte ansikts-attribut.     |
|Returnerar ansikts landmärken om de anges i identifiera anropet.   | Returnerar inte ansikts landmärken.  |

Det bästa sättet att jämföra prestanda för- `detection_01` och- `detection_02` modeller är att använda dem i en exempel data uppsättning. Vi rekommenderar att du anropar API: t för [ansikts igenkänning] på olika avbildningar, särskilt bilder av många ansikten eller ansikten som är svåra att se, med varje identifierings modell. Var uppmärksam på antalet ansikten som varje modell returnerar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du anger vilken identifierings modell som ska användas med olika ansikts-API: er. Följ sedan en snabb start för att komma igång med ansikts igenkänning.

* [Ansikte .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Ansikts python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Ansikte go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

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