---
title: Fördefinierade entiteter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en lista över de fördefinierade entiteter som ingår i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219501"
---
# <a name="entities-per-culture"></a>Entiteter per kultur

Språkförståelse (LUIS) tillhandahåller förskapade entiteter. När en entitet som är färdiga ingår i ditt program, innehåller LUIS motsvarande entitet förutsägelser i svaret slutpunkt. Alla exempel yttranden är också märkta med entiteten. Fördefinierade entiteternas beteende **kan** ändras. Om inget annat anges, är fördefinierade entiteter tillgängliga i alla THOMAS programmet språk (kulturer). I följande tabell visas de fördefinierade entiteter som stöds för varje kultur.

Fördefinierade entitet   |   Engelska (USA)<br>```En-us```   |   Franska (Frankrike)<br>```fr-FR```   |   Italienska (Italien)<br>```it-IT```   |   Spanska (Spanien)<br>```es-ES```   |   Kinesiska<br>```zh-CN```   |   Tyska<br>```de-DE```   |   Portugisiska (Brasilien)<br>```pt-BR```   |   Japanska (Japan)<br>```ja-JP```   |   Koreanska (Korea)<br>```ko-kr```   | Franska (Kanada)<br>```fr-CA```   |   Spanska (Mexiko)<br>```es-MX```   |   Nederländska (Nederländerna)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Valuta](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[E-post](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Antal](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Se information på [inaktuell fördefinierade entiteter](luis-reference-prebuilt-deprecated.md)

KeyPhrase är inte tillgänglig i alla subkulturer av portugisiska (Brasilien) - ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till fördefinierade entitet kulturer
Fördefinierade entiteter har utvecklats i identifierare fulltext open source-projektet. [Bidra](https://github.com/Microsoft/Recognizers-Text) i projektet. Det här projektet innehåller exempel på valuta per kultur. 

GeographyV2 och PersonName ingår inte i identifierare fulltext-projektet. Problem med dessa förskapade entiteter, öppna en [supportförfrågan](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [nummer](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), och [valuta](luis-reference-prebuilt-currency.md) entiteter. 
