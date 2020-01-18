---
title: Viktig information – ansikts tjänst
titleSuffix: Azure Cognitive Services
description: Viktig information för ansikts tjänsten innehåller en historik över publicerings ändringar för olika versioner.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165874"
---
# <a name="face-release-notes"></a>Viktig information om ansikte

Den här artikeln gäller Face service version 1,0.

### <a name="release-changes-in-june-2019"></a>Versions ändringar i juni 2019

* Lade till en ny ansikts identifierings modell med förbättrad noggrannhet för små, sido visning, Occluded och suddiga ansikten. Använd IT [-detektering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) genom att ange det nya namnet på ansikts identifierings modellen `detection_02` i `detectionModel` parameter. Mer information om [hur du anger en identifierings modell](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Versions ändringar i april 2019

* Bättre övergripande noggrannhet i `age` och `headPose` attribut. Attributet `headPose` uppdateras också med värdet `pitch` aktiverat nu. Använd de här attributen genom att ange dem i `returnFaceAttributes`-parametern för parametern [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Förbättrad hastighet för [ansikts igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Versions ändringar i mars 2019

* Lade till en ny ansikts igenkännings modell med förbättrad noggrannhet. Använd den genom [ansikts-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) och [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) genom att ange det nya namnet på ansikts igenkännings modellen `recognition_02` i `recognitionModel` parameter. Mer information finns i [så här anger du en igenkännings modell](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Versions ändringar i januari 2019

* Ögonblicks bild funktionen har lagts till för att stödja datamigrering över prenumerationer: [ögonblicks bild](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get) Mer information om [hur du migrerar dina ansikts data till en annan ansikts prenumeration](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Versions ändringar i oktober 2018

* Förfinad beskrivning för `status`, `createdDateTime`, `lastActionDateTime`och `lastSuccessfulTrainingDateTime` i [PersonGroup – Hämta utbildnings](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)status, [LargePersonGroup – Hämta inlärnings status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)och [LargeFaceList – Hämta utbildnings status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Versions ändringar i maj 2018

* Förbättrat `gender`-attribut avsevärt och bättre `age`, `glasses`, `facialHair`, `hair`, `makeup` attribut. Använd dem genom att [identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Öka storleks gränsen för indata-bildfiler från 4 MB till 6 MB i [ansikts-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Lägg till](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)ansikte, [PersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Versions ändringar i mars 2018

* Behållare för miljon skala har lagts till: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) och [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mer information om [hur du använder funktionen för storskalig skalning](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Ökat [ansikte – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parameter från [1, 5] till [1, 100] och standardvärdet 10.

### <a name="release-changes-in-may-2017"></a>Versions ändringar i maj 2017

* Lade till `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`och `noise` attribut i en `returnFaceAttributes` [-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) parameter.

* 10 000 personer som stöds i en PersonGroup och [identifierar ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Sid brytning som stöds i [PersonGroup person-lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) med valfria parametrar: `start` och `top`.

* Samtidighet som stöds vid tillägg/borttagning av ansikten mot olika FaceLists och olika personer i PersonGroup.

### <a name="release-changes-in-march-2017"></a>Versions ändringar i mars 2017
* `emotion` attribut har lagts till i `returnFaceAttributes`s parametern [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* Det gick inte att identifiera sidan igen med den rektangel som returnerades från [ansikts igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som `targetFace` i [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fast den identifierbara ansikts storleken för att se till att det är strikt mellan 36x36 och 4096x4096 bild punkter.

### <a name="release-changes-in-november-2016"></a>Versions ändringar i november 2016
* Lade till Ansiktslagring standard prenumeration för lagring av ytterligare beständiga ansikten när du använder [PersonGroup person – Lägg](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) till ansikte eller [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) för identifiering eller likhets matchning. De lagrade bilderna debiteras med USD 0:50 per 1000 ansikten och priset justeras proportionerligt på daglig basis. Prenumerationer på den kostnads fria nivån fortsätter att vara begränsade till totalt 1 000 personer.

### <a name="release-changes-in-october-2016"></a>Versions ändringar i oktober 2016
* Ett fel meddelande om fler än en ansikte i targetFace har ändrats från det finns fler än ett ansikte i bilden till det finns fler än ett ansikte i bilden i [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Versions ändringar i juli 2016
* Ansikte som stöds för person objekts autentisering i [FACET-verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Valfri `mode` parameter har lagts till och du kan välja mellan två arbets lägen: `matchPerson` och `matchFace` i [FACET, hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och standardvärdet är `matchPerson`.

* Extra `confidenceThreshold` parameter har lagts till för att användaren ska kunna ange tröskelvärdet för huruvida ett ansikte tillhör ett person objekt i [ansikts-identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Valfria `start`-och `top`-parametrar har lagts till i [PersonGroup-listan](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) så att användaren kan ange start punkten och det totala antalet PersonGroups som ska visas.

### <a name="v10-changes-from-v0"></a>V 1.0 ändras från v0
* Tjänstens rot slut punkt har uppdaterats från ```https://westus.api.cognitive.microsoft.com/face/v0/``` till ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Ändringarna tillämpas på: [ansikts-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [ansikte-Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ansikte – hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [ansikts grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Uppdaterade storleken på den minimala identifierade ytan till 36x36 bild punkter. Ansikten som är mindre än 36x36 pixlar kommer inte att identifieras.

* Föråldrade PersonGroup och person uppgifter i ansikts v0. Dessa data kan inte nås med Face V 1.0-tjänsten.

* Föråldrad v0-slutpunkt för Ansikts-API den 30 juni 2016.
