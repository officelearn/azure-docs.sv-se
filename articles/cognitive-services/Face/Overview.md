---
title: Vad är Ansikts-API-tjänsten?
titleSuffix: Azure Cognitive Services
description: Ordlistan beskrivs de termer som du kan stöta på när du arbetar med Ansikts-API-tjänsten.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123478"
---
# <a name="what-is-the-face-api-service"></a>Vad är Ansikts-API-tjänsten?

Välkommen till tjänsten Ansikts-API, en molnbaserad tjänst som tillhandahåller verkligt avancerade ansiktsigenkänningsalgoritmer. Ansikts-API har två huvudfunktioner: ansiktsavkänning med attribut och ansiktsigenkänning.

## <a name="face-detection"></a>Ansiktsavkänning

Ansikts-API identifierar upp till 64 mänskliga ansikten med hög platsprecision i en bild. Och avbildningen kan anges med fil i byte eller en giltig URL.

![Översikt – Ansiktsavkänning](./Images/Face.detection.jpg)

Ansiktsrektangeln (vänster, topp, bredd och höjd) som anger ansiktets plats i bilden returneras tillsammans med varje identifierat ansikte. Alternativt kan ansiktsavkänning extrahera en serie ansiktsrelaterade attribut som hållning, kön, ålder, huvudhållning, ansiktsbehåring och glasögon. Mer information finns i [Ansikte – Igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Ansiktsigenkänning

Ansiktsigenkänning används ofta i olika scenarier, som bl.a. involverar säkerhet, naturliga användargränssnitt, analys och hantering av bildinnehåll, mobilappar och robotteknik. Fyra funktioner för ansiktsigenkänning tillhandahålls: ansiktsverifiering, hitta liknande ansikten, ansiktsgruppering och personidentifiering.

### <a name="face-verification"></a>Ansiktsverifiering

Ansiktsverifiering i Ansikts-API utför en autentisering mot två identifierade ansikten eller autentisering från ett identifierat ansikte till ett personobjektet. Mer detaljerad information finns i [Ansikte – Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Hitta liknande ansikten

Givet ett identifierat målansikte och en uppsättning alternativa ansikten att söka med, så hittar tjänsten en liten uppsättning ansikten som mest liknar målansiktet. Två fungerande lägen, `matchFace` och `matchPerson`, stöds. `matchPerson`-läget returnerar liknande ansikten efter att ha tillämpat ett samma-person-tröskelvärde som härletts från [Ansikte – Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). `matchFace`-läget ignorerar samma-person-tröskelvärdet och returnerar de ansiktsalternativ som har störst likhet. I det följande exemplet är ansiktsalternativen listade.
![Översikt – Sök liknande ansikte](./Images/FaceFindSimilar.Candidates.jpg) och förfrågat ansikte är ![Översikt – Sök liknande ansikte](./Images/FaceFindSimilar.QueryFace.jpg)

Om du vill hitta fyra liknande ansikten returnerar `matchPerson`-läget (a) och (b), vilka tillhör samma person med förfrågat ansikte. `matchFace`-läget returnerar (a), (b), (c) och (d) – exakt fyra alternativ även om likheten är låg. Mer information finns i [Ansikte – Hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Ansiktsgruppering

Med en uppsättning okända ansikten delar ansiktsgrupperings-API:et automatiskt in dem i olika grupper baserat på likheter. Varje grupp är en osorterad egentlig delmängd av den ursprungliga uppsättningen okända ansikten, och innehåller liknande ansikten. Och alla ansikten i samma grupp kan anses tillhöra samma personobjekt. Mer information finns i [Ansikte – Grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Ansiktsidentifiering

Ansikts-API kan användas för att identifiera personer baserat på ett identifierat ansikte och en persondatabas (definieras som en LargePersonGroup/PersonGroup). Skapa den här databasen i förväg, och redigera den sedan över tid.

Följande bild är ett exempel på en LargePersonGroup/PersonGroup med namnet ”myfriends”. Varje grupp kan innehålla upp till 1 000 000/10 000 personobjekt. Varje personobjekt kan samtidigt ha upp till 248 registrerade ansikten.

![Översikt – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

När en LargePersonGroup/PersonGroup har skapats och tränats kan identifiering genomföras mot gruppen och ett nytt identifierat ansikte. Om ansiktet identifieras som ett personobjekt i gruppen, så returneras personobjektet.

Mer information om identifiering finns i följande API-guider:

[Ansikte – Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup – Träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 
 [LargePersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup – Träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Ansiktslagring

Med Ansiktslagring kan en standardprenumeration lagra ytterligare sparade ansikten vid användning av objekten LargePersonGroup/PersonGroup Person ([PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) eller LargeFaceLists/FaceLists ([FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) för identifiering eller likhetsmatchning med Ansikts-API. De lagrade bilderna debiteras med USD 0:50 per 1000 ansikten och priset justeras proportionerligt på daglig basis. Prenumerationer på kostnadsfri nivå är kostnadsfria, men begränsade till totalt 1 000 personer.

Prissättningen för Ansiktslagring beräknas per dag. Om ditt kontot till exempel har använt 10 000 sparade ansikten varje dag under månadens första 15 dagar men inga under resten av månaden, debiteras du bara för de 10 000 ansiktena för de dagar de lagrats. Alternativt: Om du varje dag under månaden sparar 1 000 ansikten i några timmar och sedan tar bort dem varje kväll debiteras du ändå för 1 000 sparade ansikten varje dag.

## <a name="getting-started-tutorials"></a>Självstudier för att komma igång

Följande självstudier visar de grundläggande funktionerna och prenumerationsprocesserna i Ansikts-API:

- [Självstudier: Komma igång med Ansikts-API i CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Självstudier: Komma igång med Ansikts-API i Java för Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Självstudier: Komma igång med Ansikts-API i Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Exempelappar

Ta en titt på dessa exempelprogram som använder Ansikts-API.

- [Microsoft Ansikts-API: Windows-klientbibliotek och exempel](https://github.com/Microsoft/Cognitive-Face-Windows)
  - WPF-exempelapp som visar flera olika användningsområden för ansiktsavkänning, analys och identifiering.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Universal Windows Platform (UWP) är en exempelapp som visar användningen av tal, Cortana, pennanteckningar och kamera via ett familjeanteckningsscenario.
- [Exempel på bildruteanalys](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Universal Windows Platform (UWP) är en exempelapp som visar analys av livevideoströmmar i nästintill realtid med Ansikts-API, API för visuellt innehåll och Känslo-API.

## <a name="related-topics"></a>Relaterade ämnen

- [Ansikts-API, version 1.0 Viktig information](ReleaseNotes.md)
- [Känna igen ansikten i en bild](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Identifiera ansikten i en bild](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
