---
title: Så här anger du en modell för taligenkänning – Ansikts-API
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du väljer vilka igenkänningsfunktion för att använda med ditt Azure Ansikts-API-program.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58806670"
---
# <a name="specify-a-face-recognition-model"></a>Ange en ansiktsigenkänningsmodell

Den här guiden visar hur du anger en ansikte erkännande modell för ansiktsigenkänning, identifiering och likhet sökning med hjälp av Ansikts-API i Azure.

Ansikts-API använder machine learning-modeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra våra modeller baserat på kundfeedback och utvecklingen av forskning och vi leverera dessa förbättringar som modelluppdateringar. Utvecklare har du möjlighet att ange vilken version av modellen för igenkänning av ansikte som de vill ha ska användas. de kan välja den modell som bäst passar deras användningsfall.

Om du är en ny användare, rekommenderar vi att du använder den senaste modellen. Läs vidare för att Lär dig hur du anger den i olika ansikte åtgärder samtidigt som du undviker modellkonflikter. Om du är en avancerad användare och inte är säker på om du ska gå att växla till den senaste modellen, gå vidare till den [utvärdera olika modeller](#evaluate-different-models) avsnitt för att utvärdera den nya modellen och jämföra resultat med hjälp av dina aktuella data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du bör känna till begreppet AI ansiktsigenkänning och identifiering. Om du inte se dessa instruktionsguider först:

* [Hur du identifierar ansikten i en bild](HowtoDetectFacesinImage.md)
* [Så här identifierar ansikten i en bild](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angivna modellen

Ansiktsigenkänning identifierar visual landmärken av mänskliga ansikten och söker efter deras avgränsar box-platser. Dessutom extraherar de står inför funktioner och lagrar dem för användning i identifiering. All information utgör en representation av ett ansikte.

Igenkänning av modellen används när ansikts-funktioner har extraherats så att du kan ange en modellversion när du utför åtgärden identifiera.

När du använder den [Ansiktsigenkänning – identifiera] API, tilldela versionen av modellen med den `recognitionModel` parametern. Tillgängliga värden är:

* `recognition_01`
* `recognition_02`

Alternativt kan du ange den _returnRecognitionModel_ parametern (standard **FALSKT**) som anger om _recognitionModel_ ska returneras i svaret. Det öppnar en begärd URL för den [Ansiktsigenkänning – identifiera] REST API: et ser ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Om du använder klientbiblioteket, kan du tilldela värdet för `recognitionModel` genom att skicka en sträng som representerar versionen.
Om du lämnar den otilldelade, modell standardversionen (_recognition_01_) ska användas. Se följande kodexempel för .NET-klientbiblioteket.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifiera ansikten med angivna modellen

Ansikts-API kan extrahera ansikte data från en avbildning och associera det med en **Person** objekt (via den [lägga till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-anrop, till exempel), och flera **Person** objekt kan vara lagras tillsammans i en **PersonGroup**. Ett nytt ansikte kan sedan jämföras mot ett **PersonGroup** (med den [Ansiktsigenkänning – identifiera] anropa), och den matchande personen i gruppen kan identifieras.

En **PersonGroup** bör ha en unik erkännande modell för alla de **Person**s, och du kan ange detta med hjälp av den `recognitionModel` parameter när du skapar gruppen ([PersonGroup – Skapa] eller [LargePersonGroup – skapa]). Om du inte anger den här parametern, Ursprungligt `recognition_01` modellen används. En grupp använder alltid den skapades med igenkänning av modellen och nya ansikten kommer att bli associerad med den här modellen när de läggs till. Detta kan inte ändras när du har skapat en grupp. Att se vilka modellen som en **PersonGroup** har konfigurerats med, använda den [PersonGroup - Get] API med den _returnRecognitionModel_ parameteruppsättning som **SANT**.

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

I den här koden en **PersonGroup** med ID `mypersongroupid` skapas, och den är konfigurerad att använda den _recognition_02_ modellen för att extrahera ansikts-funktioner.

På motsvarande sätt måste du ange vilken modell som du använder när du söker efter ansikten för att jämföra med det här **PersonGroup** (via den [Ansiktsigenkänning – identifiera] API). Du använder modellen bör alltid vara konsekvent med den **PersonGroup**'s konfigurationen; i annat fall misslyckas åtgärden på grund av inkompatibla modeller.

Ingen ändring i den [Ansiktsigenkänning – identifiera] API; du behöver bara ange modellversion i identifiering.

## <a name="find-similar-faces-with-specified-model"></a>Hitta liknande ansikten med angivna modellen

Du kan också ange en igenkänningsfunktion för för likheter sökning. Du kan tilldela modellversion med `recognitionModel` när du skapar listan ansikte med [FaceList - skapa] API eller [LargeFaceList – skapa]. Om du inte anger den här parametern, Ursprungligt `recognition_01` modellen används. En lista med face använder alltid den skapades med igenkänning av modellen och nya ansikten blir associerade med den här modellen när de läggs till. Detta kan inte ändras efter skapandet. Om du vill se vilken modell som en ansikts-lista som har konfigurerats med, använda den [FaceList - Get] API med den _returnRecognitionModel_ parameteruppsättning som **SANT**.

Se följande kodexempel för .NET-klientbiblioteket.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Den här koden skapar en ansikts-lista som heter `My face collection`med hjälp av den _recognition_02_ modeller för extrahering av funktionen. När du söker ansikts-listan för liknande ansikten till identifierade utseende, den sida måste har identifierats ([Ansiktsigenkänning – identifiera]) med hjälp av den _recognition_02_ modell. Modellen måste vara konsekvent som i föregående avsnitt.

Ingen ändring i den [Ansiktsigenkänning – Sök efter liknande] API; du bara ange modellversion i identifiering.

## <a name="verify-faces-with-specified-model"></a>Verifiera ansikten med angivna modellen

Den [Ansiktsigenkänning – verifiera] API kontrollerar om två ansikten tillhör samma person. Ändring ingen i verifiera API med avseende på modeller för taligenkänning, men du kan bara jämföra ansikten som hittades med samma modell. Därför de två ansiktena båda måste har identifierats med hjälp av `recognition_01` eller `recognition_02`.

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

Om du vill jämföra resultaten från de olika den _recognition_01_ och _recognition_02_ modeller på dina data, måste du:

1. Skapa två **PersonGroup**s med _recognition_01_ och _recognition_02_ respektive.
1. Använd din bilddata för att identifiera ansikten och registrera dem till **Person**s för dessa två **PersonGroup**s och utlösare utbildningen processen med [PersonGroup – träna] API.
1. Testa med [Ansiktsigenkänning – identifiera] på både **PersonGroup**s och jämför resultatet.

Om du anger vanligtvis ett förtroende tröskelvärde (ett värde mellan noll och ett som avgör hur säker modellen måste vara att identifiera ett ansikte), kan du behöva använda olika tröskelvärden för olika modeller. Ett tröskelvärde för en modell är inte avsedd att dela till en annan och kommer inte nödvändigtvis ger samma resultat.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att ange igenkänningsfunktion för att använda med tjänsten för olika Ansikts-API: er. Följ sedan en Snabbstart för att komma igång med ansiktsigenkänning.

* [Identifiera ansikten i en bild](../quickstarts/csharp-detect-sdk.md)

[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Ansiktsigenkänning – Sök efter liknande]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Ansiktsigenkänning – verifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Träna]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
