---
title: Form händelser med Azure Time Series Insights för hands version | Microsoft Docs
description: Lär dig hur du formar händelser med Azure Time Series Insights för hands version.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: f8a50e062d2dac1f30f8b745f351570262daac53
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990899"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Form händelser med Azure Time Series Insights för hands version

Den här artikeln hjälper dig att forma din JSON-fil för att maximera effektiviteten hos dina Azure Time Series Insights för hands versioner.

## <a name="best-practices"></a>Bästa metoder

Tänk på hur du skickar händelser till Time Series Insights för hands versionen. Dvs. du bör alltid:

* Skicka data över nätverket så effektivt som möjligt.
* Lagra dina data på ett sätt som hjälper dig att aggregera det bättre för ditt scenario.

För bästa prestanda för frågor gör du följande:

* Skicka inte onödiga egenskaper. Time Series Insights för hands version debiteras du på din användning. Det är bäst att lagra och bearbeta de data som du kommer att fråga.
* Använd instans fält för statiska data. Den här metoden hjälper dig att undvika att skicka statiska data över nätverket. Instans fält, en komponent i tids serie modellen, fungerar som referens data i Time Series Insights tjänst som är allmänt tillgänglig. Mer information om instans fält finns i [tids serie modell](./time-series-insights-update-tsm.md).
* Dela dimensions egenskaper mellan två eller flera händelser. Den här metoden hjälper dig att skicka data över nätverket mer effektivt.
* Använd inte djup mat ris kapsling. Time Series Insights för hands versionen stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights för hands versionen fören klar matriser i meddelanden till flera händelser med egenskaps värde par.
* Om det bara finns några mått för alla eller de flesta händelser är det bättre att skicka dessa mått som separata egenskaper inom samma objekt. Att skicka dem separat minskar antalet händelser och kan förbättra frågans prestanda eftersom färre händelser behöver bearbetas.

## <a name="example"></a>Exempel

Följande exempel bygger på ett scenario där två eller fler enheter skickar mätningar eller signaler. Mätningarna eller signalerna kan vara *flödes takt*, *motor olje tryck*, *temperatur*och *fuktighet*.

I exemplet finns det ett enda Azure IoT Hub-meddelande där den yttre matrisen innehåller ett delat avsnitt av vanliga dimensions värden. Den yttre matrisen använder Time Series instance-data för att öka effektiviteten för meddelandet. 

Time Series-instansen innehåller metadata för enheten. Dessa metadata ändras inte med varje händelse, men den innehåller användbara egenskaper för data analys. Om du vill spara på byte som skickats över kabeln och göra meddelandet mer effektivt, kan du överväga att gruppera vanliga dimensions värden och använda Time Series instance metadata.

### <a name="example-json-payload"></a>Exempel på JSON-nyttolast

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

### <a name="time-series-instance"></a>Tids serie instans 
> [!NOTE]
> Time Series-ID: t är *deviceId*.

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

Time Series Insights för hands version ansluter till en tabell (efter förenkling) under fråge tiden. Tabellen innehåller ytterligare kolumner, till exempel **typ**. Följande exempel visar hur du kan [forma](./time-series-insights-send-events.md#supported-json-shapes) dina telemetridata.

| deviceId  | Typ | L1 | L2 | tidsstämpel | serien. Flödes frekvens ft3/s | serien. Motor Oil-tryck psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Batteri system | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULATOR |   Batteri system |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA COMMON | SIMULATOR |    Batteri system |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

Observera följande saker i föregående exempel:

* Statiska egenskaper lagras i Time Series Insights för hands versionen för att optimera data som skickas över nätverket.
* Time Series Insights för hands versions data kopplas vid tid med hjälp av Time Series-ID: t som definieras i instansen.
* Två kapslings nivåer används. Det här talet är det mest som Time Series Insights för hands versionen stöder. Det är viktigt att undvika djupt kapslade matriser.
* Eftersom det finns några mått skickas de som separata egenskaper inom samma objekt. I exemplet, **serien. PSI, serie för flödes frekvens** **. Motor olja-tryck PSI**och **serie. Flow Rate ft3/s** är unika kolumner.

>[!IMPORTANT]
> Instans fält lagras inte med telemetri. De lagras med metadata i tids serie modellen.
> Tabellen ovan representerar frågevyn.

## <a name="next-steps"></a>Nästa steg

- Om du vill använda dessa rikt linjer i övningen kan du läsa mer i [Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md)-frågesyntax. Du lär dig mer om frågesyntaxen för Time Series Insights för hands versionen REST API för data åtkomst.
- Mer information om JSON-former som stöds finns i [JSON-former som stöds](./time-series-insights-send-events.md#supported-json-shapes).
