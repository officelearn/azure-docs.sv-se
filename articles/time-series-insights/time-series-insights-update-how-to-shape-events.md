---
title: Time Series Insights förhandsversionen av Azure forma – händelser form med förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Förstå hur du utformar händelser förhandsversion av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b0ce3b5bbd58c594147f64de9f06f248f4a87fc3
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269081"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formhändelser med förhandsversionen av Azure Time Series Insights

Den här artikeln hjälper dig utforma din JSON-fil för att maximera effektiviteten hos dina Azure Time Series Insights Preview-frågor.

## <a name="best-practices"></a>Bästa praxis

Det är viktigt att tänka på hur du skickar händelser till Time Series Insights Preview. Nämligen, bör du alltid:

* Skicka data via nätverket så effektivt som möjligt.
* Store dina data på ett sätt som hjälper dig att samla mer lämpligt för ditt scenario.

För bästa möjliga frågeprestanda, gör du följande:

* Skicka inte onödiga egenskaper. Time Series Insights Preview debiteras du på din användning. Det är bäst att lagra och bearbeta data som får du fråga.
* Använd instans fälten för statiska data. Den här metoden kan du undvika att skicka statiska data över nätverket. Instansfält, en komponent i time series-modell som fungerar som refererar till data i Time Series Insights GA-tjänsten. Läs mer om Instansfält i [Time Series modeller](./time-series-insights-update-tsm.md).
* Dela dimensionsegenskaper mellan två eller flera händelser. Den här metoden kan du skicka data mer effektivt över nätverket.
* Använd inte djupgående matris kapsling. Förhandsversionen av Time Series Insights har stöd för upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights Preview plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
* Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Skicka dem separat minskar antalet händelser och eftersom färre händelser måste bearbetas, övningen kan göra att frågor bättre.

## <a name="example"></a>Exempel

I följande exempel baseras på ett scenario där två eller flera enheter skicka mätning av faktisk användning eller signaler. Mätning av faktisk användning eller signaler kan vara *Flow Rate*, *Oljetryck*, *temperatur*, och *fuktighet*.

I följande exempel har ett enda meddelande på IoT Hub, där den yttersta matrisen innehåller ett delat avsnitt av vanliga dimensionsvärden. Yttre matrisen använder Time Series instansdata för att öka effektiviteten för meddelandet. Time Series-instans innehåller metadata om enheter, som inte ändras med varje händelse, men den ger användbara egenskaper för dataanalys. Överväg att batchbearbetning vanliga dimensionsvärden och använda Time Series Instance metadata för att spara på byte som skickas över nätverket och gör budskapet mer effektivt.

### <a name="example-json-payload"></a>Exempel-JSON-nyttolast

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

### <a name="time-series-instance"></a>Time Series-instans 
> [!NOTE]
> Time Series-ID är *deviceId*.

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

Förhandsversionen av Time Series Insights ansluter till en tabell (efter att förenkla) under fråga körs. Tabellen innehåller fler kolumner som **typ**. I följande exempel visar hur du kan [form](./time-series-insights-send-events.md#json) telemetridata:

| deviceId  | Typ | L1 | L2 | tidsstämpel | serie. Flow hastighet ft3/s | serie. Motorn olja tryck psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Batteri System | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SIMULATOR |    Batteri System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | VANLIGA LINE_DATA | SIMULATOR |    Batteri System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Observera följande punkter i föregående exempel:

* Statiska egenskaper lagras i Time Series Insights Preview att optimera data som skickas över nätverket.
* Time Series Insights Preview-data är ansluten när en fråga körs med hjälp av den *timeSeriesId* som definieras i instansen.
* Två nivåer av inkapsling används, vilket är de som stöds av Time Series Insights Preview. Det är viktigt att undvika djupt kapslade matriser.
* Eftersom det inte finns några mått, skickas de som separata egenskaper inom samma objekt. I det här exemplet **serien. Flow Rate psi**, **serien. Motorn olja tryck psi**, och **serien. Flow hastighet ft3/s** är unika kolumner.

>[!IMPORTANT]
> Instansfält lagras inte med telemetri. De lagras med metadata i den **Tidsseriemodell**.
> I föregående tabell representerar frågevyn.

## <a name="next-steps"></a>Nästa steg

Om du vill placera dessa riktlinjer i praktiken, se [förhandsversion av Azure Time Series Insights-frågesyntax](./time-series-insights-query-data-csharp.md). Du får lära dig mer om frågesyntax för Time Series Insights Preview data åt REST API.

Mer information om JSON-former som stöds, se [stöds JSON-former](./time-series-insights-send-events.md#json).
