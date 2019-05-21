---
title: Identifiera ansikten och attribut begrepp
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp om ansiktsigenkänning och ansiktsattribut.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891171"
---
# <a name="face-detection-and-attributes"></a>Identifiera ansikten och attribut

Den här artikeln förklarar begreppet ansiktsigenkänning och ansikte attributdata. Ansiktsigenkänning är åtgärden för att hitta ansikten i en bild och du kan också returnera olika typer av ansikts-relaterade data.

Du använder den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) åtgärd för att identifiera ansikten i en bild. Minst motsvarar varje identifierad ansikte ett faceRectangle fält i svaret. Pixel koordinaterna för vänster, top, bredd och höjd Markera hitta ansiktet. Med dessa koordinater kan visa du platsen för ansiktet och dess storlek. I API-svaret visas ansikten i storlek ordning från största till minsta.

## <a name="face-id"></a>Ansikts-ID

Ansikts-ID är en unik identifierarsträng för varje identifierad ansikte i en bild. Du kan begära en face ID i din [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-anrop.

## <a name="face-landmarks"></a>Ansiktslandmärken

Ansiktslandmärken består av ett ansikte, till exempel eleverna eller tips främre enkelt att hitta punkter. Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar alla 27 punkter:

![Ett ansikte diagram med alla 27 ansiktslandmärken märkt](../Images/landmarks.1.jpg)

Koordinaterna för punkterna som returneras i antal bildpunkter.

## <a name="attributes"></a>Attribut

Attribut är en uppsättning funktioner som eventuellt kan identifieras av den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Följande attribut kan identifieras:

* **Ålder**. Beräknad ålder i år med ett visst ansikte.
* **Oskärpa**. Blurriness av ansikte i bilden. Det här attributet returnerar ett värde mellan 0 och en och en informell klassificering av låg, medel eller hög.
* **Igenkänning**. En lista över känslor sina tryggt identifiering för det angivna ansiktet. Förtroende poäng normaliseras och poäng över alla känslor lägga till upp till en. Känslor som returneras är lycka, sorg, neutral, ilska, förakt, avsky, överraskning och behöva betala.
* **Exponering**. Exponering av ansikte i bilden. Det här attributet returnerar ett värde mellan 0 och en och en informell klassificering av Underexponering, goodExposure eller överexponering.
* **Ansiktsigenkänning hår**. Beräknad ansiktsigenkänning hår förekomst och längden för det angivna ansiktet.
* **Kön**. Visst ansikte uppskattade kön. Möjliga värden är manlig kvinnliga och genderless.
* **Glasögon**. Om har visst ansikte glasögon. Möjliga värden är NoGlasses, ReadingGlasses, Solglasögonföretag och Swimming skyddsglasögon.
* **Hår**. Typ av ansiktet hår. Det här attributet visas om hår är synliga, om baldness identifieras, och vilka färger som hår identifieras.
* **HEAD utgöra**. De står inför orientering i 3D-utrymmet. Det här attributet beskrivs genom försäljningsargument, återställning och positionen vinklar i grader. Värdeintervall är 90 grader till 90 grader, -180 grader och 180 grader och 90 grader och 90 grader. Se i följande diagram för vinkel mappningar:

    ![En head med motivation, distribuera och yaw axlar med etiketten](../Images/headpose.1.jpg)
* **Makeup**. Om de står inför har makeup. Det här attributet returnerar ett booleskt värde för eyeMakeup och lipMakeup.
* **Bruset**. Visual bruset har identifierats i ansiktsbilden. Det här attributet returnerar ett värde mellan 0 och en och en informell klassificering av låg, medel eller hög.
* **Ocklusion**. Om det finns objekt som blockerar delar av ansiktet. Det här attributet returnerar ett booleskt värde för eyeOccluded, foreheadOccluded och mouthOccluded.
* **Le**. Leende uttryck av de angivna står inför. Det här värdet är mellan noll för inga leende och en för en tydlig Leende.

> [!IMPORTANT]
> Ansiktsattribut förutse med statistisk algoritmerna. De kanske inte alltid är korrekt. Var försiktig när du fatta beslut utifrån attributdata.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att din indatabilder ger de bästa resultaten av programuppdateringsidentifieringen:

* Format som stöds inmatad bild är JPEG, PNG, GIF för första bildruta och BMP.
* Bildens filstorlek bör inte vara större än 4 MB.
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kan inte identifieras.
* Vissa ansikten kanske inte upptäcks på grund av tekniska utmaningar. Extreme ansikte vinklar (head attityd) eller ansikte ocklusion (objekt, till exempel solglasögonföretag eller händer som blockerar en del av de står inför) kan påverka identifiering. Främre och nästan direkt ansikten ger bäst resultat.

Om du identifierar ansikten från en video feed du förbättra prestanda genom att justera vissa inställningar i kameran:

* **Smoothing**: Många video kameror gäller en utjämningsläge effekt. Du bör inaktivera detta om du inte kan eftersom den skapar en oskärpa mellan ramar och minskar tydligare.
* **Slutarhastighet**: En snabbare slutarhastighet minskar mängden rörelse mellan ramar och gör varje bildruta tydligare. Vi rekommenderar slutare av 1/60 sekunders eller snabbare.
* **Slutartid vinkel**: Vissa kameror ange slutarvinkel i stället för slutarhastighet. Om möjligt bör du använda en lägre slutarvinkel. Detta resulterar i tydligare bildrutor.

    >[!NOTE]
    > En kamera med en lägre slutarvinkel får mindre ljus i varje tidsperiod, så att bilden mörkare. Du måste fastställa rätt nivå om du vill använda.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med principerna för identifiering av ansikte, lär du dig hur du skriva ett skript som identifierar ansikten i en viss avbildning.

* [Identifiera ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)