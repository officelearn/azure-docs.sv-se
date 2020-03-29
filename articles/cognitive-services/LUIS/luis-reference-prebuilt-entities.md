---
title: Alla fördefinierade enheter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller listor över de fördefinierade entiteter som ingår i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219724"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteter per kultur i luis-modellen

Språkförståelse (LUIS) ger fördefinierade entiteter. När en fördefinierad entitet ingår i ditt program, innehåller LUIS motsvarande entitetsförutsägels i slutpunktssvaret. Alla exempel yttranden är också märkta med entiteten. Beteendet för fördefinierade entiteter **kan inte** ändras. Om inget annat anges är fördefinierade entiteter tillgängliga i alla LUIS-programingresser (kulturer). I följande tabell visas de fördefinierade entiteter som stöds för varje kultur.

|Kultur|Subkulturer|Anteckningar|
|--|--|--|
|Kinesiska|[zh-CN](#chinese-entity-support)||
|Nederländska|[nl-NL](#dutch-entity-support)||
|Svenska|[en-US (amerikansk)](#english-american-entity-support)||
|Franska|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Frankrike),](#french-france-entity-support) ||
|Tyska|[de-DE](#german-entity-support)||
|Italienska|[it-IT](#italian-entity-support)||
|Japanska|[ja-JP](#japanese-entity-support)||
|Koreansk|[ko-KR](#korean-entity-support)||
|Portugisiska|[pt-BR (Brasilien)](#portuguese-brazil-entity-support)||
|Spanska|[es-ES (Spanien)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turkiska|[Turkiska](#turkish-entity-support)|Inga fördefinierade enheter som stöds i turkiska|

## <a name="prediction-endpoint-runtime"></a>Förutsägelseslutpunktskörning

Tillgängligheten för en fördefinierad entitet på ett visst språk bestäms av förutsägelseslutpunktskörningsversionen.

## <a name="chinese-entity-support"></a>Stöd för kinesiska enheter

Följande entiteter stöds:

|Fördefinierad enhet|```zh-CN``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Stöd för nederländska enheter

Följande entiteter stöds:

|Fördefinierad enhet|```nl-NL``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Stöd för engelska (amerikanska) enheter

Följande entiteter stöds:

|Fördefinierad enhet|```en-US``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Stöd från franska (Frankrike)

Följande entiteter stöds:

|Fördefinierad enhet|```fr-FR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |   -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Stöd för franska (kanadensiska) enheter

Följande entiteter stöds:

|Fördefinierad enhet|```fr-CA``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Stöd till tyska enheter

Följande entiteter stöds:

|Fördefinierad enhet|```de-DE``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Stöd till italienska enheter

Italienska fördefinierade ålder, valuta, dimension, tal, _procentuell upplösning_ ändras från V2 och V3 förhandsvisning.

Följande entiteter stöds:

|Fördefinierad enhet|```it-IT``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Stöd för japanska enheter

Följande entiteter stöds:

|Fördefinierad enhet|```ja-JP``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Stöd för koreanska enheter

Följande entiteter stöds:

|Fördefinierad enhet|```ko-KR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Stöd för portugisiska (Brasilien)

Följande entiteter stöds:

|Fördefinierad enhet|```pt-BR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Stöd för spanska (Spanien)

Följande entiteter stöds:

|Fördefinierad enhet|```es-ES``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    V2, V3   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Stöd för spanska (Mexiko)

Följande entiteter stöds:

|Fördefinierad enhet|```es-MX``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månad<br>Vecka<br>day   |    -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fraktionerad enhet (t.ex. öre)  |    -   |
[DatumtidV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumområde<br>time<br>tidsföring   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>Vikt<br>information (t.ex. bit/byte)<br>längd (ex: meter)<br>hastighet (t.ex. mil per timme)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Nyckelfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Ordnings och annan](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonNamn](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Se anteckningar om [föråldrade fördefinierade entiteter](luis-reference-prebuilt-deprecated.md)

KeyPhrase är inte tillgängligt i alla subkulturer ```pt-BR```i portugisiska (Brasilien) - .

## <a name="turkish-entity-support"></a>Stöd från turkiska enheter

**Det finns inga fördefinierade enheter som stöds på turkiska.**

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till fördefinierade entitetskulturer
De fördefinierade entiteterna utvecklas i projektet Recognizers-Text med öppen källkod. [Bidra](https://github.com/Microsoft/Recognizers-Text) till projektet. Detta projekt innehåller exempel på valuta per kultur.

GeographyV2 och PersonName ingår inte i projektet Recognizers-Text. Om du vill ha problem med dessa fördefinierade entiteter öppnar du en [supportbegäran](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [antalet,](luis-reference-prebuilt-number.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)och valutaentiteter. [currency](luis-reference-prebuilt-currency.md)
