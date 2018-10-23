---
title: Vad är Ansikts-API-tjänsten?
titleSuffix: Azure Cognitive Services
description: I det här avsnittet beskrivs Ansikts-API-tjänsten och relaterade termer.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310410"
---
# <a name="what-is-the-face-api-service"></a>Vad är Ansikts-API-tjänsten?

Ansikts-API-tjänsten är en molnbaserad tjänst som tillhandahåller algoritmer för att analysera ansikten i bilder och video. Ansikts-API har två huvudfunktioner: ansiktsavkänning med attribut och ansiktsigenkänning.

## <a name="face-detection"></a>Ansiktsspårning

Ansikts-API kan identifiera upp till 64 mänskliga ansikten med hög platsprecision i en bild. Bilden kan anges av fil (en byteström) eller en giltig URL.

![Översikt – Ansiktsavkänning](./Images/Face.detection.jpg)

Ansiktsrektangeln (vänster, topp, bredd och höjd) som anger ansiktets plats i bilden returneras tillsammans med varje identifierat ansikte. Alternativt kan ansiktsavkänning extrahera en serie ansiktsrelaterade attribut som hållning, kön, ålder, huvudhållning, ansiktsbehåring och glasögon. Mer information finns i [Ansikte – Igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Ansiktsigenkänning

Förmågan att identifiera ansikten är viktig i många scenarier som säkerhet, naturligt användargränssnitt, analys och hantering av bildinnehåll, mobilappar och robotteknik. Ansikts-API-tjänsten har fyra funktioner för ansiktsigenkänning: ansiktsverifiering, hitta liknande ansikten, ansiktsgruppering och personidentifiering.

### <a name="face-verification"></a>Ansiktsverifiering

Ansiktsverifiering utför en autentisering mot två identifierade ansikten eller från ett identifierat ansikte till ett personobjektet. Mer detaljerad information finns i [Ansikte – Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Hitta liknande ansikten

Givet ett identifierat målansikte och en uppsättning alternativa ansikten att söka med, så hittar tjänsten en liten uppsättning ansikten som mest liknar målansiktet. Två arbetslägen, **matchFace** och **matchPerson**, stöds. Läget **matchPerson** returnerar liknande ansikten efter att ha tillämpat ett samma-person-tröskelvärde som härletts från [Ansikte – Verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Läget **matchFace** ignorerar samma-person-tröskelvärdet och returnerar de ansiktsalternativ som har störst likhet. I det följande exemplet är ansiktsalternativen listade.
![Översikt – Ansikte – Hitta liknande](./Images/FaceFindSimilar.Candidates.jpg) Det här är frågans ansikte.
![Översikt – Ansikte – Hitta liknande](./Images/FaceFindSimilar.QueryFace.jpg)

För att hitta fyra liknande ansikten skulle läget **matchPerson** returnera (a) och (b), vilket visar samma person som frågeansiktet. Läget **matchFace** returnerar (a), (b), (c) och (d), exakt fyra kandidater även om vissa har låg likhet. Mer information finns i [Ansikte – Hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Ansiktsgruppering

Med en uppsättning okända ansikten delar ansiktsgrupperings-API:et automatiskt in dem i olika grupper baserat på likheter. Varje grupp är en osorterad egentlig delmängd av den ursprungliga uppsättningen okända ansikten, och innehåller liknande ansikten. Alla ansikten i samma grupp kan anses tillhöra samma person. Mer information finns i [Ansikte – Grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Personidentifiering

Ansikts-API kan användas till att identifiera personer utifrån ett identifierat ansikte och en databas med personer. Du kan skapa den här databasen i förväg, och redigera den sedan över tid.

Följande bild är ett exempel på en databas med namnet ”myfriends”. Varje grupp kan innehålla upp till 1 000 000/10 000 olika personobjekt. Varje personobjekt kan ha upp till 248 registrerade ansikten.

![Översikt – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

När en databas har skapats och tränats kan identifiering utföras mot gruppen med ett nytt identifierat ansikte. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om identifiering finns i följande API-guider:

[Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[PersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup – Träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[LargePersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup – Träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Ansiktslagring och prissättning

Med Ansiktslagring kan en standardprenumeration lagra ytterligare sparade ansikten vid användning av objekten LargePersonGroup/PersonGroup Person ([PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) eller LargeFaceLists/FaceLists ([FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) för identifiering eller likhetsmatchning med Ansikts-API. De lagrade bilderna debiteras med 0,50 USD per 1 000 ansikten och priset justeras proportionerligt på daglig basis. Prenumerationer på kostnadsfri nivå är begränsade till totalt 1 000 personer.

Om ditt kontot till exempel har använt 10 000 sparade ansikten varje dag under månadens första 15 dagar men inga under resten av månaden, debiteras du bara för de 10 000 ansiktena för de dagar de lagrats. Alternativt: Om du varje dag under månaden sparar 1 000 ansikten i några timmar och sedan tar bort dem varje kväll debiteras du ändå för 1 000 sparade ansikten varje dag.

## <a name="sample-apps"></a>Exempelappar

Ta en titt på dessa exempelprogram som använder Ansikts-API.

- [Microsoft Ansikts-API: Windows-klientbibliotek och exempel](https://github.com/Microsoft/Cognitive-Face-Windows)
  - WPF-exempelapp som visar flera olika användningsområden för ansiktsavkänning, analys och identifiering.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Universal Windows Platform (UWP) är en exempelapp som visar användningen av tal, Cortana, pennanteckningar och kamera via ett familjeanteckningsscenario.
- [Exempel på bildruteanalys](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Win32 är en exempelapp som visar analys av livevideoströmmar i nästintill realtid med Ansikts-API, API för visuellt innehåll och Känslo-API.

## <a name="tutorials"></a>Självstudier
Följande självstudier visar de grundläggande funktionerna och prenumerationsprocesserna i Ansikts-API:
- [Självstudier: Komma igång med Ansikts-API i CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Självstudier: Komma igång med Ansikts-API i Java för Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Självstudier: Komma igång med Ansikts-API i Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>Nästa steg

Testa en snabbstart för att implementera en enkelt scenario för Ansikts-API.
- [Snabbstart: Identifiera ansikten i en bild med C#](quickstarts/csharp.md) (andra språk som är tillgänglig)
