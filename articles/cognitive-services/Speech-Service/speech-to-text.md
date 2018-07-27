---
title: Om tal till Text
description: En översikt över funktionerna i tal till Text-API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 464eb9bde689aa947a5b1e2900ae0fc4670fed6e
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285484"
---
# <a name="about-the-speech-to-text-api"></a>Om tal till Text API

Den **tal till Text** API *transkriberar* ljudströmmar till text som ditt program kan visa för användaren eller vidta åtgärder som kommandot indata. API: erna kan användas med en SDK-klientbibliotek (för plattformar som stöds och språk) eller ett REST-API.

Den **tal till Text** API: et erbjuder följande funktioner:

- Avancerad taligenkänningsteknik från Microsoft, samma som Cortana, Office och andra Microsoft-produkter.

- I realtid kontinuerlig erkännande. **Tal till Text** tillåter användare att transkribera ljud till text i realtid. Det stöder även ta emot mellanresultat av ord som har blivit erkänt hittills. Tjänsten kan automatiskt identifiera slutet av tal. Användare kan också välja ytterligare alternativ, inklusive versaler och skiljetecken, svordomar maskera och inverterade text normalisering.

- Optimerad **tal till Text** resultat för interaktiva, konversationen och diktering scenarier. 

- Stöd för många språk och dialekter. En fullständig lista över språk som stöds i varje läge för igenkänning av Se [språk som stöds](supported-languages.md#speech-to-text).

- Anpassad språk och akustiska modeller, så att du kan anpassa ditt program till dina användares specialiserade domän ordförråd samtalsstil miljö och sätt att tala.

- Naturlig språkförståelse. Integreringen med [Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), du kan härleda avsikter och entiteter från tal. Användare behöver inte ha någon ordförråd för din app, men kan beskriva vad de vill med egna ord.

## <a name="api-capabilities"></a>API-funktioner

Vissa funktioner i den **tal till Text** API: et är inte tillgängliga via REST. I följande tabell sammanfattas funktionerna för varje metod för att komma åt API: et.

| Användningsfall | REST | SDK:er |
|-----|-----|-----|----|
| Transkribera en kort uttryck, till exempel ett kommando (längd < 15 s); Inga mellanliggande resultat | Ja | Ja |
| Transkribera en längre uttryck (> 15 s) | Nej | Ja |
| Transkribera strömmande ljud med valfritt mellanliggande resultat | Nej | Ja |
| Förstå talare avsikter via LUIS | Nej\* | Ja |

\* *LUIS avsikter och entiteter kan erhållas med hjälp av en separat LUIS-prenumeration. Med den här prenumerationen SDK anropa LUIS för dig och ge entiteten och vad resultat samt tal avskrifter. Med REST-API kan du anropa LUIS själv för att härleda avsikter och entiteter med LUIS-prenumeration.*

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
