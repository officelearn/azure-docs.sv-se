---
title: Förstå begrepp för förändring i THOMAS - Azure | Microsoft Docs
description: Lär dig hur du kan ändra data innan förutsägelser språk förstå (THOMAS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: ab9b9fe5c34c334fd65971f9040c8e43467aa242
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112158"
---
# <a name="data-alterations"></a>Ändringar av data
THOMAS finns olika sätt att manipulera utterance före eller under förutsägelser. 

## <a name="correct-spelling-errors-in-utterance"></a>Rätt stavfel i utterance
THOMAS använder [Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) att korrigera stavfel i utterance. THOMAS måste nyckeln som associeras med den tjänsten. Skapa nyckeln och sedan lägga till nyckeln som en querystring-parametern i den [endpoint](https://aka.ms/luis-endpoint-apis). 

Du kan också korrigera stavfel i den **Test** panelen av [ange nyckeln](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Nyckeln sparas som en sessionsvariabel i webbläsaren för panelen Test. Lägga till nyckeln i panelen Test i varje webbläsarsession som du vill att stavningen åtgärdat. 

Användning av nyckeln i panelen test och vid slutpunkten räknas in i [nyckelanvändning](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvoten. THOMAS implementerar Bing stavningskontroll gränser för textens längd. 

Slutpunkten kräver två parametrar för stavningskorrigeringar ska fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolesk|
|`bing-spell-check-subscription-key`|[Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) slutpunktsnyckel|

När [Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) upptäcker ett fel och den ursprungliga utterance korrigerade utterance returneras tillsammans med förutsägelser från slutpunkten.

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszon på fördefinierade datetimeV2 entiteten
När en THOMAS app använder fördefinierade datetimeV2 entiteten, returneras ett datetime-värde i svaret förutsägelse. Tidszonen för begäran används för att fastställa rätt datetime som ska returneras. Om begäran kommer från en bot eller ett annat centraliserad program innan du hämtar till THOMAS, korrigera tidszonen THOMAS använder. 

### <a name="endpoint-querystring-parameter"></a>Slutpunkten querystring-parametern
Tidszonen är åtgärdat genom att lägga till användarens tidszon för den [endpoint](https://aka.ms/luis-endpoint-apis) med hjälp av den `timezoneOffset` param. Värdet för `timezoneOffset` måste vara positivt eller negativt tal, i minuter, för att ändra tiden.  

|Param|Värde|
|--|--|
|`timezoneOffset`|positivt eller negativt tal, i minuter|

### <a name="daylight-savings-example"></a>Daylight besparingar exempel
Om du behöver returnerade färdiga datetimeV2 att justera för sommartid, bör du använda den `timezoneOffset` querystring parameter med en +/-värde i minuter för den [endpoint](https://aka.ms/luis-endpoint-apis) frågan.

Lägg till 60 minuter: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=TURN indikeringar på? **timezoneOffset = 60**& utförlig = {boolesk} & stavningskontroll = {boolesk} & mellanlagring = {boolesk} & bing-stavningskontroll-check-prenumeration-nyckel = {sträng} & Logga = {boolesk}

Ta bort 60 minuter: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=TURN indikeringar på? **= timezoneOffset-60**& utförlig = {boolesk} & stavningskontroll = {boolesk} & mellanlagring = {boolesk} & bing-stavningskontroll-check-prenumeration-nyckel = {sträng} & Logga = {boolesk}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kod anger rätt värde för timezoneOffset
Följande C#-kod används den [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) klassens [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) metod för att bestämma rätt `timezoneOffset` baserat på tid:

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
> [Rätt stavfel med den här självstudiekursen](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions