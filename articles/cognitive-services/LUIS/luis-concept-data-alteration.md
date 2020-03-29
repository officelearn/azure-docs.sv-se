---
title: Dataändring - LUIS
description: Lär dig hur data kan ändras före förutsägelser i Språkförståelse (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292066"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändra uttrycksdata före eller under förutsägelse
LUIS tillhandahåller sätt att manipulera uttryck före eller under förutsägelsen. Dessa inkluderar [fastställande stavning](luis-tutorial-bing-spellcheck.md), och fastställande tidszon frågor för fördefinierade [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Korrigera stavfel i uttryck


### <a name="v3-runtime"></a>V3-körning

Förbehandla text för stavningskorrigeringar innan du skickar uttrycket till LUIS. Använd exempelyttranden med rätt stavning för att se till att du får rätt förutsägelser.

Använd Stavningskontroll i [Bing](../bing-spell-check/overview.md) för att korrigera text innan du skickar den till LUIS.

### <a name="prior-to-v3-runtime"></a>Före V3-körning

LUIS använder [Bing Spell Check API V7](../Bing-Spell-Check/overview.md) för att korrigera stavfel i uttryck. LUIS behöver nyckeln som är associerad med den tjänsten. Skapa nyckeln och lägg sedan till nyckeln som frågeparameter vid [slutpunkten](https://go.microsoft.com/fwlink/?linkid=2092356).

Slutpunkten kräver två params för stavningskorrigeringar för att fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing Stavningskontroll API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) slutpunkt nyckel|

När [Bing Stavningskontroll API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) upptäcker ett fel returneras det ursprungliga uttrycket och den korrigerade uttrycket tillsammans med förutsägelser från slutpunkten.

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lista över tillåtna ord
API:et för stavningskontroll av Bing som används i LUIS stöder inte en lista med ord som ska ignoreras vid ändringar av stavningskontroll. Om du behöver tillåta en lista med ord eller förkortningar bearbetar du uttrycket i klientprogrammet innan yttrandet skickas till LUIS för avsiktsförutsägelse.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszon för fördefinierad datetimeV2-entitet
När en LUIS-app använder den fördefinierade [datetimeV2-entiteten](luis-reference-prebuilt-datetimev2.md) kan ett datetime-värde returneras i förutsägelsesvaret. Tidszonen för begäran används för att bestämma rätt datumtid att returnera. Om begäran kommer från en bot eller ett annat centraliserat program innan du kommer till LUIS korrigerar du tidszonen SOM LUIS använder.

### <a name="endpoint-querystring-parameter"></a>Parameter för slutpunktsfrågesträngning
Tidszonen korrigeras genom att användarens tidszon läggs till i `timezoneOffset` [slutpunkten](https://go.microsoft.com/fwlink/?linkid=2092356) med hjälp av param. Värdet av `timezoneOffset` bör vara det positiva eller negativa talet, i minuter, för att ändra tiden.

|Param|Värde|
|--|--|
|`timezoneOffset`|positivt eller negativt tal, i minuter|

### <a name="daylight-savings-example"></a>Exempel på sommartid
Om du behöver den returnerade fördefinierade datetimeV2 för att justera `timezoneOffset` för sommartid, bör du använda frågesträngparametern med ett +/- värde i minuter för [slutpunktsfrågan.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[V2-begäran om förutsägelseslutpunkt](#tab/V2)

Lägg till 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Ta bort 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[V3-begäran om för förutsägelseslutpunkt](#tab/V3)

Lägg till 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Ta bort 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-koden bestämmer rätt värde för tidszonOffset
Följande C#-kod använder [klassen TimeZoneInfo-klassens](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) [FindSystemTimeZoneById-metod](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) för att fastställa rätt `timezoneOffset` baserat på systemtid:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Korrigera stavfel med den här självstudien](luis-tutorial-bing-spellcheck.md)
