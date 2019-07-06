---
title: Vad är Ansikts-API?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tjänsten för ansiktsigenkänning för att identifiera och analysera ansikten i bilder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 4be33f781dec93fd9fe1b1846322672266cd7350
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606915"
---
# <a name="what-is-the-azure-face-api"></a>Vad är Azure Ansikts-API?

Azure Cognitive Services Ansikts-API ger algoritmer som används för att upptäcka, identifiera och analysera mänskliga ansikten i bilder. Möjligheten att bearbeta mänskliga ansikte information är viktigt i många olika scenarier. Exempelscenarier är säkerhet, naturliga användargränssnitt, innehåll bildanalys och hantering, mobilappar och robotteknik.

Ansikts-API: et tillhandahåller flera olika funktioner. Varje funktion beskrivs i följande avsnitt. Läs vidare för att lära dig mer om dem.

## <a name="face-detection"></a>Ansiktsspårning

Ansikts-API identifierar ansikten i en bild och returnerar rektangel koordinaterna för deras platser. Du kan också kan identifiera ansikten extrahera en serie med ansikts-relaterade attribut. Exempel är head attityd, kön, ålder, känslor, ansiktsigenkänning hår och glasögon.

> [!NOTE]
> Funktionen för identifiering av ansikte är också tillgänglig via den [API för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Om du vill ytterligare åtgärder med ansikts-data, använda Ansikts-API som är den tjänst som beskrivs i den här artikeln.

![En avbildning av en kvinna och en man, med rektanglar ritas runt sina ansikten och ålder och kön visas](./Images/Face.detection.jpg)

Mer information om ansiktsigenkänning finns i den [Ansiktsspårning](concepts/face-detection.md) begrepp artikeln. Se även de [identifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referensdokumentation.

## <a name="face-verification"></a>Ansiktsverifiering

API:et för verifiering utför en autentisering mot två identifierade ansikten eller från ett identifierat ansikte till ett personobjektet. I praktiken utvärderar det om två ansikten tillhör samma person. Den här funktionen är användbara i säkerhetsscenarier. Mer information finns i den [står inför erkännande](concepts/face-recognition.md) begrepp guiden eller [verifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) referensdokumentation.

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Hitta liknande API: et jämför en mål-yta med en uppsättning kandidat ansikten för att hitta en mindre uppsättning ansikten som liknar de mål står inför. Två fungerande lägen, matchPerson och matchFace, stöds. Läge för matchPerson returnerar liknande ansikten när filtreras för samma person med hjälp av den [verifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Läge för matchFace ignorerar filtret samma person. Den returnerar en lista med liknande ansikten i kandidat som kanske eller kanske inte tillhör samma person.

I följande exempel visas de mål står inför:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och det här är kandidatansiktena:

![Fem bilder på leende människor. Avbildningar av en och b visar samma person.](./Images/FaceFindSimilar.Candidates.jpg)

För att hitta fyra liknande ansikten, matchPerson läge returnerar en och b, som visar samma person som mål ansiktet. MatchFace läge returnerar a, b, c och d, exakt fyra kandidater, även om vissa inte är samma person som mål eller har låg likhet. Mer information finns i den [står inför erkännande](concepts/face-recognition.md) begrepp guiden eller [hitta liknande API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) referensdokumentation.

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i en grupp är sannolikt tillhör samma person. Det kan finnas flera olika grupper för en enskild person. Grupperna som åtskiljs av en annan faktor, till exempel uttryck, till exempel. Mer information finns i den [står inför erkännande](concepts/face-recognition.md) begrepp guiden eller [grupp API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) referensdokumentation.

## <a name="person-identification"></a>Personidentifiering

Identifiera API: et används för att identifiera ett identifierade ansikte mot en databas med personer. Den här funktionen kan vara användbar för bilder automatiskt taggar i hanteringsprogramvara för foto. Du skapar databasen i förväg och du kan redigera den över tid.

Följande bild visar ett exempel på en databas med namnet `"myfriends"`. Varje grupp kan innehålla upp till 1 miljon olika personobjekt. Varje personobjekt kan ha upp till 248 registrerade ansikten.

![Ett rutnät med tre kolumner för olika personer, var och en med tre raderna med ansikts-avbildningar](./Images/person.group.clare.jpg)

När du har skapat och träna en databas kan utföra du identifiering mot gruppen med identifierade utseende. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om identifiering finns i den [står inför erkännande](concepts/face-recognition.md) begrepp guiden eller [identifiera API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) referensdokumentation.

## <a name="use-containers"></a>Använda containrar

[Ansikts-behållare](face-how-to-install-containers.md) för att upptäcka, identifiera och identifiera bilder genom att installera en standardiserad dockerbehållare närmare till dina data.

## <a name="sample-apps"></a>Exempelappar

Följande exempelprogrammen visar några olika sätt att använda Ansikts-API:

- [Microsoft Ansikts-API: Windows Client-bibliotek och exempelapp](https://github.com/Microsoft/Cognitive-Face-Windows) är en WPF-app som visar flera olika användningsområden för ansikte detektering, analys och identifiering.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) är en app för Universal Windows Platform (UWP) som använder står inför identifiering tillsammans med tal, Cortana, ink och kamera i ett scenario med familjen dela anteckningar.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Utvecklare som använder Face-tjänsten måste vara medvetna om Microsofts policy på kunddata som med alla Cognitive Services-resurser. Mer information finns i den [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en Snabbstart för att implementera ett scenario med ansiktsigenkänning – i koden:

- [Snabbstart: Identifiera ansikten i en bild med hjälp av .NET-SDK med C# ](quickstarts/csharp.md). Det finns andra språk.
