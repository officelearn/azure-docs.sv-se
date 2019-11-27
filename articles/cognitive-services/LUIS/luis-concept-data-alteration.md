---
title: Data ändring – LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur data kan ändras innan förutsägelser i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222025"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändra uttryck data före eller under förutsägelse
LUIS innehåller olika sätt att manipulera uttryck före eller under förutsägelser. Detta inkluderar att [åtgärda stavnings](luis-tutorial-bing-spellcheck.md)-och åtgärda tids zons problem för färdiga [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Rätta stavfel i uttryck

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS använder [API för stavningskontroll i Bing v7](../Bing-Spell-Check/overview.md) för att korrigera stavfel i uttryck. LUIS måste den nyckel som associeras med den tjänsten. Skapa nyckeln och Lägg sedan till nyckeln som en QueryString-parameter i [slut punkten](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

Slutpunkten kräver två parametrar för stavningskorrigeringar ska fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolesk|
|`bing-spell-check-subscription-key`|[API för stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) slut punkts nyckel för v7|

När [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) identifierar ett fel returneras den ursprungliga uttryck och de korrigerade uttryck returneras tillsammans med förutsägelser från slut punkten.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)
 
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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszonen för fördefinierade datetimeV2 entitet
När en LUIS-app använder den fördefinierade [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteten kan ett datetime-värde returneras i förutsägelse svaret. Tidszonen för begäran används för att fastställa rätt datum/tid att returnera. Om förfrågan kommer från en bot eller ett annat centraliserad program innan du kommer till LUIS, korrigera tidszonen LUIS använder. 

### <a name="endpoint-querystring-parameter"></a>Frågeparametern slutpunkt
Tids zonen korrigeras genom att lägga till användarens tidszon till [slut punkten](https://go.microsoft.com/fwlink/?linkid=2092356) med hjälp av `timezoneOffset` param. Värdet för `timezoneOffset` ska vara ett positivt eller negativt tal, i minuter, för att ändra tiden.  

|Param|Värde|
|--|--|
|`timezoneOffset`|positivt eller negativt tal på bara några minuter|

### <a name="daylight-savings-example"></a>Sommartid besparingar exempel
Om du behöver den returnerade förskapade datetimeV2 för att justera för sommar tid, bör du använda parametern `timezoneOffset` QueryString med ett +/-värde i minuter för [slut punkts](https://go.microsoft.com/fwlink/?linkid=2092356) frågan.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2-begäran om slut punkts förutsägelse](#tab/V2)

Lägg till 60 minuter: 

https://{region}. API. kognitiv. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = sätt ljuset på? **timezoneOffset = 60**& verbose = {boolean} & stavnings kontroll = {boolean} & mellanlagring = {boolean} & Bing-stavning-check-prenumeration-nyckel = {string} & logg = {Boolean}

Ta bort 60 minuter: 

https://{region}. API. kognitiv. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = sätt ljuset på? **timezoneOffset =-60**& verbose = {boolean} & stavnings kontroll = {boolean} & mellanlagring = {boolean} & Bing-stavning-check-prenumeration-nyckel = {string} & logg = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 förutsägelse slut punkts förfrågan](#tab/V3)

Lägg till 60 minuter:

https://{region}. API. kognitivt. Microsoft. com/Luis/v 3.0 – för hands version/appar/{appId}/platser/produktion/förutsägelse? fråga = aktivera indikeringarna? **timezoneOffset = 60**& stavnings kontroll = {boolean} & Bing-stavning-check-prenumeration-nyckel = {string} & logg = {Boolean}

Ta bort 60 minuter: 

https://{region}. API. kognitivt. Microsoft. com/Luis/v 3.0 – för hands version/appar/{appId}/platser/produktion/förutsägelse? fråga = aktivera indikeringarna? **timezoneOffset =-60**& stavnings kontroll = {boolean} & Bing-stavning-check-prenumeration-nyckel = {string} & logg = {Boolean}

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kod anger rätt värde för timezoneOffset
I följande C# kod används [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) -klassens [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) -metod för att fastställa rätt `timezoneOffset` baserat på system tid:

```CSharp
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
