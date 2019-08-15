---
title: Metod tips för att forma JSON i Azure Time Series Insights frågor | Microsoft Docs
description: Lär dig hur du kan förbättra din Azure Time Series Insights frågas effektivitet.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 08/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 48e09a64812f7552bd79c529138db693df283790
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947150"
---
# <a name="shape-json-to-maximize-query-performance"></a>Form-JSON för att maximera prestanda för frågor 

Den här artikeln innehåller rikt linjer för hur du formar JSON för att maximera effektiviteten hos dina Azure Time Series Insightss frågor.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Lär dig metod tips för utformning av JSON för att uppfylla dina lagrings behov.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Bästa praxis
Tänk på hur du skickar händelser till Time Series Insights. Det vill säga att du alltid:

1. Skicka data via nätverket så effektivt som möjligt.
1. Se till att dina data lagras på ett sätt så att du kan utföra agg regeringar som passar ditt scenario.
1. Se till att du inte når Time Series Insights maximala egenskaps gränserna för:
   - 600 egenskaper (kolumner) för S1-miljöer.
   - 800 egenskaper (kolumner) för S2-miljöer.

Följande rikt linjer hjälper dig att säkerställa bästa möjliga prestanda för frågor:

1. Använd inte dynamiska egenskaper, till exempel ett tagg-ID, som ett egenskaps namn. Den här användningen bidrar till att nå Max gränsen för egenskaper.
1. Skicka inte onödiga egenskaper. Om en frågeparameter inte krävs är det bäst att inte skicka den. På så sätt undviker du lagrings begränsningarna.
1. Använd [referens data](time-series-insights-add-reference-data-set.md) för att undvika att skicka statiska data över nätverket.
1. Dela dimensions egenskaper mellan flera händelser för att skicka data över nätverket mer effektivt.
1. Använd inte djupgående matris kapsling. Time Series Insights stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights fören klar matriserna i meddelandena till flera händelser med egenskaps värde par.
1. Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Att skicka dem separat minskar antalet händelser och kan förbättra frågans prestanda eftersom färre händelser behöver bearbetas. När det finns flera mått kan du skicka dem som värden i en enda egenskap och minimera möjligheten att nå den maximala egenskaps gränsen.

## <a name="example-overview"></a>Exempel översikt

Följande två exempel visar hur du skickar händelser för att markera föregående rekommendationer. Följande exempel visar hur du kan se hur rekommendationerna tillämpades.

Exemplen bygger på ett scenario där flera enheter skickar mätningar eller signaler. Mått eller signaler kan vara flödes takt, motor olje tryck, temperatur och fuktighet. I det första exemplet finns det några mått på alla enheter. Det andra exemplet har många enheter och varje enhet skickar många unika mått.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario ett: Det finns bara några mätningar

> [!TIP]
> Vi rekommenderar att du skickar varje mått eller signal som en separat egenskap eller kolumn.

I följande exempel finns ett enda Azure IoT Hub-meddelande där den yttre matrisen innehåller ett delat avsnitt av vanliga dimensions värden. Den yttre matrisen använder referens data för att öka effektiviteten för meddelandet. Referens data innehåller metadata för enheten som inte ändras med varje händelse, men den innehåller användbara egenskaper för data analys. Att gruppera vanliga dimensions värden och använda referens data sparas på byte som skickas via kabeln, vilket gör meddelandet mer effektivt.

Exempel på JSON-nytto last:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Referens data tabell som har Key Property **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINJE\_DATA | EU |
   | FYYY | LINJE\_DATA | USA |

* Time Series Insights händelse tabell efter förenkling:

   | deviceId | messageId | deviceLocation | timestamp | serie. Flow hastighet ft3/s | serie. Motorn olja tryck psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINJE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINJE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINJE\_DATA | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Anteckningar om dessa två tabeller:

- Kolumnen **deviceId** fungerar som kolumn rubrik för de olika enheterna i en flotta. Att göra deviceId-värdet till ett eget egenskaps namn begränsar det totala antalet enheter till 595 (för S1-miljöer) eller 795 (för S2-miljöer) med de andra fem kolumnerna.
- Onödiga egenskaper undviks, till exempel märke och modell information. Eftersom det inte går att fråga efter egenskaper i framtiden ger det bättre nätverks-och lagrings effektivitet.
- Referens data används för att minska antalet byte som överförs via nätverket. De två attributen **messageId** och **deviceLocation** kopplas ihop med nyckel egenskap **deviceId**. Dessa data är kopplade till telemetridata vid ingångs tiden och lagras sedan i Time Series Insights för frågor.
- Två lager kapsling används, vilket är den maximala kapslings mängden som stöds av Time Series Insights. Det är viktigt att undvika djupt kapslade matriser.
- Måtten skickas som separata egenskaper inom samma objekt eftersom det finns några mått. Här, **serien. PSI** och serie för flödes frekvens **. Motor olja-tryck ft3/s** är unika kolumner.

## <a name="scenario-two-several-measures-exist"></a>Scenario två: Det finns flera åtgärder

> [!TIP]
> Vi rekommenderar att du skickar mått som "Type", "Unit" och "value"-tupler.

Exempel på JSON-nytto last:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Referens data tabell som innehåller nyckel egenskaperna **deviceId** och **serien. tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | enhet |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINJE\_DATA | EU | Flödes hastighet | ft3/s |
   | FXXX | oilPressure | LINJE\_DATA | EU | Motor Oil-tryck | psi |
   | FYYY | pumpRate | LINJE\_DATA | USA | Flödes hastighet | ft3/s |
   | FYYY | oilPressure | LINJE\_DATA | USA | Motor Oil-tryck | psi |

* Time Series Insights händelse tabell efter förenkling:

   | deviceId | series.tagId | messageId | deviceLocation | type | enhet | timestamp | serie. Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINJE\_DATA | EU | Flödes hastighet | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINJE\_DATA | EU | Motor Oil-tryck | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINJE\_DATA | EU | Flödes hastighet | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINJE\_DATA | EU | Motor Oil-tryck | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINJE\_DATA | USA | Flödes hastighet | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINJE\_DATA | USA | Motor Oil-tryck | psi | 2018-01-17T01:18:00Z | 22.2 |

Anteckningar om dessa två tabeller:

- Kolumnerna **deviceId** och **serien. tagId** fungerar som kolumn rubriker för de olika enheterna och taggarna i en flotta. Om du använder varje as-attribut begränsas frågan till 594 (för S1-miljöer) eller 794 (för S2-miljöer) totalt enheter med de andra sex kolumnerna.
- Onödiga egenskaper förhindrades, på grund av orsaken i det första exemplet.
- Referens data används för att minska antalet byte som överförs via nätverket genom att introducera **deviceId**, som används för det unika paret **messageId** och **deviceLocation**. Den sammansatta nyckel **serien. tagId** används för det unika paret av **typen** och **enheten**. Den sammansatta nyckeln tillåter att **tagId** -paret för **deviceId** och Series används för att referera till fyra värden: **messageId, deviceLocation, Type** och **Unit**. Dessa data är kopplade till telemetridata vid ingångs tiden. Den lagras sedan i Time Series Insights för frågor.
- Två kapslings nivåer används för den orsak som anges i det första exemplet.

### <a name="for-both-scenarios"></a>För båda scenarierna

För en egenskap med ett stort antal möjliga värden är det bäst att skicka som distinkta värden i en enda kolumn i stället för att skapa en ny kolumn för varje värde. Från de föregående två exemplen:

  - I det första exemplet har några få egenskaper flera värden, så det är lämpligt att göra varje enskild egenskap.
  - I det andra exemplet har måtten inte angetts som enskilda egenskaper. I stället är de en matris med värden eller mått under en gemensam serie egenskap. Den nya nyckeln **tagId** skickas, vilket skapar den nya kolumn **serien. tagId** i den sammanslagna tabellen. Den nya egenskaps **typen** och **enheten** skapas med hjälp av referens data så att egenskaps gränsen inte nås.

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights frågesyntaxen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) för att lära dig mer om frågesyntaxen för Time Series Insights data åtkomst REST API.
- Lär dig [hur du formar händelser](./time-series-insights-send-events.md).