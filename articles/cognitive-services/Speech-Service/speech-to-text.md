---
title: Om tal till Text
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna i tal till Text-API.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: 041ec1c095ec604fed7906368ff266c1586df570
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471347"
---
# <a name="about-the-speech-to-text-api"></a>Om tal till Text API

Den **tal till Text** API *transkriberar* ljudströmmar till text som ditt program kan visa för användaren eller vidta åtgärder som kommandot indata. API: erna kan användas med en SDK-klientbibliotek (för plattformar som stöds och språk) eller ett REST-API.

Den **tal till Text** API: et erbjuder följande funktioner:

- Avancerad taligenkänningsteknik från Microsoft, samma som Cortana, Office och andra Microsoft-produkter.

- I realtid kontinuerlig erkännande. **Tal till Text** tillåter användare att transkribera ljud till text i realtid. Det stöder även ta emot mellanresultat av ord som har blivit erkänt hittills. Tjänsten kan automatiskt identifiera slutet av tal. Användare kan också välja ytterligare alternativ, inklusive versaler och skiljetecken, svordomar maskera och inverterade text normalisering.

- Resultaten returneras i både Lexical och visa formulär (lexikal resultat finns DetailedSpeechRecognitionResult i exempel eller API: et).

- Stöd för många språk och dialekter. En fullständig lista över språk som stöds i varje läge för igenkänning av Se [språk som stöds](language-support.md#speech-to-text).

- Anpassad språk och akustiska modeller, så att du kan anpassa ditt program till dina användares specialiserade domän ordförråd samtalsstil miljö och sätt att tala.

- Naturlig språkförståelse. Integreringen med [Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), du kan härleda avsikter och entiteter från tal. Användare behöver inte ha någon ordförråd för din app, men kan beskriva vad de vill med egna ord.

- Förtroendepoäng returneras från tjänsten om du anger ett detaljerat resultat på konfigurationsobjektet tal (SpeechConfig.OutputFormat egenskap). Sedan kan du använda antingen Best()-metoden på resultatet eller få poäng direkt från JSON som returneras från tjänsten (exempelvis resultat. Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>API-funktioner

Några av funktionerna i den **tal till Text** API, särskilt när det gäller anpassning, är tillgängliga via REST. I följande tabell sammanfattas funktionerna för varje metod för att komma åt API: et. En fullständig lista över funktioner och API-information, se [Swagger referens](https://westus.cris.ai/swagger/ui/index).

| Användningsfall | REST | SDK:er |
|-----|-----|-----|----|
| Transkribera en kort uttryck, till exempel ett kommando (längd < 15 s); Inga mellanliggande resultat | Ja | Ja |
| Transkribera en längre uttryck (> 15 s) | Nej | Ja |
| Transkribera strömmande ljud med valfritt mellanliggande resultat | Nej | Ja |
| Förstå talare avsikter via LUIS | Nej\* | Ja |
| Skapa Precisionstester | Ja | Nej |
| Ladda upp datauppsättningar för modellanpassning | Ja | Nej |
| Skapa och hantera talmodeller | Ja | Nej |
| Skapa och hantera modelldistributioner | Ja | Nej |
| Hantera prenumerationer | Ja | Nej |
| Skapa och hantera modelldistributioner | Ja | Nej |
| Skapa och hantera modelldistributioner | Ja | Nej |

> [!NOTE]
> REST API: et implementerar de nätverksbegränsningar API-begäranden till 25 5 sekunden. Meddelandehuvudena informerar begränsningar

\* *LUIS avsikter och entiteter kan erhållas med hjälp av en separat LUIS-prenumeration. Med den här prenumerationen SDK anropa LUIS för dig och ge entiteten och vad resultat samt tal avskrifter. Med REST-API kan du anropa LUIS själv för att härleda avsikter och entiteter med LUIS-prenumeration.*

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Snabbstart: känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
* [Se hur du identifierar intentioner från tal i C#](how-to-recognize-intents-from-speech-csharp.md)
