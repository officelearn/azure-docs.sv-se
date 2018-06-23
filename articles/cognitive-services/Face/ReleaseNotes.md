---
title: Viktig information för Ansikts-API-tjänsten | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Viktig information för Ansikts-API-tjänsten innehåller en historik över versionen ändringar för olika versioner.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 918b3ea5dbaaa44e4eee1a679354c7becffd4686
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355080"
---
# <a name="face-api-release-notes"></a>Ansikts-API viktig information

Den här artikeln gäller Microsoft ansikte API-tjänsten version 1.0.

### <a name="release-changes-in-may-2018"></a>Viktiga ändringar i maj 2018

* Förbättrad `gender` attributet bättre avsevärt och även `age`, `glasses`, `facialHair`, `hair`, `makeup` attribut. Använd dem via [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Ökad inkommande avbildningen storleksbegränsningen från 4 MB till 6 MB i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Lägg till Ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) och [LargePersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Viktiga ändringar i mars 2018

* Tillagda miljoner skala behållare: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) och [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mer information finns i [hur funktionen storskaliga](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Ökad [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametern från [1, 5] till [1, 100] och standard-10.

### <a name="release-changes-in-may-2017"></a>Viktiga ändringar i maj 2017

* Lägga till `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, och `noise` attribut i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter.

* Stöds 10K personer i en PersonGroup och [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Stöd för sidbrytning i [PersonGroup Person - listan](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) med valfria parametrar: `start` och `top`.

* Stöds samtidighet i att lägga till/ta bort ytor mot olika FaceLists och andra personer i PersonGroup.

### <a name="release-changes-in-march-2017"></a>Viktiga ändringar i mars 2017
* Lägga till `emotion` attribut i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter.

* Fast de står inför inte kunde igen med rektangeln som returnerades från [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som `targetFace` i [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fast storlek kan upptäckas står inför att se till att det enbart mellan 36 x 36 till 4096 x 4 096 pixlar.

### <a name="release-changes-in-november-2016"></a>Viktiga ändringar i November 2016
* Lägga till står inför lagring Standard prenumeration för att lagra ytterligare beständiga ytor när du använder [PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) eller [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) för identifiering eller likhet matchar. Lagrade avbildningar debiteras $0,5 per 1000 ytor och denna kurs är linjärt dagligen. Kostnadsfri nivå prenumerationer fortsätter att vara begränsad till 1 000 totala personer.

### <a name="release-changes-in-october-2016"></a>Viktiga ändringar i oktober 2016
* Ett felmeddelande av mer än ett ansikte i targetFace från ”det finns mer än en yta bild' till 'Är det mer än en yta bild' har ändrats i [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) och [PersonGroup Person - Lägg till min](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Viktiga ändringar i juli 2016
* Stöds ansikte Person objektet autentisering i [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Lägga till valfria `mode` parameter som möjliggör val av två fungerande lägen: `matchPerson` och `matchFace` i [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och standardvärdet är `matchPerson`.

* Lägga till valfria `confidenceThreshold` parameter för användaren att ange ett tröskelvärde för om en yta tillhör en Person objekt i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Lägga till valfria `start` och `top` parametrar i [PersonGroup - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) så att användare ska ange startpunkten och det totala antalet PersonGroups listan.

### <a name="v10-changes-from-v0"></a>V1.0 ändringar från V0
* Uppdatera rot tjänstslutpunkten från ```https://westus.api.cognitive.microsoft.com/face/v0/``` till ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Ändringarna tillämpas på: [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Uppdatera storleken på minimala kan upptäckas ansikte 36 x 36 bildpunkter. Ytor som är mindre än 36 x 36 bildpunkter hittas inte.

* Föråldrad PersonGroup och Person data i ansikte V0. Dessa data kan inte användas med tjänsten ansikte V1.0.

* Föråldrad V0-slutpunkten för Ansikts-API på 30 juni 2016.
