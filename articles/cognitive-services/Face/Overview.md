---
title: Står inför API-tjänsten översikt | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ordlistan beskrivs de termer som kan uppstå när du arbetar med Ansikts-API-tjänsten.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: fb1d14ff80bf53adc3008d79cc998739ffffde1b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048677"
---
# <a name="what-is-face-api"></a>Vad är Ansikts-API?

Välkommen till Microsoft ansikte API, en molnbaserad tjänst som ger de mest avancerade algoritmerna för står inför. Ansikts-API: et har två huvudsakliga funktioner: står inför identifiering med attribut och står inför igenkänning.

## <a name="face-detection"></a>Ansiktsigenkänning

Ansikts-API identifierar upp till 64 mänsklig personerna bakom hög precision min plats i en bild. Och avbildningen kan anges av-fil i byte eller en giltig URL.

![Översikt – Ansiktsigenkännings-identifiering](./Images/Face.detection.jpg)

Ansikts-rektangel (vänster, överkant, bredd och höjd) som anger min plats i avbildningen returneras tillsammans med varje identifierat står inför. Du kan också extraherar står inför identifiering en serie ansikts-relaterade attribut, till exempel attityd, kön, ålder, head attityd, ansikte hår och glasögon. Mer information finns i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Framsidan igenkänning

Framsidan recognition används ofta i många scenarier inklusive säkerhet, fysiska användargränssnittet, bild analyser av innehållet och hantering, mobila appar och robotics. Fyra ansikte recognition funktioner som tillhandahålls: står inför verifiering, söka efter liknande ytor, står inför gruppering och identifiering.

### <a name="face-verification"></a>Ansiktsverifiering

Ansikts-API verifiering utför en autentisering mot två identifierade ytor eller från en identifierade yta till en person objekt. Mer information finns i [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Söka efter liknande yta

Tjänsten hittar baserat på ett mål som identifierats ansikte och en uppsättning kandidat ytor att söka med, en liten uppsättning ytor som mest liknar de står inför mål. Två fungerande lägen `matchFace` och `matchPerson` stöds. `matchPerson` läget returnerar liknande ytor efter att tillämpa ett tröskelvärde för samma person som härrör från [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). `matchFace` läget ignorerar tröskelvärdet för samma person och returnerar översta liknande kandidat ytor. I följande exempel tar kandidat ytor visas.
![Översikt – Ansiktsigenkännings Sök liknande](./Images/FaceFindSimilar.Candidates.jpg) och fråga yta är ![översikt – Ansiktsigenkännings Sök liknande](./Images/FaceFindSimilar.QueryFace.jpg)

Att hitta fyra liknande ytor `matchPerson` läge returnerar a och b, som tillhör samma person med frågan yta. `matchFace` läget returnerar (a), (b), (c) och (d), exakt fyra kandidater även om låg likhet. Mer information finns i [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Ansiktsgruppering

Med en uppsättning okänd ytor står inför gruppering API automatiskt delas upp dem i olika grupper baserat på likheten. Varje grupp är en uppdelad rätt delmängd av de ursprungliga okända står inför anges, och innehåller liknande ytor. Och alla ytor i samma grupp kan betraktas som hör till samma person objekt. Mer information finns i [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Ansiktidentifiering

Ansikts-API kan användas för att identifiera personer baserat på ett identifierade ansikte och en personer-databas (definierat som en LargePersonGroup/PersonGroup). Skapa den här databasen i förväg, som kan ändras med tiden.

Följande bild är ett exempel på en PersonGroup eller det LargePersonGroup med namnet ”myfriends”. Varje grupp kan innehålla upp till 1 000 000/10 000 person objekt. Under tiden kan kan varje person-objekt ha upp till 248 ytor registrerad.

![Översikt – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

När LargePersonGroup/PersonGroup har skapats och tränats, kan du utföra identifiering mot gruppen och identifierade utseende. Om de står inför identifieras som en person objekt i gruppen, returneras person-objektet.

Mer information om identifiering finns i följande API-guider:

[Står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 
 [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - tåg](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Ansiktslagring

Ansikts-lagring tillåter en prenumeration som Standard att lagra ytterligare beständiga ytor när du använder LargePersonGroup/PersonGroup Person objekt ([PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person - Lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) eller LargeFaceLists/FaceLists ([FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) för identifiering eller likhet matchar med Ansikts-API. Lagrade avbildningar debiteras $0,5 per 1000 ytor och denna kurs är linjärt dagligen. Kostnadsfri nivå prenumerationer är ledigt, men begränsat till 1 000 totala personer.

Priser för framsidan lagring är linjärt dagligen. Om ditt konto används 10 000 beständiga ytor varje dag för den första delen i månaden och ingen exempelvis den andra hälften faktureras bara för de 10 000 ytorna för dagar lagras. Du kan även om varje dag under månaden du bevara 1 000 ytor för några timmar, ta bort dem varje natt faktureras fortfarande för 1 000 beständiga ytor varje dag.

## <a name="getting-started-tutorials"></a>Komma igång Självstudier

Följande kurser visar de grundläggande Ansikts-API-funktioner och processer som prenumerationer:

- [Komma igång med framsidan API i CSharp självstudiekursen](Tutorials/FaceAPIinCSharpTutorial.md)
- [Komma igång med Ansikts-API i Java Android genomgång](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Komma igång med framsidan API i Python självstudiekursen](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Exempelappar

Ta en titt på dessa exempelprogram som använder Ansikts-API.

- [Microsoft ansikte API: Windows klientbiblioteket & exempel](https://github.com/Microsoft/Cognitive-Face-Windows)
  - WPF sample-appen som visar flera scenarier för framsidan identifiering, analys och identifiering.
- [FamilyNotes UWP-appen](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Universella Windowsplattformen (UWP) exempelapp som visar användningen av tal, Cortana, pennanteckningar och kameran via en familj Observera delning scenario.
- [Bildruta Analysis-exempel](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Universella Windowsplattformen (UWP) exempelapp som visar analysera live videoströmmar i nära realtid med framsidan, datorn Vision och Känslo-API: er.

## <a name="related-topics"></a>Närliggande information

- [Viktig information om ansikte API-Version 1.0](ReleaseNotes.md)
- [Hur du identifierar står i avbildningen](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Så här identifierar du står i avbildningen](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
