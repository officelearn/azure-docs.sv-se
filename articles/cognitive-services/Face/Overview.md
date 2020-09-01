---
title: Vad är tjänsten Ansiktsigenkänning?
titleSuffix: Azure Cognitive Services
description: Tjänsten Azure Cognitive Services Face tillhandahåller algoritmer som används för att identifiera, identifiera och analysera mänskliga ansikten i bilder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/01/2020
ms.author: pafarley
ms.openlocfilehash: e1b5ca8cfb9a6bc0fec7d85f609c411f38d1bbbc
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280083"
---
# <a name="what-is-the-azure-face-service"></a>Vad är Azure Face service?
<!--
> [!IMPORTANT]
> On June 11, 2020, Microsoft announced that it will not sell facial recognition technology to police departments in the United States until strong regulation, grounded in human rights, has been enacted. As such, customers may not use facial recognition features or features included in Azure Services, such as Face or Video Indexer, if a customer is, or is allowing use of such services by or for, a police department in the United States.
-->
![Juridisk information ](./media/overview/banner.png)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tjänsten Azure Cognitive Services Face tillhandahåller algoritmer som identifierar, identifierar och analyserar mänskliga ansikten i bilder. Möjligheten att bearbeta information om människo ansikte är viktig i många olika program scenarier. Exempel scenarier är säkerhet, naturligt användar gränssnitt, bild innehålls analys och hantering, mobilappar och Robotics.

Ansikts tjänsten tillhandahåller flera olika funktioner som var och en beskrivs i följande avsnitt.

## <a name="face-detection"></a>Ansiktsspårning

Ansikts tjänsten identifierar människo ansikten i en bild och returnerar Rectangle-koordinaterna för deras platser. Om du vill kan ansikts igenkänning extrahera en serie av ansikts attribut. Exempel är Head-attityd, kön, ålder, känslo, ansikts hår och glasögon.

> [!NOTE]
> Funktionen för ansikts igenkänning är också tillgänglig via [API för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Om du vill utföra ytterligare åtgärder med ansikts data använder du ansikts tjänsten, som är den tjänst som beskrivs i den här artikeln.

![En bild av en kvinna och en man, med rektanglar som ritas runt deras ansikten och ålder och kön visas](./Images/Face.detection.jpg)

Mer information om ansikts igenkänning finns i artikeln [ansikts identifierings](concepts/face-detection.md) begrepp. Se även dokumentationen om att [identifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) -referens.

## <a name="face-verification"></a>Ansiktsverifiering

Verifierings-API: t utför en autentisering mot två identifierade ansikten eller från ett identifierat ansikte till ett person objekt. I praktiken utvärderar det om två ansikten tillhör samma person. Den här funktionen är eventuellt användbar i säkerhets scenarier. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för [att kontrol lera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Hitta liknande ansikten

I Sök liknande API jämförs ett mål ansikte med en uppsättning av kandidat ytor för att hitta en mindre uppsättning ansikten som ser ut ungefär som mål ytan. Det finns stöd för två arbets lägen, matchPerson och matchFace. MatchPerson-läget returnerar liknande ansikten efter det att den har filtrerats för samma person med hjälp av [verifierings-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). MatchFace-läget ignorerar filtret för samma person. En lista visas med liknande ansikten som kanske inte tillhör samma person.

I följande exempel visas målets ansikte:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och de här avbildningarna är kandidat ytorna:

![Fem bilder på leende människor. Bilder a och b visar samma person.](./Images/FaceFindSimilar.Candidates.jpg)

För att hitta fyra liknande ansikten returnerar matchPerson-läget a och b, som visar samma person som mål ytan. MatchFace-läget returnerar a, b, c och d &mdash; exakt fyra kandidater, även om vissa inte är samma person som målet eller har låg likhet. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för att [hitta liknande API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i en grupp tillhör förmodligen samma person. Det kan finnas flera olika grupper för en enskild person. Grupperna åtskiljs av en annan faktor, till exempel uttryck. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Personidentifiering

Identifiera API används för att identifiera ett identifierat ansikte mot en databas med personer. Den här funktionen kan vara användbar för automatisk bild markering i program för foto hantering. Du skapar databasen i förväg och du kan redigera den över tid.

Följande bild visar ett exempel på en databas med namnet `"myfriends"` . Varje grupp kan innehålla upp till 1 000 000 olika person objekt. Varje personobjekt kan ha upp till 248 registrerade ansikten.

![Ett rutnät med tre kolumner för olika personer, var och en med tre rader av ansikts bilder](./Images/person.group.clare.jpg)

När du har skapat och tränat en databas kan du göra en identifiering mot gruppen med en ny identifierad ansikte. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om person identifiering finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för att [identifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="containers"></a>Containrar

[Använd behållaren ansikts](face-how-to-install-containers.md) för att identifiera, identifiera och identifiera ansikten genom att installera en standardiserad Docker-behållare närmare dina data.

## <a name="sample-apps"></a>Exempelappar

Följande exempel program visar några sätt att använda ansikts tjänsten:

- [Ansikts-API: Windows-klient bibliotek och exempel](https://github.com/Microsoft/Cognitive-Face-Windows) är en WPF-app som visar flera scenarier för ansikts igenkänning, analys och identifiering.
- [FAMILYNOTES UWP-appen](https://github.com/Microsoft/Windows-appsample-familynotes) är en universell Windows-plattform-app (UWP) som använder ansikts identifiering tillsammans med tal, Cortana, bläck och kamera i ett antecknings delnings scenario.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla Cognitive Services-resurser måste utvecklare som använder ansikts tjänsten vara medvetna om Microsofts principer för kund information. Mer information finns på [sidan Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en snabb start för att implementera ett ansikts identifierings scenario i kod:

- [Snabb start: identifiera ansikten i en avbildning med hjälp av .NET SDK med C#](quickstarts/csharp.md). Andra språk är tillgängliga.
