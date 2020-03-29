---
title: Så här anger du en igenkänningsmodell - Face
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du väljer vilken igenkänningsmodell som ska användas med ditt Azure Face-program.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938829"
---
# <a name="specify-a-face-recognition-model"></a>Ange en ansiktsigenkänningsmodell

Den här guiden visar hur du anger en ansiktsigenkänningsmodell för ansiktsidentifiering, identifiering och likhetssökning med hjälp av Azure Face-tjänsten.

Face-tjänsten använder maskininlärningsmodeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra noggrannheten i våra modeller baserat på feedback från kunder och framsteg inom forskning, och vi levererar dessa förbättringar som modelluppdateringar. Utvecklare har möjlighet att ange vilken version av ansiktsigenkänningsmodellen de vill använda. de kan välja den modell som bäst passar deras användningsfall.

Om du är en ny användare rekommenderar vi att du använder den senaste modellen. Läs vidare om du vill lära dig hur du anger det i olika Face-åtgärder samtidigt som du undviker modellkonflikter. Om du är en avancerad användare och inte är säker på om du ska byta till den senaste modellen går du till avsnittet [Utvärdera olika modeller](#evaluate-different-models) för att utvärdera den nya modellen och jämföra resultat med hjälp av den aktuella datauppsättningen.

## <a name="prerequisites"></a>Krav

Du bör känna till begreppen AI ansiktsigenkänning och identifiering. Om du inte är det kan du först se följande hjälpguider:

* [Så här identifierar du ansikten i en bild](HowtoDetectFacesinImage.md)
* [Så här identifierar du ansikten i en bild](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansiktsigenkänning identifierar de visuella landmärkena i mänskliga ansikten och hittar deras markeringsramsplatser. Det extraherar också ansiktets funktioner och lagrar dem för användning i identifiering. All denna information utgör representationen av ett ansikte.

Igenkänningsmodellen används när ansiktsfunktionerna extraheras, så du kan ange en modellversion när du utför identifieringen.

När du använder [Face - Detect] API, `recognitionModel` tilldela modellversionen med parametern. De tillgängliga värdena är:

* `recognition_01`
* `recognition_02`

Du kan också ange parametern _returnRecognitionModel_ (standard **false)** för att ange om _recognitionModel_ ska returneras som svar. Så, en begäran URL för [Face - Detect] REST API kommer att se ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Om du använder klientbiblioteket kan du `recognitionModel` tilldela värdet för genom att skicka en sträng som representerar versionen.
Om du lämnar den otilldelad används standardmodellversionen (_recognition_01_). Se följande kodexempel för .NET-klientbiblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansiktstjänsten kan extrahera ansiktsdata från en bild och associera den med ett **personobjekt** (till exempel via [lägg till](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ansikts-API-anrop) och flera **personobjekt** kan lagras tillsammans i en **persongrupp**. Sedan kan ett nytt ansikte jämföras med en **PersonGroup** (med [Face - Identifiera] samtal) och den matchande personen i den gruppen kan identifieras.

En **persongrupp** ska ha en unik igenkänningsmodell för alla `recognitionModel` **personer**och du kan ange detta med parametern när du skapar gruppen ([PersonGroup - Create] eller [LargePersonGroup - Create]). Om du inte anger den `recognition_01` här parametern används den ursprungliga modellen. En grupp använder alltid den igenkänningsmodell som skapas med, och nya ansikten associeras med den här modellen när de läggs till i den. Detta kan inte ändras när en grupp har skapats. Om du vill se vilken modell en **PersonGroup** är konfigurerad med använder du [persongruppen - Hämta] API med parametern _returnRecognitionModel_ som **true**.

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

I den här koden skapas `mypersongroupid` en **persongrupp** med ID och den är inställd på att använda _recognition_02-modellen_ för att extrahera ansiktsfunktioner.

På motsvarande sätt måste du ange vilken modell som ska användas när du upptäcker ansikten för att jämföra med den här **PersonGroup** (via [Face - Detect] API). Modellen du använder ska alltid vara förenlig med **PersonGroups**konfiguration. Annars misslyckas åtgärden på grund av inkompatibla modeller.

Det finns ingen förändring i [Face - Identifiera] API; Du behöver bara ange modellversionen i identifieringen.

## <a name="find-similar-faces-with-specified-model"></a>Hitta liknande ansikten med angiven modell

Du kan också ange en igenkänningsmodell för likhetssökning. Du kan tilldela modellversionen med `recognitionModel` när du skapar ansiktslistan med [FaceList - Create] API eller [LargeFaceList - Create]. Om du inte anger den `recognition_01` här parametern används den ursprungliga modellen. En ansiktslista använder alltid den igenkänningsmodell som den skapades med, och nya ansikten associeras med den här modellen när de läggs till i den. Detta kan inte ändras när det har skapats. Om du vill se vilken modell en ansiktslista är konfigurerad med använder du [FaceList - Get] API med parametern _returnRecognitionModel_ som **true**.

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Den här koden skapar `My face collection`en ansiktslista som heter , med hjälp av _recognition_02_ modell för funktionsextrahering. När du söker i ansiktslistan efter liknande ansikten som ett nytt upptäckt ansikte måste det ansiktet ha upptäckts[(Face - Detect)]med hjälp av _recognition_02_ modellen. Precis som i föregående avsnitt måste modellen vara konsekvent.

Det finns ingen förändring i [ansiktet - Hitta liknande] API; Du anger bara modellversionen i identifieringen.

## <a name="verify-faces-with-specified-model"></a>Verifiera ansikten med angiven modell

[Face - Verifiera] API kontrollerar om två ansikten tillhör samma person. Det finns ingen ändring i Verifiera API när det gäller igenkänningsmodeller, men du kan bara jämföra ansikten som upptäcktes med samma modell. Så, de två ansikten måste båda `recognition_01` ha `recognition_02`upptäckts med hjälp av eller .

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

Om du vill jämföra prestanda för _recognition_01_ och _recognition_02_ modeller på dina data, måste du:

1. Skapa två **PersonGroup**s med _recognition_01_ respektive _recognition_02._
1. Använd dina bilddata för att identifiera ansikten och registrera dem till **Person**s för dessa två **PersonGroup**s, och utlösa utbildningsprocessen med [PersonGroup - Train] API.
1. Testa med [Face - Identifiera] på både **PersonGroup**s och jämför resultaten.

Om du normalt anger ett konfidenströskel (ett värde mellan noll och ett som avgör hur säker modellen måste vara för att identifiera ett ansikte), kan du behöva använda olika tröskelvärden för olika modeller. En tröskel för en modell är inte avsedd att delas med en annan och kommer inte nödvändigtvis att ge samma resultat.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du anger vilken igenkänningsmodell som ska användas med olika Api:er för Face-tjänsten. Följ sedan en snabbstart för att komma igång med ansiktsigenkänning.

* [Vänd mot .NET SDK](../Quickstarts/csharp-sdk.md)
* [Ansikte Python SDK](../Quickstarts/python-sdk.md)
* [Ansikte Gå SDK](../Quickstarts/go-sdk.md)

[Ansiktsigenkänning – Känna igen]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Hitta liknande]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Ansikte - Verifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Få]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Träna]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Få]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
