---
title: Metodtips för att forma frågor om JSON – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du förbättrar frågeeffektiviteten i Azure Time Series Insights genom att forma JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8232fd4c2a1e17800c96854b4ba7298e57ed84b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989900"
---
# <a name="shape-json-to-maximize-query-performance"></a>Forma JSON för att maximera frågeprestanda 

Den här artikeln innehåller vägledning om hur du utformar JSON för att maximera effektiviteten i dina Azure Time Series Insights-frågor.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Lär dig metodtips för att forma JSON för att uppfylla dina lagringsbehov.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Bästa praxis

Fundera på hur du skickar händelser till Time Series Insights. Nämligen, du alltid:

1. Skicka data över nätverket så effektivt som möjligt.
1. Kontrollera att dina data lagras på ett sätt så att du kan utföra aggregeringar som passar ditt scenario.
1. Se till att du inte når maxgränserna för Time Series Insights för:
   - 600 egenskaper (kolumner) för S1-miljöer.
   - 800 egenskaper (kolumner) för S2-miljöer.

> [!TIP]
> Granska [gränser och planering](time-series-insights-update-plan.md) i förhandsversionen av Azure Time Series Insights.

Följande vägledning hjälper till att säkerställa bästa möjliga frågeprestanda:

1. Använd inte dynamiska egenskaper, till exempel ett tagg-ID, som ett egenskapsnamn. Den här användningen bidrar till att nå den maximala egenskapsgränsen.
1. Skicka inte onödiga egenskaper. Om en frågeegenskap inte krävs är det bäst att inte skicka den. På så sätt undviker du lagringsbegränsningar.
1. Använd [referensdata](time-series-insights-add-reference-data-set.md) för att undvika att skicka statiska data över nätverket.
1. Dela dimensionsegenskaper mellan flera händelser för att skicka data över nätverket mer effektivt.
1. Använd inte djup arraykapsling. Time Series Insights stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights plattar matriser i meddelandena till flera händelser med egenskapsvärdepar.
1. Om det bara finns ett fåtal mått för alla eller de flesta händelser är det bättre att skicka dessa mått som separata egenskaper inom samma objekt. Om du skickar dem separat minskar antalet händelser och kan frågeprestanda förbättras eftersom färre händelser behöver bearbetas. När det finns flera mått, skicka dem som värden i en enda egenskap minimerar möjligheten att nå den maximala egenskapen gränsen.

## <a name="example-overview"></a>Exempel översikt

Följande två exempel visar hur du skickar händelser för att markera de tidigare rekommendationerna. I varje exempel kan du granska hur rekommendationerna tillämpades.

Exemplen baseras på ett scenario där flera enheter skickar mätningar eller signaler. Mätningar eller signaler kan vara flödeshastighet, motoroljetryck, temperatur och luftfuktighet. I det första exemplet finns det några mätningar på alla enheter. Det andra exemplet har många enheter och varje enhet skickar många unika mätningar.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario ett: Endast ett fåtal mätningar finns

> [!TIP]
> Vi rekommenderar att du skickar varje mätning eller signal som en separat egenskap eller kolumn.

I följande exempel finns det ett enda Azure IoT Hub-meddelande där den yttre matrisen innehåller ett delat avsnitt med gemensamma dimensionsvärden. Den yttre matrisen använder referensdata för att öka meddelandets effektivitet. Referensdata innehåller enhetsmetadata som inte ändras med varje händelse, men det ger användbara egenskaper för dataanalys. Batching gemensamma dimensionsvärden och använda referensdata sparar på byte som skickas över tråden, vilket gör meddelandet mer effektivt.

Tänk på följande JSON-nyttolast som skickas till ga-miljön i Time Series Insights med hjälp av ett [IoT Device Message-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) som serialiseras till JSON när det skickas till Azure-molnet:


```JSON
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

* Referensdatatabell som har **deviceId**nyckelegenskapenId:

   | deviceId | messageId | deviceLocation (deviceLocation) |
   | --- | --- | --- |
   | FXXX (OLIKA) | LINJEDATA\_ | EU |
   | FYYY (FYYY) | LINJEDATA\_ | USA |

* Händelsetabellen Time Series Insights efter förenkling:

   | deviceId | messageId | deviceLocation (deviceLocation) | timestamp | Serien. Flödeshastighet ft3/s | Serien. Motor oljetryck psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX (OLIKA) | LINJEDATA\_ | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX (OLIKA) | LINJEDATA\_ | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY (FYYY) | LINJEDATA\_ | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **DeviceId-kolumnen** fungerar som kolumnrubrik för de olika enheterna i en vagnpark. Att göra **deviceId-värdet** till sitt eget egenskapsnamn begränsar det totala antalet enheter till 595 (för S1-miljöer) eller 795 (för S2-miljöer) med de andra fem kolumnerna.
> - Onödiga egenskaper undviks (till exempel märkes- och modellinformation). Eftersom egenskaperna inte efterfrågas i framtiden, möjliggör eliminering av dem bättre nätverks- och lagringseffektivitet.
> - Referensdata används för att minska antalet byte som överförs via nätverket. De två **attributen messageId** och **deviceLocation** sammanfogas med hjälp av nyckelgenskapen **deviceId**. Dessa data sammanfogas med telemetridata vid ingående tid och lagras sedan i Time Series Insights för frågor.
> - Två lager kapsling används, vilket är den maximala mängden kapsling som stöds av Time Series Insights. Det är viktigt att undvika djupt kapslade matriser.
> - Åtgärder skickas som separata egenskaper inom samma objekt eftersom det finns få mått. Här, **serien. Flödeshastighet psi** och **serie. Motoroljetryck ft3/s** är unika kolumner.

## <a name="scenario-two-several-measures-exist"></a>Scenario två: Det finns flera åtgärder

> [!TIP]
> Vi rekommenderar att du skickar mått som "typ", "enhet" och "värde" tupplar.

Exempel på JSON-nyttolast:

```JSON
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

* Referensdatatabell som har nyckeln egenskaper **deviceId** och **series.tagId:**

   | deviceId | series.tagId | messageId | deviceLocation (deviceLocation) | typ | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX (OLIKA) | pumpRate | LINJEDATA\_ | EU | Flöde | ft3/s |
   | FXXX (OLIKA) | oljaPressur | LINJEDATA\_ | EU | Motorns oljetryck | psi |
   | FYYY (FYYY) | pumpRate | LINJEDATA\_ | USA | Flöde | ft3/s |
   | FYYY (FYYY) | oljaPressur | LINJEDATA\_ | USA | Motorns oljetryck | psi |

* Händelsetabellen Time Series Insights efter förenkling:

   | deviceId | series.tagId | messageId | deviceLocation (deviceLocation) | typ | unit | timestamp | serie.värde |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX (OLIKA) | pumpRate | LINJEDATA\_ | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX (OLIKA) | oljaPressur | LINJEDATA\_ | EU | Motorns oljetryck | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX (OLIKA) | pumpRate | LINJEDATA\_ | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX (OLIKA) | oljaPressur | LINJEDATA\_ | EU | Motorns oljetryck | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY (FYYY) | pumpRate | LINJEDATA\_ | USA | Flöde | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY (FYYY) | oljaPressur | LINJEDATA\_ | USA | Motorns oljetryck | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Kolumnerna **deviceId** och **series.tagId** fungerar som kolumnrubriker för de olika enheterna och taggarna i en vagnpark. Om du använder varje som sitt eget attribut begränsas frågan till 594 (för S1-miljöer) eller 794 (för S2-miljöer) totalt enheter med de andra sex kolumnerna.
> - Onödiga egenskaper undveks, av den anledning som nämns i det första exemplet.
> - Referensdata används för att minska antalet byte som överförs via nätverket genom att introducera **deviceId**, som används för det unika paret **messageId** och **deviceLocation**. Den sammansatta **nyckelserien.tagId** används för det unika paret av **typ** och **enhet**. Med den sammansatta nyckeln kan **deviceId-** och **series.tagId-par** användas för att referera till fyra värden: **messageId, deviceLocation, type** och **unit**. Dessa data är kopplade till telemetridata vid ingående tid. Den lagras sedan i Time Series Insights för frågor.
> - Två lager häckning används, av den anledning som anges i det första exemplet.

### <a name="for-both-scenarios"></a>För båda scenarierna

För en egenskap med ett stort antal möjliga värden är det bäst att skicka som distinkta värden i en enda kolumn i stället för att skapa en ny kolumn för varje värde. Från de två föregående exemplen:

  - I det första exemplet har några egenskaper flera värden, så det är lämpligt att göra var och en till en separat egenskap.
  - I det andra exemplet anges inte måtten som enskilda egenskaper. I stället är de en rad värden eller mått under en gemensam serieegenskap. Den nya **nyckeltaggenId** skickas, vilket skapar den nya **kolumnserien.tagId** i den förenklade tabellen. Den nya **egenskapstypen** och **enheten** skapas med hjälp av referensdata så att egenskapsgränsen inte nås.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skickar [IoT Hub-enhetsmeddelanden till molnet](../iot-hub/iot-hub-devguide-messages-construct.md).

- Läs [frågasyntaxen för Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) om du vill veta mer om frågesyntaxen för tidsseriestatistikdataåtkomst REST API.

- Läs om hur du [utformar händelser](./time-series-insights-send-events.md).
