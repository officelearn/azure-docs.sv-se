---
title: Vad är Ansikts-API?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tjänsten för ansiktsigenkänning för att identifiera och analysera ansikten i bilder.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: 1916aa94dcf7528ce898ef37eac29c1017f03f15
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231894"
---
# <a name="what-is-the-azure-face-api"></a>Vad är Azure Ansikts-API?

Azure Ansikts-API är en kognitiv tjänst som tillhandahåller algoritmer för identifiering, igenkänning och analyser av ansikten i bilder. Förmågan att bearbeta ansiktsinformation är viktig i många olika programscenarier, till exempel säkerhet, naturliga användargränssnitt, analys och hantering av bildinnehåll, mobilappar och robotteknik.

Ansikts-API:et innehåller flera funktioner, som beskrivs i följande avsnitt. Läs vidare för att få mer information om varje funktion och avgöra om den passar dina behov.

## <a name="face-detection"></a>Ansiktsspårning

Ansikts-API:et kan identifiera ansikten i en bild och returnera rektangelkoordinaterna för ansiktenas placering. Alternativt kan ansiktsavkänning extrahera en serie ansiktsrelaterade attribut som hållning, kön, ålder, huvudhållning, ansiktsbehåring och glasögon.

![En bild av en kvinna och en man, med rektanglar ritade kring deras ansikten och visad ålder och kön](./Images/Face.detection.jpg)

Ansiktsavkänningsfunktionen är också tillgänglig genom [API:et för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) men om du vill utföra ytterligare åtgärder med ansiktsinformation ska du använda Ansikts-API (den här tjänsten). Mer information om ansiktsavkänning finns i [API:et för identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Ansiktsverifiering

API:et för verifiering utför en autentisering mot två identifierade ansikten eller från ett identifierat ansikte till ett personobjektet. I praktiken utvärderar det om två ansikten tillhör samma person. Det här är potentiellt användbart i säkerhetsscenarier. Mer information finns i [API:et för verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Hitta liknande-API:et tar ett målansikte och en uppsättning kandidatansikten och hittar en mindre mängd ansikten som mest liknar målansiktet. Två arbetslägen, **matchPerson** och **matchFace**, stöds. Läget **matchPerson** returnerar likande ansikten efter filtrering för samma person (med hjälp av [API:et för verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). Läget **matchFace** ignorerar samma person-filtret och returnerar en lista med likande kandidatansikten som kanske tillhör samma person.

I följande exempel är det här målansiktet:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och det här är kandidatansiktena:

![Fem bilder på leende människor. Bild a) och b) är samma person](./Images/FaceFindSimilar.Candidates.jpg)

För att hitta fyra liknande ansikten skulle läget **matchPerson** returnera (a) och (b), vilket visar samma person som målansiktet. Läget **matchFace** returnerar (a), (b), (c) och (d) – exakt fyra kandidater, även om vissa inte är samma person som målet eller har låg likhet. Mer information finns i [Hitta liknande-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i den grupp tillhör sannolikt samma person men det kan finnas flera olika grupper för en enskild person (särskild med en annan faktor, till exempel uttryck). Mer information finns i [API:et för gruppering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Personidentifiering

API:et för identifiering kan användas för att identifiera ett ansikte mot en databas med människor. Det kan vara användbart för automatisk bildtaggning i fotohanteringsprogram. Du kan skapa databasen i förväg, och redigera den sedan över tid.

Följande bild visar ett exempel på en databas med namnet ”myfriends”. Varje grupp kan innehålla upp till 1 000 000 olika personobjekt, och varje personobjekt kan ha upp till 248 registrerade ansikten.

![Ett rutnät med 3 kolumner för olika personer, var och med 3 rader med ansiktsbilder](./Images/person.group.clare.jpg)

När en databas har skapats och tränats kan du utföra identifiering mot gruppen med ett nytt identifierat ansikte. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om personidentifiering finns i [API:et för identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Använda containrar

[Använd containern för ansiktsigenkänning](face-how-to-install-containers.md) för att känna av, känna igen och identifiera ansikten, genom att installera en standardiserad Docker-container närmare dina data.

## <a name="sample-apps"></a>Exempelappar

Följande exempelprogram visar några sätt som ansikts-API:et kan användas.

- [Microsoft Ansikts-API: Windows Client Library & Sample](https://github.com/Microsoft/Cognitive-Face-Windows) – en WPF-app som visar flera olika scenarier för avkänning, analys och identifiering av ansikten.
- [FamilyNotes UWP-appen](https://github.com/Microsoft/Windows-appsample-familynotes) – en app för universell Windows-plattform (UWP) som använder ansiktsidentifiering tillsammans med tal, Cortana, pennanteckningar och kamera i ett scenario med delade familjeanteckningar.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder ansiktsigenkänningstjänsten känna till Microsofts policyer gällande kunddata. Se [Cognitive Services-sidan](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center om du vill veta mer.

## <a name="next-steps"></a>Nästa steg

Följ en snabbstart för att implementera ett enkelt ansiktsavkänningsscenario i kod.
- [Snabbstart: Identifiera ansikten i en bild med hjälp av .NET SDK med C#](quickstarts/csharp.md) (andra språk är tillgängliga)
