---
title: Den färdiga THOMAS entiteter referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller en lista över fördefinierade entiteter som ingår i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316892"
---
# <a name="entities-per-culture"></a>Entiteter per kultur

Språk förstå (THOMAS) innehåller fördefinierade entiteter. När en fördefinierad entitet ingår i ditt program innehåller THOMAS motsvarande entitet förutsägelser endpoint-svar. Alla exempel utterances är också märkta med entiteten. Fördefinierade entiteternas beteende **kan** ändras. Om inget annat anges är färdiga entiteter tillgängliga i alla THOMAS programmet språk (kulturer). I följande tabell visas de fördefinierade enheter som stöds för varje kultur.

Fördefinierade entitet   |   Engelska (USA)<br>```En-us```   |   Franska (Frankrike)<br>```fr-FR```   |   Italienska (Italien)<br>```it-IT```   |   Spanska (Spanien)<br>```es-ES```   |   Kinesiska<br>```zh-CN```   |   Tyska<br>```de-DE```   |   Portugisiska (Brasilien)<br>```pt-BR```   |   Japanska (Japan)<br>```ja-JP```   |   Koreanska (Korea)<br>```ko-kr```   | Franska (Kanada)<br>```fr-CA```   |   Spanska (Mexiko)<br>```es-MX```   |   Nederländska (Nederländerna)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Valuta](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: cent)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (ex: bit/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[E-post](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Antal](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Ordningstalet](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Du hittar information om [föråldrad färdiga entiteter](luis-reference-prebuilt-deprecated.md)

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>Exempel på färdiga entiteter i en-us kultur
I följande tabell visas fördefinierade entiteter med exempeldata och returvärden.

Fördefinierade entitet   |   Exempel utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | Se [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | Se [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|


## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till fördefinierade entitet kulturer
De färdiga entiteterna utvecklas i projektet identifierare Text öppen källkod. Kontrollera [bidra](https://github.com/Microsoft/Recognizers-Text) i projektet. Det här projektet innehåller exempel på valuta per kultur. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [nummer](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), och [valuta](luis-reference-prebuilt-currency.md) entiteter. 