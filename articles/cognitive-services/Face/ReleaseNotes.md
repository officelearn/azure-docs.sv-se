---
title: Viktig information - Ansiktsservice
titleSuffix: Azure Cognitive Services
description: Viktig information för Face-tjänsten innehåller en historik över versionsändringar för olika versioner.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165874"
---
# <a name="face-release-notes"></a>Anteckningar för ansiktsutgåva

Den här artikeln gäller Face service version 1.0.

### <a name="release-changes-in-june-2019"></a>Release förändringar i juni 2019

* Lade till en ny ansiktsigenkänningsmodell med förbättrad noggrannhet på små, sidovyer, ockluderade och suddiga ansikten. Använd den via [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) genom att ange det nya ansiktsidentifieringsmodellnamnet `detection_02` i `detectionModel` parametern. Mer information i [Så här anger du en identifieringsmodell](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Release förändringar i april 2019

* Förbättrad total noggrannhet `age` och `headPose` attribut. Attributet `headPose` uppdateras också `pitch` med värdet aktiverat nu. Använd dessa attribut genom att `returnFaceAttributes` ange dem i parametern [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Förbättrad hastighet på [ansikte - Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [persongruppsperson - Lägg till face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [largepersongrupp - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Release förändringar i mars 2019

* Lade till en ny ansiktsigenkänningsmodell med förbättrad noggrannhet. Använd den via [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) and [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) by specifying the new face recognition model name `recognition_02` in `recognitionModel` parameter. Mer information i [Så här anger du en igenkänningsmodell](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Release förändringar i januari 2019

* Funktionen Lade till ögonblicksbild för att stödja datamigrering mellan prenumerationer: [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mer information i [Så här migrerar du dina ansiktsdata till en annan Face-prenumeration](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Release förändringar i oktober 2018

* Förfinad beskrivning `status` `createdDateTime`för `lastActionDateTime`, `lastSuccessfulTrainingDateTime` , och i [PersonGroup - Hämta träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)och [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Release förändringar i maj 2018

* Förbättrat `gender` attribut avsevärt `age`och `glasses` `facialHair`även `hair` `makeup` förbättrat , , , , attribut. Använd dem via [Face - Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Ökad filstorleksgräns för indata från 4 MB till 6 MB i [face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Release förändringar i mars 2018

* Lade till behållare i miljoner skala: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) och [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mer information i [Hur du använder den storskaliga funktionen](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Ökad [face - Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametern från [1, 5] till [1, 100] och standard till 10.

### <a name="release-changes-in-may-2017"></a>Release förändringar i maj 2017

* Lade `hair` `makeup`till `accessory` `occlusion`, `blur` `exposure`, `noise` , , och attribut i parametern [Face - Detect.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`

* Stöds 10K personer i en PersonGroup och [Face - Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Sidnumreringen som stöds i [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) `start` Person - Lista med valfria parametrar: och `top`.

* Samtidighet som stöds när du lägger till/tar bort ansikten mot olika facelists och olika personer i PersonGroup.

### <a name="release-changes-in-march-2017"></a>Release förändringar i mars 2017
* Lagt `emotion` till attribut i face [- detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter.

* Fast att ansiktet inte kunde ritas om med rektangel `targetFace` som returnerats från Face - [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som i [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fast detekterbara ansikte storlek för att se till att det är strikt mellan 36x36 till 4096x4096 pixlar.

### <a name="release-changes-in-november-2016"></a>Release förändringar i november 2016
* Lade till Face Storage Standard-prenumeration för att lagra ytterligare beständiga ansikten när du använder [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) eller [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) för identifiering eller likhetsmatchning. De lagrade bilderna debiteras med USD 0:50 per 1000 ansikten och priset justeras proportionerligt på daglig basis. Gratis abonnemang fortsätter att vara begränsade till 1 000 personer totalt.

### <a name="release-changes-in-october-2016"></a>Release förändringar i oktober 2016
* Ändrade felmeddelandet för mer än ett ansikte i targetFace från "Det finns mer än ett ansikte i bilden" till "Det finns mer än ett ansikte i bilden" i [FaceList - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Release förändringar i juli 2016
* Autentisering av ansikte mot person-objekt i [Ansikte - Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Lade `mode` till valfri parameter som möjliggör `matchPerson` val `matchFace` av två arbetslägen: och i [Face - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och standard är `matchPerson`.

* Lade `confidenceThreshold` till valfri parameter för användaren att ange tröskelvärdet för om ett ansikte tillhör ett personobjekt i [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Lade `start` till `top` valfria och parametrar i [PersonGroup - Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) så att användaren kan ange startpunkten och det totala persongruppersnumret som ska visas.

### <a name="v10-changes-from-v0"></a>V1.0-ändringar från V0
* Uppdaterad tjänstrotslutpunkt ```https://westus.api.cognitive.microsoft.com/face/v0/``` ```https://westus.api.cognitive.microsoft.com/face/v1.0/```från till . Ändringar som tillämpas på: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), Face - [Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), Ansikte - [Hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och Face [- Grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Uppdaterade den minimala detekterbara ansiktsstorleken till 36x36 pixlar. Ansikten som är mindre än 36x36 pixlar kommer inte att upptäckas.

* Inaktuella persongrupps- och persondata i Face V0. Dessa data kan inte nås med Face V1.0-tjänsten.

* Inaktuella V0-slutpunkten för Ansikts-API den 30 juni 2016.
