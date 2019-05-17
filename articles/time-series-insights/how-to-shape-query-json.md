---
title: Bästa praxis för att forma JSON i Azure Time Series Insights frågor | Microsoft Docs
description: Lär dig hur du kan förbättra din Azure Time Series Insights frågans effektiviteten.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790069"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Hur du utformar JSON för att maximera prestanda för frågor 

Den här artikeln innehåller vägledning för att forma JSON, att maximera effektiviteten hos dina Azure Time Series Insights-frågor.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Läs om bästa praxis för att forma JSON för att uppfylla dina lagringsbehov.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Regelverk

Det är viktigt att tänka på hur du skickar händelser till Time Series Insights. Nämligen, bör du alltid:

1. Skicka data via nätverket så effektivt som möjligt.
1. Se till att dina data lagras på ett sätt som gör att du kan utföra aggregeringar som passar ditt scenario.
1. Se till att du inte når Time Series Insights maximala egenskapen gränserna för:
   - 600 egenskaper (kolumner) för S1-miljöer.
   - 800 egenskaper (kolumner) för S2-miljöer.

Följande riktlinjer hjälper dig att se till att den bästa möjliga frågeprestanda:

1. Använd inte dynamisk egenskaper, till exempel en tagg-ID som egenskapsnamn, eftersom det bidrar till nått gränsen för högsta egenskaper.
1. Skicka inte onödiga egenskaper. Om en fråga egenskap krävs inte, är det bäst att inte skicka den och undvika storage-begränsningar.
1. Använd [referensdata](time-series-insights-add-reference-data-set.md), för att undvika att skicka statiska data över nätverket.
1. Dela dimensionsegenskaper bland flera händelser att skicka data mer effektivt över nätverket.
1. Använd inte djupgående matris kapsling. Time Series Insights har stöd för upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
1. Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Skicka dem separat minskar antalet händelser och kan bli frågor bättre eftersom färre händelser måste bearbetas. När det finns flera åtgärder, minimerar skickas som värden i en enskild egenskap risken att du nått gränsen för högsta egenskapen.

## <a name="example-overview"></a>Exempel-översikt

Följande två exempel visar skickar händelser för att fokusera på rekommendationerna ovan. Varje exemplet ser du hur rekommendationerna har tillämpats.

Exemplen är baserade på ett scenario där flera enheter skicka mätning av faktisk användning eller signaler. Mätning av faktisk användning eller signaler kan flöda hastighet, Oljetryck, temperatur och fuktighet. I det första exemplet att det finns några mått på alla enheter. Det finns många enheter i det andra exemplet och var och en skickar många unika mätning av faktisk användning.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario ett: endast några mått finns

> [!TIP]
> **Rekommendationen**: skicka varje mätning/signal som en separat egenskapskolumnen.

I följande exempel har ett enda meddelande på IoT Hub, där den yttersta matrisen innehåller ett delat avsnitt av vanliga dimensionsvärden. Yttre matrisen använder referensdata för att öka effektiviteten för meddelandet. Referensdata innehåller metadata om enheter, som inte ändras med varje händelse, men ger användbar egenskaper för dataanalys. Både batchbearbetning vanliga dimensionsvärden och använda referensdata, sparar på byte som skickas över nätverket, vilket gör meddelandet mer effektivt.

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

* Referenstabell för data (nyckelegenskap **deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | RAD\_DATA | EU |
   | RÅÅÅ | RAD\_DATA | USA |

* Time Series Insights händelse tabell (efter att förenkla):

   | deviceId | messageId | deviceLocation | tidsstämpel | serie. Flow hastighet ft3/s | serie. Motorn olja tryck psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | RAD\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | RAD\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | RÅÅÅ | RAD\_DATA | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Ovan:

- Den **deviceId** kolumnen fungerar som kolumnrubriken för olika enheter i en flotta. Försök att upprätta deviceId värdet skulle sin egen egenskapsnamn har begränsad Totalt antal enheter 595 (S1-miljöer) eller 795 (S2-miljöer), med de fem kolumnerna.

- Egenskaper för onödiga undviks, till exempel märke och modell information, osv. Eftersom de inte efterfrågas i framtiden, kan eliminera dem bättre nätverks- och lagringseffektivitet.

- Referensdata används för att minska antalet byte som överförs i nätverket. Två attribut **messageId** och **deviceLocation**, med hjälp av egenskapen viktiga **deviceId**. Dessa data är ansluten med dessa data när ingångshändelser och därefter lagras i Time Series Insights för frågor.

- Två nivåer av inkapsling används, vilket är den maximala mängden kapsling som stöds av Time Series Insights. Det är viktigt att undvika djupt kapslade matriser.

- Mått skickas som separata egenskaper inom samma objekt eftersom det finns några mått. Här kan **serien. Flow Rate psi** och **serien. Motorn olja tryck ft3/s** är unika kolumner.

## <a name="scenario-two-several-measures-exist"></a>Scenario två: det finns flera åtgärder

> [!TIP]
> **Rekommendation:** skicka mätning av faktisk användning som ”typ”, ”enhet”, ”value” tupplar.

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

* Referensdata (nyckelegenskaper är **deviceId** och **series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | typ | enhet |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s |
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | psi |
   | RÅÅÅ | pumpRate | RAD\_DATA | USA | Flöde | ft3/s |
   | RÅÅÅ | oilPressure | RAD\_DATA | USA | Oljetryck | psi |

* Time Series Insights händelse tabell (efter att förenkla):

   | deviceId | series.tagId | messageId | deviceLocation | typ | enhet | tidsstämpel | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | RAD\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | RAD\_DATA | EU | Oljetryck | Psi | 2018-01-17T01:17:00Z | 49.2 |
   | RÅÅÅ | pumpRate | RAD\_DATA | USA | Flöde | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | RÅÅÅ | oilPressure | RAD\_DATA | USA | Oljetryck | psi | 2018-01-17T01:18:00Z | 22.2 |

Ovan:

- Kolumnerna **deviceId** och **series.tagId** utgöra kolumnrubriker för olika enheter och taggar i en flotta. Med hjälp av varje som sin egen attribut skulle begränsad frågan till 594 (S1-miljöer) eller 794 (S2-miljöer) totalt antal enheter med sex kolumner.

- onödiga egenskaper har undvikas av skäl som anges i det första exemplet.

- referensdata som används för att minska antalet byte som överförs i nätverket genom att introducera **deviceId**, för ett unikt par **messageId** och **deviceLocation**. En sammansatt nyckel används, **series.tagId**, för unikt par **typ** och **skalningsenheten**. Den sammansatta nyckeln tillåter den **deviceId** och **series.tagId** par som ska användas att referera till fyra värden: **messageId deviceLocation, Skriv** och **enhet** . Dessa data är ansluten med dessa data när ingångshändelser och därefter lagras i Time Series Insights för frågor.

- två nivåer av inkapsling används av skäl som anges i det första exemplet.

### <a name="for-both-scenarios"></a>För båda scenarierna

Om du har en egenskap med ett stort antal möjliga värden är det bäst att skicka som distinkta värden inom en enda kolumn i stället för att skapa en ny kolumn för varje värde. I föregående två exempel:

  - Det finns några egenskaper som har flera värden, så det är lämpligt att kontrollera var och en separat egenskap i det första exemplet.
  - Men i det andra exemplet ser du att åtgärderna som inte har angetts som enskilda egenskaper, men i stället en matris med värden/åtgärder under en gemensam egenskap för serien. En ny nyckel skickas **tagId** , vilket skapar en ny kolumn **series.tagId** i tabellen utplattad. Nya egenskaper har skapats, **typ** och **enhet**, med hjälp av referensdata, vilket gör den egenskap gränsen som når.

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights frågesyntax](/rest/api/time-series-insights/ga-query-syntax) mer information om frågesyntaxen för Time Series Insights-dataåtkomst REST API.

- Lär dig [så formhändelser](./time-series-insights-send-events.md).
