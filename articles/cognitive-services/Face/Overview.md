---
title: Vad är tjänsten Ansiktsigenkänning?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Face-tjänsten tillhandahåller algoritmer som används för att identifiera, känna igen och analysera mänskliga ansikten i avbildningar.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: c433d99fd96e99d418fd5500c1075e68dfb1742c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053580"
---
# <a name="what-is-the-azure-face-service"></a>Vad är Azure Face-tjänsten?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Cognitive Services Face-tjänsten tillhandahåller algoritmer som används för att identifiera, känna igen och analysera mänskliga ansikten i avbildningar. Möjligheten att bearbeta mänsklig ansiktsinformation är viktig i många olika programvaruscenarier. Exempel på scenarier är säkerhet, naturligt användargränssnitt, bildinnehållsanalys och hantering, mobilappar och robotteknik.

Face-tjänsten erbjuder flera olika funktioner. Varje funktion beskrivs i följande avsnitt. Läs vidare för att lära dig mer om dem.

## <a name="face-detection"></a>Ansiktsspårning

Face-tjänsten identifierar mänskliga ansikten i en bild och returnerar rektangelkoordinaterna för deras platser. Alternativt kan ansiktsigenkänning extrahera en serie ansiktsrelaterade attribut. Exempel är huvud pose, kön, ålder, känslor, ansiktshår, och glasögon.

> [!NOTE]
> Ansiktsigenkänningsfunktionen är också tillgänglig via [API:et för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Om du vill utföra ytterligare åtgärder med ansiktsdata använder du Face-tjänsten, som är den tjänst som beskrivs i den här artikeln.

![En bild av en kvinna och en man, med rektanglar ritade runt deras ansikten och ålder och kön visas](./Images/Face.detection.jpg)

Mer information om ansiktsigenkänning finns i artikeln [Ansiktsidentifieringskoncept.](concepts/face-detection.md) Se även referensdokumentationen [identifiera API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Ansiktsverifiering

Api:et Verifiera gör en autentisering mot två identifierade ansikten eller från ett upptäckt ansikte till ett personobjekt. I praktiken utvärderar det om två ansikten tillhör samma person. Den här funktionen är potentiellt användbar i säkerhetsscenarier. Mer information finns i guiden [För ansiktsigenkänningskoncept](concepts/face-recognition.md) eller referensdokumentationen [Verifiera API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Hitta liknande API jämför ett mål ansikte med en uppsättning kandidat ansikten för att hitta en mindre uppsättning ansikten som liknar målytan. Två arbetslägen, matchPerson och matchFace, stöds. MatchPerson-läget returnerar liknande ansikten efter att det filtrerats för samma person med hjälp av [Verifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). MatchFace-läget ignorerar filtret med samma personer. Den returnerar en lista över liknande kandidat ansikten som kanske eller kanske inte tillhör samma person.

I följande exempel visas målytan:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och dessa bilder är kandidaten ansikten:

![Fem bilder på leende människor. Bilder a och b visar samma person.](./Images/FaceFindSimilar.Candidates.jpg)

Om du vill hitta fyra liknande ansikten returnerar matchPerson-läget a och b, som visar samma person som målytan. MatchFace-läget returnerar en, b,&mdash;c och d exakt fyra kandidater, även om vissa inte är samma person som målet eller har låg likhet. Mer information finns i konceptguiden [för ansiktsigenkänning](concepts/face-recognition.md) eller referensdokumentationen [Hitta liknande API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i en grupp kommer sannolikt att tillhöra samma person. Det kan finnas flera olika grupper för en enda person. Grupperna differentieras till exempel med en annan faktor, till exempel uttryck. Mer information finns i guiden [För ansiktsigenkänningskoncept](concepts/face-recognition.md) eller referensdokumentationen [för grupp-API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Personidentifiering

Identifiera API används för att identifiera ett upptäckt ansikte mot en databas med personer. Den här funktionen kan vara användbar för automatisk bildtaggning i fotohanteringsprogram. Du skapar databasen i förväg och du kan redigera den med tiden.

Följande bild visar ett exempel `"myfriends"`på en databas med namnet . Varje grupp kan innehålla upp till 1 miljon olika personobjekt. Varje personobjekt kan ha upp till 248 registrerade ansikten.

![Ett rutnät med tre kolumner för olika personer, var och en med tre rader med ansiktsbilder](./Images/person.group.clare.jpg)

När du har skapat och tränat en databas kan du identifiera dig mot gruppen med ett nytt upptäckt ansikte. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om personidentifiering finns i guiden [För ansiktsigenkänningskoncept](concepts/face-recognition.md) eller referensdokumentationen [identifiera API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="use-containers"></a>Använda containrar

[Använd Ansiktsbehållaren](face-how-to-install-containers.md) för att identifiera, känna igen och identifiera ansikten genom att installera en standardiserad Docker-behållare närmare dina data.

## <a name="sample-apps"></a>Exempelappar

Följande exempelprogram visar några sätt att använda Ansiktstjänsten:

- [Ansikts-API: Windows Client Library och exempel](https://github.com/Microsoft/Cognitive-Face-Windows) är en WPF-app som demonstrerar flera scenarier med ansiktsidentifiering, analys och identifiering.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) är en UWP-app (Universal Windows Platform) som använder ansiktsidentifiering tillsammans med tal, Cortana, bläck och kamera i ett familjebrevdelningsscenario.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla Cognitive Services-resurser måste utvecklare som använder Face-tjänsten vara medvetna om Microsofts policyer för kunddata. Mer information finns på [sidan Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) i Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en snabbstart för att implementera ett ansiktsidentifieringsscenario i kod:

- [Snabbstart: Identifiera ansikten i en bild med hjälp av .NET SDK med C#](quickstarts/csharp.md). Andra språk finns tillgängliga.
