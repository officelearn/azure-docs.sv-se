---
title: Bästa praxis för att forma JSON i Azure Time Series Insights frågor | Microsoft Docs
description: Lär dig hur du kan förbättra din Azure Time Series Insights frågans effektiviteten.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244467"
---
# <a name="shape-json-to-maximize-query-performance"></a>Forma JSON för att maximera prestanda för frågor 

Den här artikeln innehåller råd om hur du form JSON för att maximera effektiviteten hos dina Azure Time Series Insights-frågor.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Läs om bästa praxis för att forma JSON för att uppfylla dina lagringsbehov.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Bästa praxis
Tänk på hur du skickar händelser till Time Series Insights. Det kan du alltid:

1. Skicka data via nätverket så effektivt som möjligt.
1. Kontrollera att dina data lagras på ett sätt så att du kan utföra aggregeringar som passar ditt scenario.
1. Se till att du inte nå Time Series Insights maximala egenskapen gränserna för:
   - 600 egenskaper (kolumner) för S1-miljöer.
   - 800 egenskaper (kolumner) för S2-miljöer.

Följande riktlinjer hjälper dig för att kontrollera den bästa möjliga frågeprestanda:

1. Använd inte dynamisk egenskaper, till exempel en tagg-ID som ett egenskapsnamn. Den här användning bidrar till att gränsen för högsta egenskaper.
1. Skicka inte onödiga egenskaper. Om en fråga egenskap krävs inte, är det bäst att inte skicka den. På så sätt undvika storage-begränsningar.
1. Använd [referensdata](time-series-insights-add-reference-data-set.md) att undvika att skicka statiska data över nätverket.
1. Dela dimensionsegenskaper bland flera händelser för att skicka data mer effektivt över nätverket.
1. Använd inte djupgående matris kapsling. Time Series Insights har stöd för upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
1. Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Skicka dem separat minskar antalet händelser och kan förbättra frågeprestanda eftersom färre händelser måste bearbetas. När det finns flera åtgärder, minimerar skickas som värden i en enskild egenskap risken att du når gränsen för högsta egenskapen.

## <a name="example-overview"></a>Exempel-översikt

Följande två exempel visar hur du skickar händelser för att markera rekommendationerna ovan. Varje exemplet ser du hur rekommendationerna har tillämpats.

Exemplen är baserade på ett scenario där flera enheter skicka mätning av faktisk användning eller signaler. Mätning av faktisk användning och signaler kan vara Flow hastighet, Oljetryck, temperatur och fuktighet. I det första exemplet att det finns några mått på alla enheter. Det andra exemplet har många enheter, och varje enhet skickar många unika mätning av faktisk användning.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario ett: Endast några mått finns

> [!TIP]
> Vi rekommenderar att du skickar varje mått eller en signal som en separat egenskap eller en kolumn.

I följande exempel har ett enda meddelande på Azure IoT Hub där den yttersta matrisen innehåller ett delat avsnitt av vanliga dimensionsvärden. Yttre matrisen använder referensdata för att öka effektiviteten för meddelandet. Referensdata innehåller metadata om enheter som inte ändras med varje händelse, men den innehåller användbara egenskaper för dataanalys. Batchbearbetning vanliga dimensionsvärden och använda referens för data som sparas på byte som skickas över nätverket, vilket gör meddelandet mer effektivt.

Exempel-JSON-nyttolast:

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

* Referenstabell för data som har nyckelegenskapen **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | RAD\_DATA | EU |
   | RÅÅÅ | RAD\_DATA | USA |

* Time Series Insights händelse tabell, efter att förenkla:

   | deviceId | messageId | deviceLocation | timestamp | serie. Flow hastighet ft3/s | serie. Motorn olja tryck psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | RAD\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | RAD\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | RÅÅÅ | RAD\_DATA | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Anmärkningar om de två tabellerna:

- Den **deviceId** kolumnen fungerar som kolumnrubriken för olika enheter i en flotta. Eget egenskapsnamn begränsar vilket gör att deviceId värdet Totalt antal enheter 595 (för S1-miljöer) eller 795 (för S2-miljöer) med de fem kolumnerna.
- Egenskaper för onödiga undviks, exempel, märke och modell information. Eftersom egenskaperna inte efterfrågas i framtiden, kan eliminera dem bättre nätverks- och lagringseffektivitet.
- Referensdata används för att minska antalet byte som överförs i nätverket. De två attributen **messageId** och **deviceLocation** kopplas med hjälp av nyckelegenskapen **deviceId**. Dessa data är kopplad till dessa data när ingångshändelser och lagras sedan i Time Series Insights för frågor.
- Två nivåer av inkapsling används, vilket är den maximala mängden kapsling som stöds av Time Series Insights. Det är viktigt att undvika djupt kapslade matriser.
- Mått skickas som separata egenskaper inom samma objekt eftersom det inte finns några åtgärder. Här kan **serien. Flow Rate psi** och **serien. Motorn olja tryck ft3/s** är unika kolumner.

## <a name="scenario-two-several-measures-exist"></a>Scenario två: Det finns flera åtgärder

> [!TIP]
> Vi rekommenderar att du ska skicka mätning av faktisk användning som ”typ”, ”enhet” och ”värde” tupplar.

Exempel-JSON-nyttolast:

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

* Referenstabell för data som har nyckelegenskaper **deviceId** och **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | Enhet |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s |
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | psi |
   | RÅÅÅ | pumpRate | RAD\_DATA | USA | Flöde | ft3/s |
   | RÅÅÅ | oilPressure | RAD\_DATA | USA | Oljetryck | psi |

* Time Series Insights händelse tabell, efter att förenkla:

   | deviceId | series.tagId | messageId | deviceLocation | type | Enhet | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | psi | 2018-01-17T01:17:00Z | 49.2 |
   | RÅÅÅ | pumpRate | RAD\_DATA | USA | Flöde | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | RÅÅÅ | oilPressure | RAD\_DATA | USA | Oljetryck | psi | 2018-01-17T01:18:00Z | 22.2 |

Anmärkningar om de två tabellerna:

- Kolumnerna **deviceId** och **series.tagId** utgöra kolumnrubriker för olika enheter och taggar i en flotta. Med hjälp av varje som sin egen attributet begränsar frågan till 594 (för S1-miljöer) eller 794 (för S2-miljöer) totalt antal enheter med sex kolumner.
- onödiga egenskaper har undvikas av skäl som anges i det första exemplet.
- Referensdata som används för att minska antalet byte som överförs i nätverket genom att introducera **deviceId**, som används för unikt par **messageId** och **deviceLocation**. Den sammansatta nyckeln **series.tagId** används för att unikt par **typ** och **enhet**. Den sammansatta nyckeln tillåter den **deviceId** och **series.tagId** par som ska användas för att referera till fyra värden: **messageId deviceLocation, Skriv** och **enhet**. Dessa data är kopplad till telemetridata vid inkommande tidpunkt. Den är lagrad i Time Series Insights för frågor.
- två nivåer av inkapsling används av skäl som anges i det första exemplet.

### <a name="for-both-scenarios"></a>För båda scenarierna

För en egenskap med ett stort antal möjliga värden är det bäst att skicka som distinkta värden i en enskild kolumn istället för att skapa en ny kolumn för varje värde. I föregående två exempel:

  - I det första exemplet har några egenskaper flera värden, så det är lämpligt att kontrollera var och en separat egenskap.
  - I det andra exemplet anges inte åtgärderna som som enskilda egenskaper. I stället är de en matris med värden eller mått under en gemensam egenskap för serien. Den nya nyckeln **tagId** skickas, som skapar den nya kolumnen **series.tagId** i tabellen utplattad. De nya egenskaperna **typ** och **enhet** skapas med hjälp av referensdata så att egenskapen inte är gränsen.

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights frågesyntax](/rest/api/time-series-insights/ga-query-syntax) mer information om frågesyntaxen för Time Series Insights-dataåtkomst REST API.
- Lär dig [så formhändelser](./time-series-insights-send-events.md).
