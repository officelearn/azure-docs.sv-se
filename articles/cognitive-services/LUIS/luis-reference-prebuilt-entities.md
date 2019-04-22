---
title: Alla fördefinierade entiteter
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en lista över de fördefinierade entiteter som ingår i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: 96157511c0255774a64c0f64a333fe88a37f80d6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261436"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteter per kulturen i LUIS-modellen

Språkförståelse (LUIS) tillhandahåller förskapade entiteter. När en entitet som är färdiga ingår i ditt program, innehåller LUIS motsvarande entitet förutsägelser i svaret slutpunkt. Alla exempel yttranden är också märkta med entiteten. Fördefinierade entiteternas beteende **kan** ändras. Om inget annat anges, är fördefinierade entiteter tillgängliga i alla THOMAS programmet språk (kulturer). I följande tabell visas de fördefinierade entiteter som stöds för varje kultur.

|Kultur|Subkulturer|Anteckningar|
|--|--|--|
|Kinesiska|[zh-CN](#chinese-entity-support)||
|Nederländska|[NL-NL](#dutch-entity-support)||
|Svenska|[en-US (American)](#english-american-entity-support)||
|Franska|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Frankrike)](#french-france-entity-support), ||
|Tyska|[de-DE](#german-entity-support)||
|Italienska|[IT-IT](#italian-entity-support)||
|Japanska|[Ja-JP](#japanese-entity-support)||
|Koreanska|[ko-KR](#korean-entity-support)||
|Portugisiska|[pt-BR (Brasilien)](#portuguese-brazil-entity-support)||
|Spanska|[es-ES (Spanien)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turkiska|[Turkiska](#turkish-entity-support)|Inga förskapade entiteter som stöds i turkiska|

## <a name="chinese-entity-support"></a>Stöd för kinesiska entitet

Följande enheter stöds:

|Fördefinierade entitet|```zh-CN``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Nederländska entitet support

Följande enheter stöds:

|Fördefinierade entitet|```nl-NL``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Stöd för engelska (American) entitet

Följande enheter stöds:

|Fördefinierade entitet|```en-US``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Franska (Frankrike) entitet support

Följande enheter stöds:

|Fördefinierade entitet|```fr-FR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Franska (Kanada) entitet support

Följande enheter stöds:

|Fördefinierade entitet|```fr-CA``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Stöd för tyska entitet

Följande enheter stöds:

|Fördefinierade entitet|```de-DE``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Italienska entitet support

Följande enheter stöds:

|Fördefinierade entitet|```it-IT``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Japanska entitet support

Följande enheter stöds:

|Fördefinierade entitet|```ja-JP``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Koreanska entitet support

Följande enheter stöds:

|Fördefinierade entitet|```ko-KR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    -   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    -   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    -   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Portugisiska (Brasilien) entitet support

Följande enheter stöds:

|Fördefinierade entitet|```pt-BR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Spanska (Spanien) entitet support

Följande enheter stöds:

|Fördefinierade entitet|```es-ES``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    ✔   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    ✔   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Spanska (Mexiko) entitet support

Följande enheter stöds:

|Fördefinierade entitet|```es-MX``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>vecka<br>dag   |    -   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>dollar<br>bråkdelar enhet (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimensionen](luis-reference-prebuilt-dimension.md):<br>volym<br>området<br>vikt<br>information (t.ex.: bitars/byte)<br>längden (ex: mätaren)<br>hastighet (ex: mil per timme)  |    -   | 
[E-post](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nummer](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Procent](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Se information på [inaktuell fördefinierade entiteter](luis-reference-prebuilt-deprecated.md)

KeyPhrase är inte tillgänglig i alla subkulturer av portugisiska (Brasilien) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Stöd för turkiska entitet

**Det finns inga förskapade entiteter som stöds i turkiska.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till fördefinierade entitet kulturer
Fördefinierade entiteter har utvecklats i identifierare fulltext open source-projektet. [Bidra](https://github.com/Microsoft/Recognizers-Text) i projektet. Det här projektet innehåller exempel på valuta per kultur. 

GeographyV2 och PersonName ingår inte i identifierare fulltext-projektet. Problem med dessa förskapade entiteter, öppna en [supportförfrågan](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [nummer](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), och [valuta](luis-reference-prebuilt-currency.md) entiteter. 
