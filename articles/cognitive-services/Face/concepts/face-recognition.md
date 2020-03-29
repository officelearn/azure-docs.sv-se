---
title: Begrepp för ansiktsigenkänning
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs begreppen verifiera, hitta liknande, gruppera och identifiera ansiktsigenkänningsåtgärder och underliggande datastrukturer.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743073"
---
# <a name="face-recognition-concepts"></a>Begrepp för ansiktsigenkänning

I den här artikeln beskrivs begreppen verifiera, hitta liknande, gruppera och identifiera ansiktsigenkänningsåtgärder och underliggande datastrukturer. I stort sett beskriver erkännandet arbetet med att jämföra två olika ansikten för att avgöra om de är likartade eller tillhör samma person.

## <a name="recognition-related-data-structures"></a>Igenkänningsrelaterade datastrukturer

Erkännandeåtgärderna använder huvudsakligen följande datastrukturer. Dessa objekt lagras i molnet och kan refereras av deras ID-strängar. ID-strängar är alltid unika i en prenumeration. Namnfält kan dupliceras.

|Namn|Beskrivning|
|:--|:--|
|DetectedFace| Den här enskilda ansiktsrepresentationen hämtas av [ansiktsigenkänningsåtgärden.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) Dess ID upphör att gälla 24 timmar efter att det har skapats.|
|PersistedFace| När DetectedFace-objekt läggs till i en grupp, till exempel FaceList eller Person, blir de PersistedFace-objekt. De kan [hämtas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) när som helst och upphör inte att gälla.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) eller [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Den här datastrukturen är en blandad lista över PersistedFace-objekt. En FaceList har ett unikt ID, en namnsträng och en användardatasträng.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Den här datastrukturen är en lista över PersistedFace-objekt som tillhör samma person. Den har ett unikt ID, en namnsträng och eventuellt en användardatasträng.|
|[Persongroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) eller [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Den här datastrukturen är en blandad lista över personobjekt. Den har ett unikt ID, en namnsträng och eventuellt en användardatasträng. En PersonGroup måste [tränas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) innan den kan användas i igenkänningsåtgärder.|

## <a name="recognition-operations"></a>Erkännandeåtgärder

I det här avsnittet beskrivs hur de fyra igenkänningsåtgärderna använder de datastrukturer som tidigare beskrivits. En bred beskrivning av varje igenkänningsåtgärd finns i [Översikt](../Overview.md).

### <a name="verify"></a>Verifiera

[Åtgärden Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) tar ett ansikts-ID från DetectedFace eller PersistedFace och antingen ett annat ansikts-ID eller ett personobjekt och avgör om de tillhör samma person. Om du skickar in ett personobjekt kan du eventuellt skicka in en persongrupp som personen tillhör för att förbättra prestanda.

### <a name="find-similar"></a>Hitta liknande

Åtgärden [Hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) tar ett ansikts-ID från DetectedFace eller PersistedFace och antingen en FaceList eller en matris med andra ansikts-ID: er. Med en FaceList returnerar den en mindre FaceList av ansikten som liknar det givna ansiktet. Med en matris med ansikts-ID:er returnerar den på samma sätt en mindre matris.

### <a name="group"></a>Grupp

[Åtgärden Grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) tar en matris med olika ansikts-ID:er från DetectedFace eller PersistedFace och returnerar samma ID grupperade i flera mindre matriser. Varje "grupper"-matris innehåller ansikts-ID:er som ser likadana ut. En enda "messyGroup"-matris innehåller ansikts-ID:er för vilka inga likheter hittades.

### <a name="identify"></a>Identifiera

Åtgärden [Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) tar ett eller flera ansikts-ID:er från DetectedFace eller PersistedFace och en persongrupp och returnerar en lista över personobjekt som varje ansikte kan tillhöra. Returnerade personobjekt är raderade som kandidatobjekt, som har ett förutsägelsekontroendevärde.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att indatabilderna ger de mest exakta igenkänningsresultaten:

* De indatabildformat som stöds är JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildfilstorleken bör inte vara större än 4 MB.
* När du skapar personobjekt använder du foton med olika typer av vinklar och belysning.
* Vissa ansikten kanske inte känns igen på grund av tekniska utmaningar, till exempel:
  * Bilder med extrem belysning, till exempel svår bakgrundsbelysning.
  * Hinder som blockerar ett eller båda ögonen.
  * Skillnader i hårtyp eller ansiktshår.
  * Förändringar i ansiktsutseende på grund av ålder.
  * Extrema ansiktsuttryck.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med ansiktsigenkänningsbegrepp kan du lära dig hur du skriver ett skript som identifierar ansikten mot en tränad PersonGroup.

* [Identifiera ansikten i bilder](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)