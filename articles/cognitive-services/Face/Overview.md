---
title: Vad är Azure Face service?
titleSuffix: Azure Cognitive Services
description: Azure Face service tillhandahåller AI-algoritmer som du använder för att identifiera, identifiera och analysera människo ansikten i bilder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/17/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: ansikts igenkänning, ansikts igenkännings program, ansikts analys, ansikts matchning, ansikts igenkännings program, ansikts sökning efter bild, ansikts igenkännings sökning
ms.openlocfilehash: ccc9ee3107d287844430be0ae089e4b7ac3a2f44
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911451"
---
# <a name="what-is-the-azure-face-service"></a>Vad är Azure Face service?

> [!WARNING]
> Den 11 juni 2020 meddelade Microsoft att de inte kommer att sälja teknik för ansiktsigenkänning till polismyndigheter i USA förrän starka föreskrifter som rör användningen av dessa tekniker och som bygger på de mänskliga rättigheterna har införts. Därför kan kunder inte använda ansikts igenkännings funktioner eller funktioner som ingår i Azure-tjänster, till exempel ansikte eller Video Indexer, om en kund är eller tillåter att sådana tjänster används av eller för en polis avdelning i USA.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Face service tillhandahåller AI-algoritmer som identifierar, identifierar och analyserar mänskliga ansikten i bilder. Program vara för ansikts igenkänning är viktig i många olika scenarier, till exempel säkerhet, naturligt användar gränssnitt, analys av avbildnings innehåll, mobilappar och Robotics.

Ansikts tjänsten innehåller flera olika funktioner för ansikts analys som är de som beskrivs i följande avsnitt.

## <a name="face-detection"></a>Ansiktsspårning

Ansikts tjänsten identifierar människo ansikten i en bild och returnerar Rectangle-koordinaterna för deras platser. Om du vill kan ansikts igenkänning extrahera en serie av ansikts attribut, till exempel Head attityd, kön, ålder, känslo, ansikts hår och glasögon.

> [!NOTE]
> Funktionen för ansikts igenkänning är också tillgänglig via [visuellt innehålls tjänsten](../computer-vision/overview.md). Men om du vill utföra ytterligare åtgärder med ansikts data bör du använda den här tjänsten i stället.

![En bild av en kvinna och en man, med rektanglar som ritas runt deras ansikten och ålder och kön visas](./Images/Face.detection.jpg)

Mer information om ansikts igenkänning finns i artikeln [ansikts identifierings](concepts/face-detection.md) begrepp. Se även dokumentationen om att [identifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) -referens.

## <a name="face-verification"></a>Ansiktsverifiering

Verifierings-API: t utför en autentisering mot två identifierade ansikten eller från ett identifierat ansikte till ett person objekt. I praktiken utvärderar det om två ansikten tillhör samma person. Den här funktionen är eventuellt användbar i säkerhets scenarier. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för [att kontrol lera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Hitta liknande API: er möter ansikts matchning mellan målets ansikte och en uppsättning kandidater, och söker efter en mindre uppsättning ansikten som ser ut ungefär som mål ytan. Detta är användbart för att göra en ansikts sökning efter bild. 

Det finns stöd för två arbets lägen, **matchPerson** och **matchFace** . **MatchPerson** -läget returnerar liknande ansikten efter filtrering av samma person med hjälp av [verifierings-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). **MatchFace** -läget ignorerar filtret för samma person. Den returnerar en lista över liknande kandidater som kanske inte tillhör samma person.

I följande exempel visas målets ansikte:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och de här avbildningarna är kandidat ytorna:

![Fem bilder på leende människor. Bilder a och b visar samma person.](./Images/FaceFindSimilar.Candidates.jpg)

För att hitta fyra liknande ansikten returnerar **matchPerson** -läget a och b, som visar samma person som mål ytan. **MatchFace** -läget returnerar a, b, c och d &mdash; exakt fyra kandidater, även om vissa inte är samma person som målet eller har låg likhet. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för att [hitta liknande API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i en grupp tillhör förmodligen samma person. Det kan finnas flera olika grupper för en enskild person. Grupperna åtskiljs av en annan faktor, till exempel uttryck. Mer information finns i rikt linjer för [ansikts igenkänning](concepts/face-recognition.md) eller referens dokumentation för [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Personidentifiering

Identifiera API används för att identifiera ett identifierat ansikte mot en databas med personer (ansikts igenkännings sökning). Den här funktionen kan vara användbar för automatisk bild markering i program för foto hantering. Du skapar databasen i förväg och du kan redigera den över tid.

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

Följ en snabb start för att koda de grundläggande komponenterna i en ansikts igenkännings app på valfritt språk.

- [Snabb start för klient bibliotek](quickstarts/client-libraries.md).