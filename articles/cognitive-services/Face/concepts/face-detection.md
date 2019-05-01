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
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572546"
---
# <a name="face-detection-and-attributes"></a>Identifiera ansikten och attribut

Den här artikeln förklarar begreppet ansiktsigenkänning och ansikte attributdata. Ansiktsigenkänning är åtgärden för att hitta ansikten i en bild och du kan också returnera en mängd olika ansikts-relaterade data.

Du använder den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) åtgärd för att identifiera ansikten i en bild. Varje identifierad ansikte motsvarar minst en **faceRectangle** i svaret. Det här är en uppsättning pixel koordinaterna (vänster, top, bredd, höjd) Markera hitta ansiktet. Med dessa koordinater kan visa du platsen för ansiktet, samt dess storlek. I API-svaret visas ansikten i storlek ordning från största till minsta.

## <a name="face-id"></a>Ansikts-ID

Ansikts-ID är helt enkelt en unik identifierarsträng för varje identifierad ansikte i en bild. Du kan begära en face ID i din [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-anrop.

## <a name="face-landmarks"></a>Ansiktslandmärken

Ansiktslandmärken är en uppsättning enkelt att hitta punkter på ett ansikte, till exempel eleverna eller visarens näsa. Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar alla 27 punkter:

![Ett ansikte diagram med alla 27 ansiktslandmärken märkt](../Images/landmarks.1.jpg)

Koordinaterna för punkterna som returneras i antal bildpunkter.

## <a name="attributes"></a>Attribut

Attribut är en uppsättning ytterligare ansikts-funktioner som eventuellt kan identifieras av den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Här följer några attribut som kan identifieras:

* **Ålder** beräknad ålder, i flera år av ett visst ansikte.
* **Oskärpa** blurriness av ansikte i bilden. Det här returnerar ett värde mellan noll och en samt en informell klassificering (”låg”, ”medel”, ”hög”).
* **Exponering** exponering av ansikte i bilden. Det här returnerar ett värde mellan noll och en samt en informell klassificering (”Underexponering”, ”goodExposure”, ”överexponering”).
* **Igenkänning** en lista över känslor sina tryggt identifiering för det angivna ansiktet. Förtroende poäng normaliseras: poängen över alla känslor läggs till. Känslor som returneras är lycka, sorg, neutral, ilska, förakt, avsky, överraskning och behöva betala.
* **Ansiktsigenkänning hår** uppskattade ansiktsigenkänning hår förekomst och längden på visst ansikte.
* **Kön** visst ansikte uppskattade kön. Möjliga värden är ”manlig”, ”kvinnliga” och ”genderless”.
* **Glasögon** om visst ansikte har glasögon. Möjliga värden är ”NoGlasses”, ”ReadingGlasses”, ”Solglasögonföretag” och ”Swimming skyddsglasögon”.
* **Hår** styl hår ansiktet. Detta visar om hår är synliga, om baldness identifieras, och vilka färger som hår identifieras.
* **HEAD utgöra** de står inför orientering i 3D-utrymmet. Detta beskrivs genom försäljningsargument, återställning och positionen vinklar i grader. Värdeintervall är [-90, 90], [-180, 180] och [-90, 90] grader respektive. Se i följande diagram för vinkel mappningar:

    ![En head med motivation, distribuera och yaw axlar med etiketten](../Images/headpose.1.jpg)
* **Makeup** om ansiktet har makeup. Det här returnerar ett booleskt värde för ”eyeMakeup” och ”lipMakeup”.
* **Bruset** visual bruset har identifierats i ansiktsbilden. Det här returnerar ett värde mellan noll och en samt en informell klassificering (”låg”, ”medel”, ”hög”).
* **Ocklusion** om det finns objekt som blockerar delar av ansiktet. Det här returnerar ett booleskt värde för ”eyeOccluded”, ”foreheadOccluded” och ”mouthOccluded”.
* **Le** Leende-uttrycket för visst ansikte. Det här är ett värde mellan noll (inga Leende) och en (rensa Leende).

> [!IMPORTANT]
> Ansiktsattribut förutse med statistisk algoritmerna och kanske inte alltid är korrekt. Var försiktig när du fattar beslut utifrån attributdata.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att din indatabilder ger de bästa resultaten av programuppdateringsidentifieringen:

* Format som stöds inmatad bild är JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek bör inte vara större än 4 MB.
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kan inte identifieras.
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar. Extreme ansikte vinklar (head attityd) eller ansikte ocklusion (objekt, till exempel solglasögonföretag eller händer blockerar en del av de står inför) kan påverka identifiering. Främre och nästan direkt ansikten ger bäst resultat.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med principerna för identifiering av ansikte, lär du dig hur du skriver ett enkelt skript som identifierar ansikten i en viss avbildning.

* [Hur du identifierar ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)