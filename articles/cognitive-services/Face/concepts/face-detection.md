---
title: Begrepp för ansiktsidentifiering och attribut
titleSuffix: Azure Cognitive Services
description: Ansiktsigenkänning är effekten av att hitta mänskliga ansikten i en bild och eventuellt returnera olika typer av ansiktsrelaterade data.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743064"
---
# <a name="face-detection-and-attributes"></a>Ansiktsigenkänning och attribut

I den här artikeln beskrivs begreppen ansiktsidentifiering och ansiktsattributdata. Ansiktsigenkänning är effekten av att hitta mänskliga ansikten i en bild och eventuellt returnera olika typer av ansiktsrelaterade data.

Du använder åtgärden [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för att identifiera ansikten i en bild. Varje detekterad yta motsvarar minst ett faceRectangle-fält i svaret. Den här uppsättningen pixelkoordinater för vänster, överkant, bredd och höjd markerar den placerade ytan. Med hjälp av dessa koordinater kan du få platsen för ansiktet och dess storlek. I API-svaret visas ansikten i storleksordning från största till minsta.

## <a name="face-id"></a>Ansikts-ID

Ansikts-ID är en unik identifierare sträng för varje upptäckt ansikte i en bild. Du kan begära ett ansikts-ID i ditt [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-anrop.

## <a name="face-landmarks"></a>Ansiktslandmärken

Face landmärken är en uppsättning lätt att hitta punkter på ett ansikte, såsom eleverna eller spetsen på näsan. Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar alla 27 punkter:

![Ett ansiktsdiagram med alla 27 landmärken märkta](../Images/landmarks.1.jpg)

Koordinaterna för punkterna returneras i enheter av pixlar.

## <a name="attributes"></a>Attribut

Attribut är en uppsättning funktioner som eventuellt kan identifieras av [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Följande attribut kan identifieras:

* **Ålder**. Den beräknade åldern i år av ett visst ansikte.
* **Oskärpa**. Suddigheten i ansiktet i bilden. Det här attributet returnerar ett värde mellan noll och ett och en informell klassificering av låg, medel eller hög.
* **Känslor**. En lista över känslor med deras upptäckt förtroende för det givna ansiktet. Konfidenspoäng normaliseras, och poängen över alla känslor lägger till upp till en. De känslor som returneras är lycka, sorg, neutral, ilska, förakt, avsky, överraskning och rädsla.
* **Exponering**. Exponeringen av ansiktet i bilden. Det här attributet returnerar ett värde mellan noll och ett och en informell klassificering av underExponering, goodExposure eller overExposure.
* **Ansiktshår**. Den uppskattade ansiktshår närvaro och längden för det givna ansiktet.
* **Kön**. Det uppskattade könet på det givna ansiktet. Möjliga värden är män, kvinnor och könlösa.
* **Glasögon**. Om det givna ansiktet har glasögon. Möjliga värden är NoGlasses, ReadingGlasses, Solglasögon och Simglasögon.
* **Hår**. Håret typ av ansiktet. Detta attribut visar om håret är synligt, om skallighet detekteras och vilka hårfärger som detekteras.
* **Huvud pose**. Ansiktets orientering i 3D-rymden. Det här attributet beskrivs av tonhöjds-, rullnings- och girvinklar i grader. Värdeområdena är -90 grader till 90 grader, -180 grader till 180 grader och -90 grader till 90 grader. Se följande diagram för vinkelmappningar:

    ![Ett huvud med tonhöjd, rulle och giryxor märkta](../Images/headpose.1.jpg)
* **Smink.** Oavsett om ansiktet har smink. Det här attributet returnerar ett booleskt värde för eyeMakeup och lipMakeup.
* **Buller**. Det visuella bruset som upptäcks i ansiktsbilden. Det här attributet returnerar ett värde mellan noll och ett och en informell klassificering av låg, medel eller hög.
* **Ocklusion**. Om det finns objekt som blockerar delar av ansiktet. Detta attribut returnerar ett booleskt värde för eyeOccluded, foreheadOccluded och mouthOccluded.
* **Le.** Leendet uttryck för det givna ansiktet. Detta värde är mellan noll för inget leende och en för ett klart leende.

> [!IMPORTANT]
> Ansiktsattribut förutses med hjälp av statistiska algoritmer. De kanske inte alltid är korrekta. Var försiktig när du fattar beslut baserat på attributdata.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att indatabilderna ger de mest exakta identifieringsresultaten:

* De indatabildformat som stöds är JPEG, PNG, GIF för den första bildrutan och BMP.
* Bildfilsstorleken bör inte vara större än 4 MB.
* Detekterbara området för ansiktsstorlek är 36 x 36 till 4096 x 4096 pixlar. Ansikten utanför det här intervallet kommer inte att upptäckas.
* Vissa ansikten kanske inte upptäcks på grund av tekniska utmaningar. Extrema ansiktsvinklar (huvudställning) eller ansiktsocklusion (föremål som solglasögon eller händer som blockerar en del av ansiktet) kan påverka detektionen. Främre och nära frontal ansikten ger bästa resultat.

Om du upptäcker ansikten från ett videoflöde kan du kanske förbättra prestanda genom att justera vissa inställningar på videokameran:

* **Utjämning:** Många videokameror använder en utjämnande effekt. Du bör stänga av detta om du kan eftersom det skapar en oskärpa mellan bildrutor och minskar tydligheten.
* **Slutartid:** En snabbare slutartid minskar mängden rörelse mellan ramar och gör varje bildruta tydligare. Vi rekommenderar slutartider på 1/60 sekund eller snabbare.
* **Slutarvinkel:** Vissa kameror anger slutarvinkel istället för slutartid. Du bör använda en lägre slutarvinkel om möjligt. Detta kommer att resultera i tydligare videobildrutor.

    >[!NOTE]
    > En kamera med en lägre slutarvinkel får mindre ljus i varje bildruta, så bilden blir mörkare. Du måste bestämma rätt nivå för användning.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med ansiktsidentifieringsbegrepp kan du lära dig att skriva ett skript som identifierar ansikten i en viss bild.

* [Identifiera ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)