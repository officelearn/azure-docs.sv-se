---
title: Så här anger du en igenkännings modell – ansikte
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du väljer vilken igenkännings modell som ska användas med ditt Azure-ansikts program.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938829"
---
# <a name="specify-a-face-recognition-model"></a>Ange en ansiktsigenkänningsmodell

Den här guiden visar hur du anger en ansikts igenkännings modell för ansikts igenkänning, identifiering och likhets sökning med hjälp av Azure Face service.

Ansikts tjänsten använder maskin inlärnings modeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra noggrannheten i våra modeller baserat på kundfeedback och framsteg i forskningen, och vi levererar dessa förbättringar som modell uppdateringar. Utvecklare har möjlighet att ange vilken version av ansikts igenkännings modellen som de vill använda. de kan välja den modell som passar bäst för deras användnings fall.

Om du är en ny användare rekommenderar vi att du använder den senaste modellen. Läs vidare om du vill veta hur du anger den i olika ansikts operationer samtidigt som du undviker modell konflikter. Om du är en avancerad användare och inte är säker på om du ska växla till den senaste modellen går du till avsnittet [utvärdera olika modeller](#evaluate-different-models) för att utvärdera den nya modellen och jämför resultat med den aktuella data uppsättningen.

## <a name="prerequisites"></a>Krav

Du bör känna till begreppen AI-ansikts identifiering och identifiering. Om du inte gör det kan du läsa följande instruktions guider först:

* [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
* [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansikts igenkänning identifierar de visuella landmärken av mänskliga ansikten och hittar deras platser för avgränsnings rutor. Den extraherar också ansikts funktionerna och lagrar dem för användning i identifiering. All den här informationen utgör en representation av ett ansikte.

Igenkännings modellen används när ansikts funktionerna extraheras, så du kan ange en modell version när du utför identifierings åtgärden.

När du använder API: et för [Identifiera ansikte] tilldelar du modell versionen med parametern `recognitionModel`. De tillgängliga värdena är:

* `recognition_01`
* `recognition_02`

Alternativt kan du ange parametern _returnRecognitionModel_ (standard **falskt**) för att ange om _recognitionModel_ ska returneras som svar. En fråge-URL för [Identifiera ansikte] REST API ser därför ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Om du använder klient biblioteket kan du tilldela värdet för `recognitionModel` genom att skicka en sträng som representerar versionen.
Om du lämnar den otilldelad, används standard modell versionen (_recognition_01_). Se följande kod exempel för .NET-klient biblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansikts tjänsten kan extrahera ansikts data från en avbildning och koppla den till ett **person** objekt (via [Lägg till ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -API-anrop, till exempel) och flera **person** objekt kan lagras tillsammans i en **PersonGroup**. Sedan kan en ny ansikte jämföras med en **PersonGroup** (med [Ansiktsigenkänning – identifiera] -anropet) och den matchande personen i gruppen kan identifieras.

En **PersonGroup** bör ha en unik igenkännings modell för all **person**s, och du kan ange detta med hjälp av parametern `recognitionModel` när du skapar gruppen ([PersonGroup – Skapa] eller [LargePersonGroup – Skapa]). Om du inte anger den här parametern används den ursprungliga `recognition_01` modellen. En grupp använder alltid den igenkännings modell som den skapades med, och nya ytor blir kopplade till den här modellen när de läggs till. Detta kan inte ändras efter att en grupp har skapats. Om du vill se vilken modell en **PersonGroup** har kon figurer ATS med använder du [PersonGroup – Hämta] API med parametern _returnRecognitionModel_ inställd på **True**.

Se följande kod exempel för .NET-klient biblioteket.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

I den här koden skapas en **PersonGroup** med ID `mypersongroupid` och den har kon figurer ATS att använda _recognition_02_ -modellen för att extrahera ansikts funktioner.

På motsvarande sätt måste du ange vilken modell som ska användas för att identifiera ansikten som ska jämföras med den här **PersonGroup** (via API för [Identifiera ansikte] ). Modellen du använder bör alltid vara konsekvent med **PersonGroup**-konfigurationen. annars fungerar inte åtgärden på grund av inkompatibla modeller.

Det finns ingen ändring i API för [Ansiktsigenkänning – identifiera] . du behöver bara ange modell versionen som ska identifieras.

## <a name="find-similar-faces-with-specified-model"></a>Hitta liknande ansikten med angiven modell

Du kan också ange en igenkännings modell för sökning efter likheter. Du kan tilldela modell versionen med `recognitionModel` när du skapar ansikts listan med [FaceList – skapa] API eller [LargeFaceList – skapa]. Om du inte anger den här parametern används den ursprungliga `recognition_01` modellen. En ansikts lista använder alltid den igenkännings modell som den skapades med och nya ytor blir kopplade till den här modellen när de läggs till. Detta kan inte ändras efter att det har skapats. Om du vill se vilken modell en ansikts lista har kon figurer ATS med använder du [FaceList - Get] API med parametern _returnRecognitionModel_ inställd som **Sant**.

Se följande kod exempel för .NET-klient biblioteket.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Den här koden skapar en ansikts lista som kallas `My face collection`med hjälp av _recognition_02s_ modellen för funktions extrahering. När du söker i den här ansikts listan efter liknande ansikten till en ny identifierad ansikte, måste det ansikte ha identifierats ([Identifiera ansikte]) med _recognition_02_ modellen. Som i föregående avsnitt måste modellen vara konsekvent.

Det finns ingen ändring i [Ansikte – hitta liknande] API; du anger bara modell versionen som ska identifieras.

## <a name="verify-faces-with-specified-model"></a>Verifiera ansikten med angiven modell

API för [Bekräfta ansikte] kontrollerar om två ansikten tillhör samma person. Det finns ingen ändring i verifierings-API: t avseende igenkännings modeller, men du kan bara jämföra ansikten som upptäcktes med samma modell. Därför behöver båda ansikten ha identifierats med hjälp av `recognition_01` eller `recognition_02`.

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

Om du vill jämföra prestanda för _recognition_01_ och _recognition_02_ modeller på dina data måste du:

1. Skapa två **PersonGroup**s med _recognition_01_ respektive _recognition_02_ .
1. Använd dina bilddata för att identifiera ansikten och registrera dem för **person**uppgifter för de här två **PersonGroupna**och utlösa inlärnings processen med [PersonGroup – Träna] API.
1. Testa med [Ansiktsigenkänning – identifiera] både på **PersonGroup**s och jämför resultaten.

Om du vanligt vis anger ett konfidens tröskelvärde (ett värde mellan noll och ett som avgör hur trygg modellen måste vara att identifiera en ansikts) kan du behöva använda olika tröskelvärden för olika modeller. Ett tröskelvärde för en modell är inte avsett att delas till en annan och kommer inte nödvändigt vis att ge samma resultat.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du anger vilken igenkännings modell som ska användas med olika API: er för ansikts tjänster. Följ sedan en snabb start för att komma igång med ansikts igenkänning.

* [Ansikte .NET SDK](../Quickstarts/csharp-sdk.md)
* [Ansikts python SDK](../Quickstarts/python-sdk.md)
* [Ansikte go SDK](../Quickstarts/go-sdk.md)

[Identifiera ansikte]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Ansikte – hitta liknande]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Bekräfta ansikte]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup – Hämta]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Träna]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
