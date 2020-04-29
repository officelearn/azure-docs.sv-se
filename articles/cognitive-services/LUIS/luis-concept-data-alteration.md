---
title: Data ändring – LUIS
description: Lär dig hur data kan ändras innan förutsägelser i Language Understanding (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292066"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändra uttryck-data före eller under förutsägelse
LUIS tillhandahåller sätt att ändra uttryck före eller under förutsägelsen. Detta inkluderar att [åtgärda stavnings](luis-tutorial-bing-spellcheck.md)-och åtgärda tids zons problem för färdiga [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Korrigera stavfel i uttryck


### <a name="v3-runtime"></a>V3-körning

Förbearbeta text för stavnings korrigeringar innan du skickar uttryck till LUIS. Använd exempel yttranden med rätt stavning för att se till att du får rätt förutsägelser.

Använd [stavningskontroll i Bing](../bing-spell-check/overview.md) för att korrigera text innan du skickar den till Luis.

### <a name="prior-to-v3-runtime"></a>Före v3-körning

LUIS använder [API för stavningskontroll i Bing v7](../Bing-Spell-Check/overview.md) för att korrigera stavfel i uttryck. LUIS behöver nyckeln som är kopplad till tjänsten. Skapa nyckeln och Lägg sedan till nyckeln som en QueryString-parameter i [slut punkten](https://go.microsoft.com/fwlink/?linkid=2092356).

Slut punkten kräver två parametrar för att stavnings korrigeringar ska fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[API för stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) slut punkts nyckel för v7|

När [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) identifierar ett fel returneras den ursprungliga uttryck och de korrigerade uttryck returneras tillsammans med förutsägelser från slut punkten.

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

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
Stavnings kontrolls-API: t för Bing som används i LUIS stöder inte en lista över ord som ska ignoreras under stavnings kontrollen. Om du behöver tillåta en lista över ord eller akronymer, bearbetar du uttryck i klient programmet innan du skickar uttryck till LUIS för avsikts förutsägelse.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszon för en fördefinierad datetimeV2-entitet
När en LUIS-app använder den fördefinierade [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteten kan ett datetime-värde returneras i förutsägelse svaret. Tids zonen för begäran används för att fastställa korrekt datum/tid för att returnera. Om begäran kommer från en robot eller ett annat centraliserat program innan du kommer till LUIS, korrigerar du LUIS för timezone.

### <a name="endpoint-querystring-parameter"></a>Parameter för slut punkts QueryString
Tids zonen korrigeras genom att lägga till användarens tidszon till [slut punkten](https://go.microsoft.com/fwlink/?linkid=2092356) med `timezoneOffset` hjälp av param. Värdet för `timezoneOffset` ska vara ett positivt eller negativt tal, i minuter, för att ändra tiden.

|Param|Värde|
|--|--|
|`timezoneOffset`|positivt eller negativt tal, i minuter|

### <a name="daylight-savings-example"></a>Exempel på sommar tids besparingar
Om du behöver den returnerade förskapade datetimeV2 för att justera för sommar tid, bör du `timezoneOffset` använda parametern QueryString med ett +/-värde i minuter för [slut punkts](https://go.microsoft.com/fwlink/?linkid=2092356) frågan.

#### <a name="v2-prediction-endpoint-request"></a>[V2-begäran om slut punkts förutsägelse](#tab/V2)

Lägg till 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Ta bort 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[V3 förutsägelse slut punkts förfrågan](#tab/V3)

Lägg till 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Ta bort 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-koden bestämmer korrekt värde för timezoneOffset
I följande C#-kod används [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) -klassens [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) -metod för att `timezoneOffset` avgöra korrekt baserat på system tid:

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
> [Korrigera stavfel i den här självstudien](luis-tutorial-bing-spellcheck.md)
