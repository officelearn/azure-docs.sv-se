---
title: Så här formhändelser med Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Förstå hur du utformar händelser med Azure Time Series Insights (förhandsversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: edc1dac05a8ab4281eee3ee0eb4c5e6b7571b404
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856918"
---
# <a name="shaping-events-with-azure-time-series-insights-preview"></a>Forma händelser med Azure Time Series Insights (förhandsversion)

Den här artikeln innehåller vägledning för att forma JSON, för att maximera effektiviteten hos du är Azure Time Series Insights (förhandsversion)-frågor.

## <a name="best-practices"></a>Bästa praxis

> [!NOTE]
> Gränser för 600-800-egenskapen för S1/S2 gäller inte för Azure TSI (förhandsversion).

Det är viktigt att tänka på hur du skickar händelser till Azure TSI. Nämligen, bör du alltid:

1. Skicka data via nätverket så effektivt som möjligt.
1. Se till att dina data lagras på ett sätt som gör att du kan utföra aggregeringar som passar ditt scenario.

Följande riktlinjer hjälper dig att se till att den bästa möjliga frågeprestanda:

1. Skicka inte onödiga egenskaper. TSI (förhandsversion) faktureras du på din användning och det är en bra idé att lagra och bearbeta data som du frågar.
1. Använd instansfält för statiska data för att undvika att skicka statiska data över nätverket. Instansfält, en komponent i time series-modell som fungerar som refererar till data i allmänt tillgänglig TSI-tjänsten. Mer information om instansen fältet [Time Series modeller](./time-series-insights-update-tsm.md).
1. Dela dimensionsegenskaper bland flera händelser att skicka data mer effektivt över nätverket.
1. Använd inte djupgående matris kapsling. TSI stöder upp till två nivåer av kapslade matriser som innehåller objekt. TSI plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
1. Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Skicka dem separat minskar antalet händelser och kan bli frågor bättre eftersom färre händelser måste bearbetas.

## <a name="example"></a>Exempel

Exemplet baseras på ett scenario där flera enheter skicka mätning av faktisk användning eller signaler. Mätning av faktisk användning eller signaler kan vara **Flow Rate**, **Oljetryck**, **temperatur**, och **fuktighet**.

I följande exempel har ett enda meddelande på IoT Hub, där den yttersta matrisen innehåller ett delat avsnitt av vanliga dimensionsvärden. Yttre matrisen använder Time Series instansdata för att öka effektiviteten för meddelandet. Time Series-instans innehåller metadata om enheter, som inte ändras med varje händelse, men ger användbar egenskaper för dataanalys. Både batchbearbetning vanliga dimensionsvärden och använda metadata för Time Series-instans, sparar på byte som skickas över nätverket, vilket gör meddelandet mer effektivt.

Exempel-JSON-nyttolast:

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Time Series-instans (Obs: den **Time Series-ID** är *deviceId*):

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

TSI anslutna tabell (efter att förenkla) under fråga körs. Tabellen innehåller fler kolumner, till exempel typen. Det här exemplet visar hur du kan forma telemetridata:

| deviceId  | Typ | L1 | L2 | tidsstämpel | serie. Flow hastighet ft3/s | serie. Motorn olja tryck psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Batteri System | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SIMULATOR |    Batteri System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | VANLIGA LINE_DATA | SIMULATOR |    Batteri System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Observera följande i föregående exempel:

* Statiska egenskaper lagras som TSI att optimera data som skickas över nätverket.
* TSI-Data är ansluten när en fråga körs med hjälp av den *timeSeriesId* definitionen i instansen.
* Två nivåer av inkapsling används, vilket är den maximala mängden kapsling som stöds av TSD. Det är viktigt att undvika djupt kapslade matriser.
* Mått skickas som separata egenskaper inom samma objekt eftersom det finns några mått. Här kan **serien. Flow Rate psi**, serien **Oljetryck**, och **ft3/s** är unika kolumner.

>[!IMPORTANT]
> Instansfält inte lagras med telemetri, de lagras med metadata i den **Tidsseriemodell**.
> Tabellen ovan representerar frågevyn.

## <a name="next-steps"></a>Nästa steg

Om du vill placera dessa riktlinjer i praktiken, se [Azure TSI-frågesyntax](./time-series-insights-query-data-csharp.md) mer information om frågesyntaxen för TSI dataåtkomst REST API.
