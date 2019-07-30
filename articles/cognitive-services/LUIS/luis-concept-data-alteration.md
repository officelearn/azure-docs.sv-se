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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 198ce98808c8a62a839d154c365518c9e8263056
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619907"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändra uttryck data före eller under förutsägelse
LUIS innehåller olika sätt att manipulera uttryck före eller under förutsägelser. Detta inkluderar att [åtgärda stavnings](luis-tutorial-bing-spellcheck.md)-och åtgärda tids zons problem för färdiga [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Rätta stavfel i uttryck
LUIS använder [Bing stavningskontroll kontrollera API V7](../Bing-Spell-Check/overview.md) att rätta stavfel i uttryck. LUIS måste den nyckel som associeras med den tjänsten. Skapa nyckeln och sedan lägga till nyckeln som en frågesträngsparameter på den [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356). 

Du kan också korrigera stavfel i den **Test** panelen av [att ange nyckeln](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Nyckeln sparas som en sessionsvariabel i webbläsaren till Test-panelen. Lägga till nyckeln i panelen Test i varje webbläsarsession som du vill att stavning korrigerats. 

Användning av nyckeln på panelen test och på slutpunkten räknas in i den [nyckelanvändning](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvot. LUIS implementerar stavningskontroll i Bing gränser för textlängd. 

Slutpunkten kräver två parametrar för stavningskorrigeringar ska fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolesk|
|`bing-spell-check-subscription-key`|[Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) slutpunktsnyckel|

När [Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) upptäcker ett fel och den ursprungliga uttryck korrigerad uttryck returneras tillsammans med förutsägelser från slutpunkten.

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
 
### <a name="list-of-allowed-words"></a>Lista över tillåtna ord
API för stavnings kontroll i Bing som används i LUIS stöder inte en lista (kallas även vitlista) för ord som ska ignoreras under stavnings kontrollen. Om du behöver tillåta en lista över ord eller akronymer, bearbetar du uttryck i klient programmet innan du skickar uttryck till LUIS för avsikts förutsägelse.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszonen för fördefinierade datetimeV2 entitet
När en LUIS-app använder den fördefinierade [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteten kan ett datetime-värde returneras i förutsägelse svaret. Tidszonen för begäran används för att fastställa rätt datum/tid att returnera. Om förfrågan kommer från en bot eller ett annat centraliserad program innan du kommer till LUIS, korrigera tidszonen LUIS använder. 

### <a name="endpoint-querystring-parameter"></a>Frågeparametern slutpunkt
Tidszonen korrigeras genom att lägga till användarens tidszon för den [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) med hjälp av den `timezoneOffset` param. Värdet för `timezoneOffset` ska vara positivt eller negativt tal, på bara några minuter att ändra tiden.  

|Param|Värde|
|--|--|
|`timezoneOffset`|positivt eller negativt tal på bara några minuter|

### <a name="daylight-savings-example"></a>Sommartid besparingar exempel
Om du behöver den returnerade fördefinierade datetimeV2 att justera för sommartid, bör du använda den `timezoneOffset` Frågeparametern med en +/-värdet i minuter för den [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) fråga.

Lägg till 60 minuter: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=TURN lamporna på? **timezoneOffset = 60**& utförlig = {boolesk} & stavningskontroll = {boolesk} & mellanlagring = {boolesk} & bing-stavningskontroll-kontroll-subscription-key = {sträng} & logg = {boolesk}

Ta bort 60 minuter: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=TURN lamporna på? **timezoneOffset = – 60**& utförlig = {boolesk} & stavningskontroll = {boolesk} & mellanlagring = {boolesk} & bing-stavningskontroll-kontroll-subscription-key = {sträng} & logg = {boolesk}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kod anger rätt värde för timezoneOffset
Följande C#-kod använder den [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) klassens [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metod för att fastställa rätt `timezoneOffset` baserat på systemtid:

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
> [Rätt stavfel i den här självstudiekursen](luis-tutorial-bing-spellcheck.md)
