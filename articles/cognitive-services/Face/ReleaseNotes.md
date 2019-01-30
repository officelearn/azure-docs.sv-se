---
title: Viktig information – Ansikts-API-tjänsten
titleSuffix: Azure Cognitive Services
description: Viktig information för Ansikts-API-tjänsten omfattar en historik över ändringar i versionen för olika versioner.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 545f3669f04c3cd30f4766357c5d582dfe515c80
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215596"
---
# <a name="face-api-release-notes"></a>Ansikts-API: et viktig information

Den här artikeln gäller Ansikts-API-tjänst version 1.0.

### <a name="release-changes-in-may-2018"></a>Versionen ändringar i maj 2018

* Förbättrad `gender` attributet avsevärt och även förbättrat `age`, `glasses`, `facialHair`, `hair`, `makeup` attribut. Använd dem via [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametern. 

* Avbildning av ökade gränsen för filstorlek från 4 MB till 6 MB i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Lägg till Ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Versionen ändringar i mars 2018

* Miljonskala-Behållargrupp har lagts till: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) och [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mer information finns i [hur funktionen storskaliga](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Ökad [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametern från [1, 5] till [1, 100] och standard till 10.

### <a name="release-changes-in-may-2017"></a>Versionen ändringar i maj 2017

* Lagt till `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, och `noise` attribut i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametern.

* Stöd för 10K personer i en PersonGroup och [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Stöd för sidbrytning i [PersonGroup Person - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) med valfria parametrar: `start` och `top`.

* Stöds samtidighet i att lägga till/ta bort ansikten mot olika FaceLists och olika personer i PersonGroup.

### <a name="release-changes-in-march-2017"></a>Versionen ändringar i mars 2017
* Lagt till `emotion` attributet i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametern.

* Fast ansiktet inte kunde igen med rektangel som returnerades från [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som `targetFace` i [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fasta flashminnet ansikte storleken att kontrollera att det är strikt mellan 36 x 36 till 4096 x 4 096 pixlar.

### <a name="release-changes-in-november-2016"></a>Ändringar av versionen i November 2016
* Lagt till Face Storage Standard prenumeration lagra ytterligare sparade ansikten när du använder [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) eller [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) för identifiering eller likheter matchar. De lagrade bilderna debiteras med USD 0:50 per 1000 ansikten och priset justeras proportionerligt på daglig basis. Kostnadsfri nivå prenumerationer fortsätter att vara begränsad till 1 000 totala personer.

### <a name="release-changes-in-october-2016"></a>Versionen ändringar i oktober 2016
* Ändrat felmeddelandet för mer än ett ansikte i targetFace från ”det finns mer än en yta i bild' till 'Är det mer än ett ansikte i bilden' i [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Versionen ändringar i juli 2016
* Stöds ansikte till Person objektet autentisering i [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Lagt till valfritt `mode` parameter som möjliggör val av två lägen för fungerande: `matchPerson` och `matchFace` i [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och standardvärdet är `matchPerson`.

* Lagt till valfritt `confidenceThreshold` parametern för användaren att ange tröskelvärdet på om ett ansikte tillhör en personobjektet i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Lagt till valfritt `start` och `top` parametrar i [PersonGroup - listan](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) att aktivera användaren anger startpunkten och det totala antalet PersonGroups listan.

### <a name="v10-changes-from-v0"></a>V1.0 ändras från V0
* Uppdatera tjänsten rot-slutpunkten från ```https://westus.api.cognitive.microsoft.com/face/v0/``` till ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Ändringar tillämpas på: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Ansiktsigenkänning – Sök efter liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Uppdaterade minimal flashminnet ansikte storleken till 36 x 36 bildpunkter. Kommer inte att identifiera ansikten som är mindre än 36 x 36 bildpunkter.

* Föråldrad PersonGroup och Person data i Ansikts-V0. Dessa data kan inte nås med Face V1.0-tjänsten.

* Föråldrad V0-slutpunkten för Ansikts-API den 30 juni 2016.
